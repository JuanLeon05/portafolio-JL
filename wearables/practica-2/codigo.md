---
layout: default
title: Algoritmo y Código
parent: P2
grand_parent: Wearables
nav_order: 4
---

# Algoritmo y Firmware "Ultimate"
{: .fs-9 }

El firmware del **SpectraGlove** no es un simple interruptor que enciende luces cuando hay ruido. Es un sistema reactivo diseñado para interpretar la música de la misma forma que lo hace el oído humano.

Para lograr esto, el código implementa tres conceptos avanzados de ingeniería de software: **Adaptabilidad Automática**, **Multitarea** y **Gestión de Estados**.

---

## ¿Cómo "piensa" el guante? (Conceptos Clave)

### 1. Detección de Ritmo Adaptativa (Beat Detection)
**El Problema:**
La mayoría de las luces audiorítmicas baratas tienen un problema: si la canción es muy suave, no se encienden; si es muy fuerte, se quedan siempre encendidas. Esto pasa porque usan un "umbral fijo" (ej. enciéndete si el volumen es mayor a 50).

**La Solución (Nuestra Ingeniería):**
Implementamos un **Promedio Móvil (Running Average)**.
El Arduino calcula constantemente el volumen promedio de los últimos segundos.
* Si la canción es suave, el guante baja su umbral de sensibilidad automáticamente.
* Si "explota" el coro de la canción, el guante sube su umbral para detectar solo los picos más fuertes.
* *Resultado:* El guante "baila" igual de bien con música clásica que con Heavy Metal sin tener que ajustar nada manualmente.

### 2. Máquina de Estados (Modo Espera)
El sistema sabe distinguir entre "estar apagado" y "estar esperando".
* **Modo Música:** Si detecta sonido, ejecuta el análisis de frecuencias FFT para iluminar los dedos.
* **Modo Standby:** Si detecta silencio total por más de 3 segundos, asume que la música terminó y entra en un modo de ahorro de atención. Aquí activa una animación suave tipo "scanner" (inspirada en *Knight Rider*) para indicar que el sistema sigue vivo y listo, pero en reposo.

### 3. Multitarea Real (Sin bloqueos)
En programación básica, la función `delay(100)` congela el cerebro del procesador por 100 milisegundos. Si usáramos eso, el guante perdería ritmos rápidos de batería.
En su lugar, usamos contadores de tiempo (`millis()`). El procesador nunca se detiene; siempre está preguntando: *"¿Ya pasó el tiempo para mover la luz? No. Ok, sigo escuchando música"*. Esto permite una respuesta visual con latencia cero.

---

## Diagrama de Flujo del Procesamiento

1.  **Muestreo:** El sistema toma 64 "fotos" del audio por segundo a una velocidad de 9000Hz.
2.  **Transformación (FFT):** Matemáticamente descompone esas fotos para separar los Graves (Bombo), Medios (Voz) y Agudos (Platillos).
3.  **Decisión Lógica:**
    * ¿Hay volumen general? -> **Sí** -> Ejecuta lógica de Beat Detection por cada dedo.
    * ¿Hay volumen general? -> **No** -> ¿Han pasado 3 segundos? -> **Sí** -> Activa Animación Standby.

---

## El Código (Firmware)

Este código está optimizado para el **Arduino Uno R4 WiFi**.

