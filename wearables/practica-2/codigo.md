---
layout: default
title: Algoritmo y Firmware
parent: P2 - SonicGauntlet
grand_parent: Wearables
nav_order: 2
---

# Ingeniería de Software y DSP
{: .fs-9 }

El firmware del **SonicGauntlet** no es una simple secuencia de luces; es un sistema de **Procesamiento Digital de Señales (DSP)** en tiempo real.

El objetivo del código es convertir una señal de voltaje caótica (música) en una visualización ordenada y agradable, filtrando el ruido eléctrico electromagnético (EMI) propio de los *wearables* y adaptándose dinámicamente al volumen de la fuente.

---

## 1. Arquitectura del Sistema

El flujo de datos sigue una tubería (pipeline) estricta para asegurar que la visualización sea sincrónica con el oído humano.

`Señal Analógica (ADC) → Pre-Procesamiento (Deadband) → FFT → Agrupación (Binning) → Suavizado (AGC) → Lógica de Confirmación → Visualización`

### Muestreo Síncrono y Aislamiento (Sampling)
Para que las matemáticas funcionen, el microcontrolador debe muestrear la señal a intervalos exactos con la menor interferencia posible.
* **Aislamiento de Radiofrecuencia:** Se desactiva el radio WiFi interno del ESP32 (`WiFi.mode(WIFI_OFF)`) durante el arranque para evitar que sus propias transmisiones inyecten ruido parásito en el ADC.
* **Frecuencia de Muestreo:** 20,000 Hz (20kHz).
* **Teorema de Nyquist:** Al muestrear a 20kHz, el sistema analiza frecuencias de audio de hasta **10kHz**, cubriendo el espectro fundamental de la música.
* **Resolución:** Se utiliza el ADC de 12-bits del ESP32, ofreciendo un rango de valores de 0 a 4095.

---

## 2. Algoritmos de Filtrado y Estabilización

La electrónica textil introduce variables complejas: módulos Bluetooth que generan pulsos de radio, caídas de tensión por hilos resistivos y ruido estático. El software compensa estas deficiencias de hardware mediante tres capas de seguridad:

### A. Auto-Calibración (Zero-Calibration)
Las baterías de litio (LiPo/Li-ion) cambian su voltaje a medida que se descargan. Un valor central fijo de referencia provocaría errores en la amplitud de la onda.
* **Solución:** Al encenderse y tras dar tiempo al Bluetooth de arrancar, el sistema mide el "silencio eléctrico" durante 400ms.
* **Resultado:** Calcula el **DC Offset** exacto de ese momento y lo usa como el centro matemático (Cero) para la sesión.

### B. Filtro de Zona Muerta (Deadband)
El módulo MHM-28 y el sistema de alimentación generan un ligero "rizado" (ripple) continuo, incluso en absoluto silencio.
* **Implementación:** Se recorta cualquier variación analógica menor al umbral `deadband` (calibrado a 30 unidades gracias a la limpieza de la corriente continua de la batería).
* **Resultado:** El ruido blanco es aplastado a cero absoluto antes de entrar a la transformada de Fourier, evitando falsos positivos de baja amplitud.

### C. Filtro Anti-Transitorios (Debounce Analógico)
El módulo Bluetooth emite picos electromagnéticos ultracortos al comunicarse con el teléfono, que la FFT puede interpretar erróneamente como música a un volumen masivo.
* **Implementación:** Se diseñó un contador de validación (`validAudioFrames`). Para que el sistema reaccione y despierte los LEDs, debe detectar energía superior al umbral maestro durante **al menos 2 ciclos consecutivos**.
* **Resultado:** Los "chispazos" eléctricos de 1 ciclo de duración son ignorados sistemáticamente, blindando la animación de *Standby*.

---

## 3. Transformada Rápida de Fourier (FFT)

El núcleo matemático del proyecto. La librería `arduinoFFT` toma 64 muestras de tiempo limpias y las descompone en sus frecuencias constitutivas.

### Binning (Agrupación de Bandas)
La FFT entrega 32 "cubetas" (bins) de frecuencias. Para los 5 canales de salida, se agrupan en rangos perceptuales:

| Canal (LED) | Rango de Frecuencia | Instrumento Típico |
| :--- | :--- | :--- |
| **1. Graves** | 40Hz - 150Hz | Bombo (Kick), Sub-bajo |
| **2. Bajos** | 150Hz - 400Hz | Bajo eléctrico, Cello |
| **3. Medios** | 400Hz - 1.5kHz | Guitarra, Voces masculinas |
| **4. Voces** | 1.5kHz - 4kHz | Voces femeninas, Sintetizadores |
| **5. Agudos** | 4kHz - 10kHz | Platillos (Hi-Hats), Shakers |

---

## 4. Control de Ganancia Automático (AGC)

El sistema adapta su sensibilidad de forma activa para reaccionar a la dinámica de diferentes géneros musicales.

### Beat Detection (Detección de Ritmo)
* **Promedio Móvil:** El sistema recuerda el volumen promedio de las iteraciones recientes (`runningAverage`).
* **Compuerta de Ruido (Noise Gate):** Antes de multiplicar, la banda debe superar un nivel mínimo de energía para asegurar que es un sonido real y no artefactos matemáticos.
* **Disparador:** El volumen actual debe superar la ganancia histórica multiplicada por un factor de sensibilidad (`multipliers`). Los graves requieren un multiplicador mayor (2.5) debido a su mayor acumulación de energía en la FFT.
* **Ataque y Decaimiento (Fast Attack / Slow Decay):**
  * Al detectar un golpe, el promedio sube rápidamente (40% del nuevo valor).
  * En el silencio, el promedio decae suavemente multiplicándose por 0.99, dando una transición visual fluida y natural.

---

## 5. Máquina de Estados

El dispositivo tiene dos estados lógicos principales gestionados por temporizadores no bloqueantes (`millis()`):

1.  **Estado ACTIVO (Music Mode):**
    * Se activa cuando la energía validada supera el `masterThreshold`.
    * Los LEDs responden a la FFT en tiempo real con latencia casi nula.
2.  **Estado STANDBY (Idle Mode):**
    * Se activa tras 3 segundos (`silenceDelay`) de silencio absoluto.
    * Ejecuta una animación de escáner de bajo consumo, confirmando al usuario que el sistema sigue encendido y midiendo, pero a la espera de un estímulo.

---

## Firmware

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

[Ver Hardware](../hardware.md){: .btn .btn-outline } [Ver Proceso](../proceso.md){: .btn .btn-outline } [Ver Diseño](../diseno.md){: .btn .btn-outline } [Inicio](../practica-2){: .btn .btn-primary } 