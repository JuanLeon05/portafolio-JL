---
layout: default
title: 2. Algoritmo y Código
parent: P2 - SpectraGlove
grand_parent: Wearables
nav_order: 2
---

#  Algoritmo y Firmware
{: .fs-9 }

El desafío principal del **SpectraGlove** es la traducción de señales analógicas (audio) a respuestas lumínicas con latencia cero. A diferencia de los sistemas básicos que solo miden "volumen" (amplitud), este sistema realiza un **análisis espectral en tiempo real**.

---

## Fundamento Teórico: Fast Fourier Transform (FFT)

Para lograr que cada dedo reaccione a un instrumento distinto, utilizamos la **Transformada Rápida de Fourier (FFT)**. Este algoritmo matemático descompone una señal compleja (música) en sus ondas sinusoidales individuales.

* **Librería:** `arduinoFFT` (Optimizada para arquitecturas AVR/Renesas).
* **Resolución:** 128 muestras (samples) por ciclo de lectura.
* **Frecuencia de Muestreo (Nyquist):** ~1000 Hz.
  * *¿Por qué 1000Hz?* Nos enfocamos en el rango visualmente rítmico de la música (Graves y Medios: 20Hz - 500Hz). Frecuencias más altas requieren más CPU y aportan poco impacto visual.

### Flujo del Procesamiento (Pipeline)

1.  **Sampling (Adquisición):** El ADC (Convertidor Analógico-Digital) toma una "ráfaga" de 128 lecturas del pin A0 en microsegundos.
2.  **Windowing (Ventaneado):** Aplicamos una función *Hamming* para suavizar la señal y reducir el "spectral leakage" (ruido en los bordes de la muestra).
3.  **Compute (Transformación):** La FFT convierte la señal del *Dominio del Tiempo* al *Dominio de la Frecuencia*.
4.  **Binning (Mapeo):** Los resultados se agrupan en "cubetas" (bins) asignadas a cada dedo:
    * **Pulgar/Índice:** Agudos/Voces.
    * **Medio/Anular:** Medios/Ritmo.
    * **Meñique/Muñeca:** Sub-graves (Bombos).

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


[Ver Hardware](./hardware.md){: .btn .btn-outline } [Ver Proceso](./proceso.md){: .btn .btn-outline } [Ver Diseño](../diseño.md){: .btn .btn-outline } [Practica 2](../practica-2){: .btn .btn-outline } 