```cpp
/*
  Vúmetro - Arduino Uno R4 WiFi + MHM-28
  
  Características Técnicas: 
  - FFT con Ventana Hamming para limpieza de señal.
  - Filtro de Promedio Móvil para control automático de ganancia.
  - Animaciones no bloqueantes basadas en millis().
*/

#include "arduinoFFT.h"

// --- CONFIGURACIÓN DE HARDWARE ---
const int audioPin = A0;                // Entrada de audio analógico
const int ledPins[5] = {2, 3, 4, 5, 6}; // Mapeo físico: Pines D2 a D6

// --- CONFIGURACIÓN FFT (Matemáticas) ---
const uint16_t samples = 64;           // Cantidad de lecturas por ciclo
const double samplingFrequency = 9000; // Frecuencia de muestreo (Hz)

double vReal[samples];
double vImag[samples];
ArduinoFFT<double> FFT = ArduinoFFT<double>(vReal, vImag, samples, samplingFrequency);

// --- CEREBRO DINÁMICO (Beat Detection) ---
// Aquí guardamos el volumen promedio reciente de cada frecuencia
double runningAverage[5] = {100, 100, 100, 100, 100}; 

// Multiplicadores: Define qué tan fuerte debe ser un golpe respecto al promedio para encender la luz
// Ejemplo: 1.5 significa que el golpe debe ser 50% más fuerte que el promedio actual
float multipliers[5] = {1.4, 1.3, 1.3, 1.3, 1.5}; 

int noiseGate = 150; // Ignoramos cualquier ruido por debajo de este valor (estática)

// --- CEREBRO DE ESTADOS (Standby) ---
unsigned long lastAudioTime = 0;   // Cronómetro: ¿Cuándo fue el último sonido?
const int silenceDelay = 3000;     // Tiempo de espera (3 segundos)
bool isStandby = false;            // Bandera de estado actual

// Variables para animación "Auto Fantástico" (Scanner)
int scannerPos = 0;
int scannerDir = 1;
unsigned long lastScannerUpdate = 0;
int scannerSpeed = 100; // Velocidad de la animación

void setup() {
  analogReadResolution(10); // Resolución de 10 bits para mayor precisión
  for (int i = 0; i < 5; i++) pinMode(ledPins[i], OUTPUT);

  // Al encender, ejecutamos un test visual para confirmar que todo funciona
  secuenciaArranque();
}

void loop() {
  // 1. MUESTREO DE SEÑAL
  // Tomamos una "foto" instantánea del audio
  unsigned long microseconds = micros();
  for (int i = 0; i < samples; i++) {
    vReal[i] = analogRead(audioPin);
    vImag[i] = 0;
    // Espera milimétrica para mantener la frecuencia de muestreo estable
    while (micros() - microseconds < (1000000 / samplingFrequency)) { /* esperar */ }
    microseconds += (1000000 / samplingFrequency);
  }

  // 2. PROCESAMIENTO MATEMÁTICO (FFT)
  // Convertimos la señal eléctrica en frecuencias separadas
  FFT.windowing(FFTWindow::Hamming, FFTDirection::Forward);
  FFT.compute(FFTDirection::Forward);
  FFT.complexToMagnitude();

  // 3. AGRUPACIÓN DE BANDAS (Asignación a dedos)
  double bandValues[5] = {0};
  double totalVolume = 0;
  
  // Sumamos rangos de frecuencia específicos para cada dedo
  // vReal contiene el resultado de la FFT. Los índices bajos son graves, los altos son agudos.
  for (int i = 2; i < 5; i++) bandValues[0] += vReal[i];   // Dedo 1: Graves profundos (Bombo)
  for (int i = 5; i < 9; i++) bandValues[1] += vReal[i];   // Dedo 2: Graves/Medios
  for (int i = 9; i < 14; i++) bandValues[2] += vReal[i];  // Dedo 3: Medios (Voz principal)
  for (int i = 14; i < 21; i++) bandValues[3] += vReal[i]; // Dedo 4: Medios-Altos
  for (int i = 21; i < 32; i++) bandValues[4] += vReal[i]; // Dedo 5: Agudos (Platillos)

  // Calculamos el volumen total para saber si hay silencio
  for(int i=0; i<5; i++) totalVolume += bandValues[i];

  // --- TOMA DE DECISIONES ---

  if (totalVolume > 1500) { // CASO A: HAY MÚSICA
    lastAudioTime = millis(); // Reiniciamos el cronómetro de silencio
    isStandby = false;        
    
    // Algoritmo de Detección de Golpes (Beat Detection)
    for (int i = 0; i < 5; i++) {
      // El "disparador" es dinámico: Promedio Actual * Multiplicador
      double triggerThreshold = runningAverage[i] * multipliers[i];
      
      if (bandValues[i] > noiseGate && bandValues[i] > triggerThreshold) {
        digitalWrite(ledPins[i], HIGH); // ¡Encender LED!
        
        // Actualizamos el promedio rápido (ataque rápido) para que se adapte a la subida de volumen
        runningAverage[i] = (runningAverage[i] * 0.7) + (bandValues[i] * 0.3);
      } else {
        digitalWrite(ledPins[i], LOW); // Apagar LED
        
        // El promedio baja lentamente (decaimiento lento) esperando el siguiente golpe
        runningAverage[i] = runningAverage[i] * 0.98;
      }
      
      // Seguridad: El promedio nunca puede ser menor al ruido base
      if (runningAverage[i] < noiseGate) runningAverage[i] = noiseGate;
    }
  } 
  else { // CASO B: SILENCIO DETECTADO
    
    // 1. Limpieza inmediata: Apagar todo si la música acaba de parar
    if (!isStandby && (millis() - lastAudioTime < silenceDelay)) {
       for(int i=0; i<5; i++) digitalWrite(ledPins[i], LOW);
    }

    // 2. Activación de Standby: Si pasaron 3 segundos, iniciar animación
    if (millis() - lastAudioTime > silenceDelay) {
      isStandby = true;
      animacionStandby(); 
    }
  }
}

// --- SECUENCIA DE ARRANQUE (POST) ---
// Se ejecuta una sola vez al encender para verificar LEDs
void secuenciaArranque() {
  for (int i = 0; i < 5; i++) { digitalWrite(ledPins[i], HIGH); delay(100); }
  delay(200);
  for (int i = 4; i >= 0; i--) { digitalWrite(ledPins[i], LOW); delay(100); }
  
  // Parpadeo final
  for(int k=0; k<3; k++) {
    for (int i = 0; i < 5; i++) digitalWrite(ledPins[i], HIGH);
    delay(100);
    for (int i = 0; i < 5; i++) digitalWrite(ledPins[i], LOW);
    delay(100);
  }
}

// --- ANIMACIÓN STANDBY ---
// Esta función NO usa delay(), permite que el guante siga "escuchando" mientras anima
void animacionStandby() {
  if (millis() - lastScannerUpdate > scannerSpeed) {
    lastScannerUpdate = millis();
    
    // Apagamos todos y encendemos solo el actual
    for(int i=0; i<5; i++) digitalWrite(ledPins[i], LOW);
    digitalWrite(ledPins[scannerPos], HIGH);
    
    // Movemos la posición
    scannerPos += scannerDir;
    
    // Si llegamos a un borde, rebotamos la dirección
    if (scannerPos >= 4 || scannerPos <= 0) {
      scannerDir = -scannerDir; 
    }
  }
}

```


[Ver Hardware](./hardware.md){: .btn .btn-outline } [Ver Proceso](./proceso.md){: .btn .btn-outline } [Ver Diseño](./diseno.md){: .btn .btn-outline } [Practica 2](../practica-2){: .btn .btn-outline } 