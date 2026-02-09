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

El objetivo del código es convertir una señal de voltaje caótica (música) en una visualización ordenada y agradable, filtrando el ruido eléctrico y adaptándose dinámicamente al volumen de la fuente.

---

## 1. Arquitectura del Sistema

El flujo de datos sigue una tubería (pipeline) estricta para asegurar que la visualización sea sincrónica con el oído humano.

`Señal Analógica (ADC) → Pre-Procesamiento (Offset) → FFT → Agrupación (Binning) → Suavizado (AGC) → Visualización`

### Muestreo Síncrono (Sampling)
Para que la matemáticas funcionen, el microcontrolador no puede leer "cuando pueda". Debe leer a intervalos exactos.
* **Frecuencia de Muestreo:** 20,000 Hz (20kHz).
* **Teorema de Nyquist:** Al muestrear a 20kHz, podemos analizar frecuencias de audio de hasta **10kHz**, lo cual cubre perfectamente el rango fundamental de la música (Bombos, Bajos, Voces y Platillos).
* **Resolución:** Se utiliza el ADC de 12-bits del ESP32, ofreciendo un rango de valores de 0 a 4095 (4 veces más preciso que un Arduino Uno).

---

## 2. Algoritmos de Filtrado

Uno de los mayores retos en wearables es el ruido eléctrico, ya que los motores, baterías y módulos Bluetooth comparten la misma tierra.

### Auto-Calibración (Zero-Calibration)
Las baterías LiPo cambian su voltaje a medida que se descargan (de 4.2V a 3.7V). Un valor "fijo" de referencia provocaría errores.
* **Solución:** Al encenderse, el sistema ejecuta una rutina de arranque que mide el "silencio eléctrico" durante 500ms.
* **Resultado:** Calcula el **DC Offset** exacto de ese momento y lo usa como el "Cero" matemático para el resto de la sesión.

### Noise Gate (Compuerta de Ruido)
El módulo Bluetooth genera un silbido de alta frecuencia ("Hiss") cuando no reproduce música.
* **Implementación:** Se aplica una compuerta lógica. Si la energía de una frecuencia no supera un umbral mínimo (`noiseGate`), se considera "basura" y se fuerza a cero.
* **Corte de Frecuencia:** En el código, ignoramos deliberadamente los últimos "bins" de la FFT (frecuencias ultra-altas) donde reside el ruido del reloj del procesador Bluetooth.

---

## 3. Transformada Rápida de Fourier (FFT)

El núcleo matemático del proyecto. La librería `arduinoFFT` toma 64 muestras de tiempo y las descompone en sus frecuencias constitutivas.

### Binning (Agrupación de Bandas)
La FFT nos entrega 32 "cubetas" (bins) de frecuencias lineales. Para los 5 LEDs, agrupamos estas cubetas en rangos perceptuales:

| Canal (LED) | Rango de Frecuencia | Instrumento Típico |
| :--- | :--- | :--- |
| **1. Graves** | 40Hz - 150Hz | Bombo (Kick), Sub-bajo |
| **2. Bajos** | 150Hz - 400Hz | Bajo eléctrico, Cello |
| **3. Medios** | 400Hz - 1.5kHz | Guitarra, Voces masculinas |
| **4. Voces** | 1.5kHz - 4kHz | Voces femeninas, Sintetizadores |
| **5. Agudos** | 4kHz - 10kHz | Platillos (Hi-Hats), Shakers |

---

## 4. Control de Ganancia Automático (AGC)

El problema de los vúmetros tradicionales es que con volumen bajo no prenden, y con volumen alto se saturan (quedan siempre prendidos).

### Beat Detection (Detección de Ritmo)
Implementé un algoritmo comparativo dinámico:
1.  **Promedio Móvil:** El sistema recuerda el volumen promedio de los últimos segundos (`runningAverage`).
2.  **Disparador:** Para encender un LED, el sonido actual debe ser **X veces más fuerte** que el promedio histórico.
    * *Ejemplo:* Para los agudos, el sonido debe ser 1.2 veces el promedio.
3.  **Ataque y Decaimiento:**
    * Si hay un golpe (Beat), el promedio sube rápido (*Fast Attack*) para adaptarse a la canción.
    * Si hay silencio, el promedio baja lentamente (*Slow Decay*).

> **Resultado:** El guante "baila" igual de bien con una balada suave al 30% de volumen que con Metal Industrial al 100%.

---

## 5. Máquina de Estados

El dispositivo tiene dos personalidades para ahorrar energía y dar feedback visual.

