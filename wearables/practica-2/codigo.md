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

##  El Cerebro: Fast Fourier Transform (FFT)

Utilizamos el algoritmo **FFT (Transformada Rápida de Fourier)** para descomponer la señal de audio en sus frecuencias constitutivas.

* **Librería:** `arduinoFFT` por Enrique Condés.
* **Muestreo:** 128 muestras (samples) por ciclo.
* **Frecuencia de Muestreo:** ~1000 Hz (enfocado en el rango visible de la música, graves y medios).

### ¿Cómo funciona?
1.  **Sampling:** El Arduino toma una "foto" del audio (128 lecturas rápidas del pin A0).
2.  **Windowing:** Suavizamos la señal para evitar "ruido" en los bordes.
3.  **Compute:** La FFT convierte el *Dominio del Tiempo* (onda) al *Dominio de la Frecuencia* (barras).
4.  **Binning:** Agrupamos los resultados en 5 rangos (Graves, Medios-Graves, Medios, Medios-Altos, Agudos) correspondientes a los 5 dedos.

---

##  El Código (Firmware)

```cpp
#include "arduinoFFT.h"

// Configuración del FFT
#define SAMPLES 128             // Debe ser potencia de 2
#define SAMPLING_FREQUENCY 1000 // Hz, debe ser el doble de la frecuencia máxima a leer (Nyquist)

arduinoFFT FFT = arduinoFFT();

unsigned int sampling_period_us;
unsigned long microseconds;

double vReal[SAMPLES];
double vImag[SAMPLES];

// Pines de los LEDs (Dedos)
int ledPins[] = {2, 3, 4, 5, 6}; // Pulgar a Meñique

void setup() {
  sampling_period_us = round(1000000*(1.0/SAMPLING_FREQUENCY));
  
  for(int i=0; i<5; i++) {
    pinMode(ledPins[i], OUTPUT);
  }
}

void loop() {
  /* 1. MUESTREO (SAMPLING) */
  for(int i=0; i<SAMPLES; i++) {
    microseconds = micros();
    vReal[i] = analogRead(A0); // Leer audio del MHM-28
    vImag[i] = 0;
    
    // Esperar para mantener frecuencia de muestreo estable
    while(micros() < (microseconds + sampling_period_us)) { }
  }

  /* 2. PROCESAMIENTO FFT */
  FFT.Windowing(vReal, SAMPLES, FFT_WIN_TYP_HAMMING, FFT_FORWARD);
  FFT.Compute(vReal, vImag, SAMPLES, FFT_FORWARD);
  FFT.ComplexToMagnitude(vReal, vImag, SAMPLES);

  /* 3. VISUALIZACIÓN (Mapping a los 5 dedos) */
  // Analizamos rangos específicos del array vReal[]
  // vReal[2-5]   -> Graves (Bombo)     -> Pin 2
  // vReal[6-15]  -> Medios (Voz/Guitarra) -> Pin 4
  // vReal[20-30] -> Agudos (Platillos) -> Pin 6
  
  visualizarFrecuencias();
}

void visualizarFrecuencias() {
  // Lógica simplificada de visualización
  int grave = vReal[3]; 
  if (grave > 500) digitalWrite(ledPins[0], HIGH);
  else digitalWrite(ledPins[0], LOW);
  
  // ... (código repetido para otros rangos)
}

Optimización: Para reducir la latencia (retraso), eliminamos cualquier uso de la función delay() y optimizamos la lectura analógica para que el guante reaccione instantáneamente al ritmo.

⬅ Volver a Hardware{: .btn .btn-outline .mr-2 } Ver en GitHub{: .btn .btn-primary }