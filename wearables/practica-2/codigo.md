---
layout: default
title: Algoritmo y Código
parent: P2 - SonicGauntlet
grand_parent: Wearables
nav_order: 2
---

# El Cerebro Digital: Firmware Reactivo
{: .fs-9 }

A diferencia de la *Pulsera RockBand* (Práctica 1), que dependía de la acción mecánica del usuario, el **SonicGauntlet** opera con autonomía total. El firmware está diseñado bajo la filosofía de "Set and Forget": el artista se lo pone y el sistema se encarga de adaptarse al entorno sonoro.

El código implementa una arquitectura de **Sistemas Embebidos en Tiempo Real**, priorizando la velocidad de respuesta (latencia cero) y la estabilidad.

---

## Lógica de Ingeniería: ¿Cómo "escucha" el guante?

### 1. Auto-Gain & Beat Detection (Adaptabilidad)
En un concierto, el volumen cambia drásticamente entre una balada y un solo de batería. Un sistema estático fallaría (se saturaría o no encendería).
* **Solución:** Implementamos un **Promedio Móvil (Running Average)**. El algoritmo aprende del volumen de los últimos segundos y ajusta su "umbral de disparo" dinámicamente.
* **Resultado:** El guante detecta el *beat* con la misma precisión en el ensayo silencioso que en el escenario principal a 100dB.

### 2. Máquina de Estados (Gestión de Energía)
El sistema no gasta recursos innecesariamente.
* **Estado ACTIVO:** Si hay música (>600 de amplitud), ejecuta la FFT a 9000Hz para mapear frecuencias a los dedos.
* **Estado STANDBY:** Si detecta silencio por >3 segundos, entra en modo reposo visual, activando una animación suave ("Scanner") para indicar que el sistema está vivo pero esperando señal.

### 3. Multitarea No Bloqueante
Prohibido usar `delay()`. El procesador utiliza contadores de tiempo (`millis()`) para manejar la animación de luces sin dejar de escuchar el micrófono ni un solo microsegundo.

---

## Diagrama de Flujo de Datos

1.  **Muestreo:** Adquisición de 64 muestras de audio (Ventana de tiempo).
2.  **FFT:** Descomposición matemática en espectro de frecuencias.
3.  **Binning:** Agrupación de frecuencias en 5 canales (Dedos).
4.  **Decisión:** Comparación contra el promedio móvil -> ¿Encender LED?

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

  if (totalVolume > 600) { // CASO A: HAY MÚSICA
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


[Ver Hardware](./hardware.md){: .btn .btn-outline } [Ver Proceso](./proceso.md){: .btn .btn-outline } [Ver Diseño](./diseno.md){: .btn .btn-outline } [Inicio](../practica-2){: .btn .btn-primary } 