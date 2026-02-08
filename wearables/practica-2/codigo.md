---
layout: default
title: 2. Algoritmo y Código
parent: P2 - SpectraGlove
grand_parent: Wearables
nav_order: 2
---

# Algoritmo y Firmware
{: .fs-9 }

El desafío principal del **SpectraGlove** es traducir señales de audio analógicas en pulsos de luz en tiempo real (Real-Time System). Para esto, no usamos simples condicionales `if/else` basados en volumen, sino un análisis de frecuencia real.

---

## El Cerebro: Fast Fourier Transform (FFT)

Utilizamos el algoritmo **FFT (Transformada Rápida de Fourier)** para descomponer la señal de audio en sus frecuencias constitutivas.

* **Librería:** `arduinoFFT` por Enrique Condés.
* **Muestreo:** 128 muestras (samples) por ciclo.
* **Frecuencia de Muestreo:** ~1000 Hz (enfocado en el rango visible de la música: graves y medios).

### ¿Cómo funciona el proceso?

1.  **Sampling (Muestreo):** El Arduino toma una "foto" del audio (128 lecturas rápidas del pin A0).
2.  **Windowing (Ventaneado):** Suavizamos la señal para evitar "ruido" matemático en los bordes.
3.  **Compute (Cálculo):** La FFT convierte el *Dominio del Tiempo* (onda) al *Dominio de la Frecuencia* (barras).
4.  **Binning (Clasificación):** Agrupamos los resultados en 5 rangos (Graves, Medios-Graves, Medios, Medios-Altos, Agudos) correspondientes a los 5 dedos.

---

## El Código

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

Volver a Hardware{: .btn .btn-outline .mr-2 } Ver Repositorio Completo{: .btn .btn-primary }