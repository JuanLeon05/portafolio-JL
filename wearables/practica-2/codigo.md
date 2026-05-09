---
layout: default
title: Algoritmo y Firmware
parent: P2 - SonicGauntlet
grand_parent: Wearables
nav_order: 3
---

# Ingeniería de Software y DSP
{: .fs-9 }

El firmware del **SonicGauntlet** es un sistema de Procesamiento Digital de Señales (DSP) en tiempo real. El objetivo es convertir la música en una visualización ordenada, operando en un bucle continuo (loop) de análisis y respuesta visual.

---

## 1. Arquitectura del Sistema y Secuencia de Inicio

El flujo de datos sigue una tubería estricta para asegurar que la visualización sea sincrónica. Al conectar la fuente de poder (simulando el cierre o abrochado de la prenda), el microcontrolador inicia su rutina de calibración y arranca la secuencia en loop.

`Señal (ADC) → Pre-Procesamiento → Transformada (FFT) → Agrupación → Visualización`

### Muestreo y Aislamiento
* **Aislamiento de Radiofrecuencia:** Se desactiva el radio WiFi interno (`WiFi.mode(WIFI_OFF)`) para evitar que sus transmisiones inyecten ruido parásito.
* **Frecuencia de Muestreo:** 20,000 Hz, permitiendo analizar frecuencias de audio de hasta 10kHz (Teorema de Nyquist).

## 2. Filtros de Estabilización

La electrónica textil introduce variables complejas que el software compensa:
1. **Auto-Calibración:** Al encenderse, el sistema mide el "silencio eléctrico" para calcular el centro matemático (Cero) exacto.
2. **Filtro Deadband:** Recorta variaciones analógicas menores al umbral calibrado, eliminando el ruido de fondo.
3. **Debounce Analógico:** Para que los LEDs despierten de su secuencia de espera, la energía debe superar el umbral durante al menos 2 ciclos, ignorando "chispazos" eléctricos pasajeros.

## 3. Transformada de Fourier (FFT) y Agrupación
La librería toma muestras limpias y las descompone en 32 "cubetas", que luego se agrupan en 5 canales para los LEDs (Graves, Bajos, Medios, Voces, Agudos).

<img src="{{ site.baseurl }}/assets/img/practica-2/Microcontrolador.jpeg" alt="Microcontrolador" width="49%">
<img src="{{ site.baseurl }}/assets/img/practica-2/Microcontrolador2.jpeg" alt="Microcontrolador Detalle" width="49%">

