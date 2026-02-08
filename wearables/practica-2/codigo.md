---
layout: default
title: 2. Algoritmo y Código
parent: P2 - SpectraGlove
grand_parent: Wearables
nav_order: 2
---

# Algoritmo y Firmware
{: .fs-9 }

El desafío principal del **SpectraGlove** es traducir señales de audio analógicas en pulsos de luz en tiempo real. Para esto, utilizamos un análisis de frecuencia real con FFT.

---

## 💻 El Código

```cpp
#include "arduinoFFT.h"

// Configuración del FFT
#define SAMPLES 128             
#define SAMPLING_FREQUENCY 1000 

arduinoFFT FFT = arduinoFFT();

unsigned int sampling_period_us;
unsigned long microseconds;

double vReal[SAMPLES];
double vImag[SAMPLES];

// Pines de los LEDs (Dedos: Pulgar a Meñique)
int ledPins[] = {2, 3, 4, 5, 6}; 

void setup() {
  sampling_period_us = round(1000000*(1.0/SAMPLING_FREQUENCY));
  
  for(int i=0; i<5; i++) {
    pinMode(ledPins[i], OUTPUT);
  }
}

void loop() {
  // 1. MUESTREO (SAMPLING)
  for(int i=0; i<SAMPLES; i++) {
    microseconds = micros();
    vReal[i] = analogRead(A0);
    vImag[i] = 0;
    
    // Espera precisa para mantener 1000Hz estables
    while(micros() < (microseconds + sampling_period_us)) { }
  }

  // 2. PROCESAMIENTO FFT
  FFT.Windowing(vReal, SAMPLES, FFT_WIN_TYP_HAMMING, FFT_FORWARD);
  FFT.Compute(vReal, vImag, SAMPLES, FFT_FORWARD);
  FFT.ComplexToMagnitude(vReal, vImag, SAMPLES);

  // 3. VISUALIZACIÓN
  visualizarFrecuencias();
}

void visualizarFrecuencias() {
  // Ejemplo de lógica: Si los graves superan el umbral, encender LED
  if (vReal[3] > 500) {
    digitalWrite(ledPins[0], HIGH);
  } else {
    digitalWrite(ledPins[0], LOW);
  }
}
```