1.  **Estado ACTIVO (Music Mode):**
    * Se activa cuando la suma total de energía supera el `Master Threshold`.
    * Los LEDs responden a la FFT en tiempo real.
2.  **Estado STANDBY (Idle Mode):**
    * Se activa tras 3 segundos de silencio absoluto.
    * Ejecuta una animación tipo "Scanner" (Auto Fantástico) de bajo consumo.
    * Sirve como indicador de "Encendido" para que el usuario no olvide apagar el guante.

---

## Firmware

```cpp
/*
 * SONIC GAUNTLET - Firmware v4.4 (Balanced Edition)
 * Hardware: Seeed Studio XIAO ESP32S3 + MHM-28
 * Ingeniería: Juan León
 * Descripción: Sistema DSP con Auto-Calibración y Filtro de Ruido Bluetooth.
 */

#include "arduinoFFT.h"

// --- CONFIGURACIÓN DE PINES ---
const int audioPin = A0; // Entrada Analógica (GPIO 1)
// Mapeo físico de LEDs en el guante (De muñeca a dedo índice)
const int ledPins[5] = {D1, D2, D3, D4, D5}; 

// --- CONFIGURACIÓN FFT ---
const uint16_t samples = 64;            // Tamaño de ventana (Potencia de 2)
const double samplingFrequency = 20000; // 20kHz de Muestreo (Nyquist = 10kHz Audio)

unsigned int sampling_period_us;
unsigned long microseconds;
double vReal[samples]; // Vector Real para FFT
double vImag[samples]; // Vector Imaginario para FFT

// Objeto de procesamiento FFT
ArduinoFFT<double> FFT = ArduinoFFT<double>(vReal, vImag, samples, samplingFrequency);

// --- AUTO-CALIBRACIÓN ---
int dcOffset = 0; // Variable para guardar el "Silencio" (Voltaje Bias)

// --- VARIABLES DE AUTO-GAIN (AGC) ---
// Promedio histórico inicial (se adapta solo)
double runningAverage[5] = {300, 300, 300, 300, 300}; 

// Multiplicadores de Sensibilidad [Graves ... Agudos]
// 1.2 significa que el sonido debe ser 20% más fuerte que el promedio para encender
float multipliers[5] = {1.2, 1.2, 1.2, 1.1, 1.1};     

// Noise Gate (Compuerta de Ruido)
// Valor 250: Suficiente para ignorar estática, sensible para música suave.
int noiseGate = 250; 

// --- VARIABLES STANDBY ---
unsigned long lastAudioTime = 0;   
const int silenceDelay = 3000;     // 3 segundos para ir a dormir
bool isStandby = false;            

// Variables para animación "Scanner"
int scannerPos = 0;
int scannerDir = 1;
unsigned long lastScannerUpdate = 0;

void setup() {
  // Configuración del ADC del ESP32 (Resolución 0 - 4095)
  analogReadResolution(12); 
  
  for (int i = 0; i < 5; i++) {
    pinMode(ledPins[i], OUTPUT);
    digitalWrite(ledPins[i], LOW);
  }

  // Calculamos el tiempo exacto entre lecturas para lograr 20kHz
  sampling_period_us = round(1000000 * (1.0 / samplingFrequency));

  // --- RUTINA DE AUTO-CALIBRACIÓN ---
  // Leemos el sensor 200 veces para encontrar el punto cero exacto de la batería
  long calibrationSum = 0;
  for(int i=0; i<200; i++) {
     calibrationSum += analogRead(audioPin);
     delay(2);
  }
  dcOffset = calibrationSum / 200; 
  
  // Ejecutar animación de bienvenida
  secuenciaArranque();
}

void loop() {
  
  // ============================================================
  // 1. MUESTREO DE SEÑAL (Sampling)
  // ============================================================
  microseconds = micros();
  for (int i = 0; i < samples; i++) {
    int rawValue = analogRead(audioPin);
    
    // Resta dinámica: Eliminamos el voltaje DC de la batería para dejar solo la onda de audio
    vReal[i] = rawValue - dcOffset; 
    vImag[i] = 0;
    
    // Espera activa para mantener sincronía estricta de 20kHz
    while (micros() - microseconds < sampling_period_us) { }
    microseconds += sampling_period_us;
  }

  // ============================================================
  // 2. PROCESAMIENTO MATEMÁTICO (FFT)
  // ============================================================
  FFT.windowing(FFTWindow::Hamming, FFTDirection::Forward); // Suavizado de ventana
  FFT.compute(FFTDirection::Forward);                       // Transformada
  FFT.complexToMagnitude();                                 // Obtener magnitud absoluta

  // ============================================================
  // 3. AGRUPACIÓN DE BANDAS (Binning)
  // ============================================================
  double bandValues[5] = {0};
  double totalVolume = 0;
  
  // Mapeo de los bins de la FFT a los 5 LEDs
  // Saltamos los primeros 3 bins para evitar ruido DC residual
  for (int i = 3; i < 5; i++) bandValues[0] += vReal[i];   // Graves (Kick)
  for (int i = 5; i < 8; i++) bandValues[1] += vReal[i];   // Bajos (Bass)
  for (int i = 8; i < 12; i++) bandValues[2] += vReal[i];  // Medios (Guitar)
  for (int i = 12; i < 18; i++) bandValues[3] += vReal[i]; // Voces
  
  // FILTRO PASA-BAJAS DE SOFTWARE:
  // Solo leemos hasta el bin 24. Del 25 en adelante vive el ruido eléctrico del Bluetooth.
  for (int i = 18; i < 24; i++) bandValues[4] += vReal[i]; // Agudos (Cymbals)

  // Calcular energía total del frame
  for(int i=0; i<5; i++) totalVolume += bandValues[i];

  // ============================================================
  // 4. LÓGICA DE DECISIÓN
  // ============================================================
  
  // UMBRAL MAESTRO (Master Threshold)
  // Si la suma total supera 1500, asumimos que hay música real.
  if (totalVolume > 1500) { 
    lastAudioTime = millis();
    isStandby = false;        
    
    for (int i = 0; i < 5; i++) {
      
      // ALGORITMO DE DETECCIÓN DE BEAT
      // Condición 1: Superar el piso de ruido (250)
      // Condición 2: Superar el promedio histórico multiplicado por sensibilidad
      if (bandValues[i] > noiseGate && bandValues[i] > (runningAverage[i] * multipliers[i])) {
        digitalWrite(ledPins[i], HIGH);
        
        // Fast Attack: El promedio sube rápido para adaptarse a canciones intensas
        runningAverage[i] = (runningAverage[i] * 0.6) + (bandValues[i] * 0.4); 
      } else {
        digitalWrite(ledPins[i], LOW);
        
        // Slow Decay: El promedio baja lento para no perderse en silencios cortos
        runningAverage[i] = runningAverage[i] * 0.95; 
      }
      
      // Safety Floor: Evitar que el promedio baje demasiado cerca del ruido
      if (runningAverage[i] < (noiseGate + 50)) runningAverage[i] = (noiseGate + 50);
    }
  } 
  else { 
    // MODO SILENCIO
    
    // Apagar LEDs si la música acaba de parar
    if (!isStandby && (millis() - lastAudioTime < silenceDelay)) {
       for(int i=0; i<5; i++) digitalWrite(ledPins[i], LOW);
    }

    // Activar animación si pasaron 3 segundos
    if (millis() - lastAudioTime > silenceDelay) {
      isStandby = true;
      animacionStandby(); 
    }
  }
}

// ============================================================
// FUNCIONES DE ANIMACIÓN
// ============================================================

void secuenciaArranque() {
  // Parpadeo rápido x3
  for(int k=0; k<3; k++) {
    for (int i = 0; i < 5; i++) digitalWrite(ledPins[i], HIGH);
    delay(100);
    for (int i = 0; i < 5; i++) digitalWrite(ledPins[i], LOW);
    delay(100);
  }
  // Barrido (Knight Rider)
  for (int i = 0; i < 5; i++) { digitalWrite(ledPins[i], HIGH); delay(80); }
  for (int i = 0; i < 5; i++) { digitalWrite(ledPins[i], LOW); delay(80); }
}

void animacionStandby() {
  // Scanner sin bloqueo (non-blocking)
  if (millis() - lastScannerUpdate > 120) { 
    lastScannerUpdate = millis();
    
    for(int i=0; i<5; i++) digitalWrite(ledPins[i], LOW);
    digitalWrite(ledPins[scannerPos], HIGH);
    
    scannerPos += scannerDir;
    
    // Rebotar en los extremos
    if (scannerPos >= 4 || scannerPos <= 0) scannerDir = -scannerDir; 
  }
}

```


---

[Ver Hardware](./hardware.md){: .btn .btn-outline } [Ver Proceso](./proceso.md){: .btn .btn-outline } [Ver Diseño](./diseno.md){: .btn .btn-outline } [Inicio](../practica-2){: .btn .btn-primary } 