```cpp
/*
 * SONIC GAUNTLET - Firmware v4.9 
 * Hardware: ESP32-C3 Super Mini + MHM-28
 * Características: DSP avanzado, Anti-Transitorios, Auto-Gain, WiFi OFF.
 */

#include <WiFi.h> 
#include "arduinoFFT.h"

// --- CONFIGURACIÓN DE PINES (ESP32-C3 Super Mini) ---
const int audioPin = 0;  // GPIO 0 (ADC1_CH0)
const int ledPins[5] = {4, 3, 7, 6, 5}; 

// --- CONFIGURACIÓN FFT ---
const uint16_t samples = 64;           
const double samplingFrequency = 20000; 

unsigned int sampling_period_us;
unsigned long microseconds;
double vReal[samples];
double vImag[samples];

ArduinoFFT<double> FFT = ArduinoFFT<double>(vReal, vImag, samples, samplingFrequency);

// --- AUTO-CALIBRACIÓN ---
int dcOffset = 0; 

// --- VARIABLES DE AUTO-GAIN ---
double runningAverage[5] = {300, 300, 300, 300, 300}; 
float multipliers[5] = {2.5, 2.3, 2.0, 1.8, 1.8};     

// --- UMBRALES DE RUIDO (Calibrados para Batería DC) ---
int noiseGate = 150;       // Nivel individual para ignorar artefactos FFT
int deadband = 30;        // Filtro de ruido base (Ripple de la fuente)
int masterThreshold = 800; // Nivel maestro para despertar el guantelete

// --- VARIABLES STANDBY ---
unsigned long lastAudioTime = 0;   
const int silenceDelay = 3000;     
bool isStandby = false;            

int validAudioFrames = 0;

int scannerPos = 0;
int scannerDir = 1;
unsigned long lastScannerUpdate = 0;

void setup() {
  Serial.begin(115200);
  delay(2000); 

  // Aislamiento RF interno
  WiFi.mode(WIFI_OFF); 
  analogReadResolution(12); 
  
  for (int i = 0; i < 5; i++) {
    pinMode(ledPins[i], OUTPUT);
    digitalWrite(ledPins[i], LOW);
  }

  sampling_period_us = round(1000000 * (1.0 / samplingFrequency));

  // --- AUTO-CALIBRACIÓN ---
  delay(3000); 
  long calibrationSum = 0;
  for(int i = 0; i < 200; i++) {
      calibrationSum += analogRead(audioPin);
      delay(2);
  }
  dcOffset = calibrationSum / 200; 
  
  secuenciaArranque();
}

void loop() {
  // 1. MUESTREO DE SEÑAL
  microseconds = micros();
  for (int i = 0; i < samples; i++) {
    int rawValue = analogRead(audioPin);
    
    // Filtro Deadband: Elimina ruido de fondo y ripple
    if (abs(rawValue - dcOffset) < deadband) { 
        rawValue = dcOffset; 
    }
    
    vReal[i] = rawValue - dcOffset; 
    vImag[i] = 0;
    while (micros() - microseconds < sampling_period_us) { }
    microseconds += sampling_period_us;
  }
  
  // 2. PROCESAMIENTO FFT
  FFT.windowing(FFTWindow::Hamming, FFTDirection::Forward);
  FFT.compute(FFTDirection::Forward);
  FFT.complexToMagnitude();

  // 3. AGRUPACIÓN DE BANDAS
  double bandValues[5] = {0};
  double totalVolume = 0;
  
  for (int i = 1; i < 3; i++) bandValues[0] += vReal[i];   
  for (int i = 3; i < 5; i++) bandValues[1] += vReal[i];   
  for (int i = 5; i < 9; i++) bandValues[2] += vReal[i];   
  for (int i = 9; i < 15; i++) bandValues[3] += vReal[i];  
  for (int i = 15; i < 24; i++) bandValues[4] += vReal[i]; 

  for(int i = 0; i < 5; i++) totalVolume += bandValues[i];

  // --- LÓGICA DE DECISIÓN ---
  if (totalVolume > masterThreshold) { 
    validAudioFrames++; 
    
    // Filtro Anti-Transitorios: Exige confirmación
    if (validAudioFrames >= 2) { 
        lastAudioTime = millis();
        isStandby = false;        
        
        for (int i = 0; i < 5; i++) {
          bool beatDetected = (bandValues[i] > noiseGate) && 
                              (bandValues[i] > (runningAverage[i] * multipliers[i]));
    
          if (beatDetected) {
            digitalWrite(ledPins[i], HIGH);
            runningAverage[i] = (runningAverage[i] * 0.6) + (bandValues[i] * 0.4); 
          } else {
            digitalWrite(ledPins[i], LOW);
            runningAverage[i] = runningAverage[i] * 0.99; 
          }
          
          if (runningAverage[i] < (noiseGate + 50)) runningAverage[i] = (noiseGate + 50);
        }
    }
  } 
  else { // MODO SILENCIO
    validAudioFrames = 0; 
    
    if (!isStandby && (millis() - lastAudioTime < silenceDelay)) {
       for(int i = 0; i < 5; i++) digitalWrite(ledPins[i], LOW);
    }

    if (millis() - lastAudioTime > silenceDelay) {
      isStandby = true;
      animacionStandby(); 
    }
  }
}  

// --- SECUENCIAS AUXILIARES ---
void secuenciaArranque() {
  for(int k = 0; k < 2; k++) {
    for (int i = 0; i < 5; i++) digitalWrite(ledPins[i], HIGH);
    delay(100);
    for (int i = 0; i < 5; i++) digitalWrite(ledPins[i], LOW);
    delay(100);
  }
}

void animacionStandby() {
  if (millis() - lastScannerUpdate > 120) { 
    lastScannerUpdate = millis();
    for(int i = 0; i < 5; i++) digitalWrite(ledPins[i], LOW);
    digitalWrite(ledPins[scannerPos], HIGH);
    scannerPos += scannerDir;
    if (scannerPos >= 4 || scannerPos <= 0) scannerDir = -scannerDir; 
  }
}
```


---

[Ver Hardware](./hardware.md){: .btn .btn-outline .mr-2 } [Ver Diseño](./diseno.md){: .btn .btn-outline .mr-2 } [Inicio](../practica-2){: .btn .btn-primary }