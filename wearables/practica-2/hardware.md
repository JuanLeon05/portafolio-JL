---
layout: default
title: Hardware y Electrónica
parent: P2 - SonicGauntlet
grand_parent: Wearables
nav_order: 1
---

# Hardware y Acondicionamiento de Señal
{: .fs-9 }

El sistema **SonicGauntlet** se divide en dos módulos físicos conectados por una interfaz de broches metálicos: la **Unidad de Procesamiento** (Externa/Cinturón) y la **Unidad de Visualización** (Guante de Piel).

Esta arquitectura permite separar la fuente de energía pesada de la mano del artista, mejorando la ergonomía.

---

## Diagrama de Bloques y Acondicionamiento

Para procesar audio en un microcontrolador digital, no basta con conectar el cable. Se implementó una etapa de **Acondicionamiento de Señal (DC Bias)**.

### El Reto: Señal AC vs ADC DC
Las señales de audio son Corriente Alterna (AC) que oscila entre voltajes positivos y negativos. El puerto analógico del Arduino (A0) solo lee valores positivos (0V a 5V). Si conectáramos el audio directo, perderíamos la mitad de la onda (los valores negativos) y podríamos dañar el pin.

### La Solución: Circuito de Offset
Se diseñó un circuito divisor de voltaje y acople capacitivo:
1.  **Divisor de Tensión (2x 100kΩ):** Crea un "piso virtual" de 2.5V (la mitad de 5V) en el pin A0. Así, el silencio vale 2.5V, y la música oscila hacia arriba (5V) y hacia abajo (0V) sin recortarse.
2.  **Capacitor de Desacople (1nF):** Permite pasar la señal de audio (frecuencias) pero bloquea el voltaje DC de la fuente, protegiendo el módulo de audio.

---

## Lista de Materiales (BOM)

### Electrónica de Procesamiento
| Componente | Cantidad | Función Técnica |
| :--- | :---: | :--- |
| **Arduino Uno R4 WiFi** | 1 | Microcontrolador (Renesas 32-bit) con DSP. |
| **Módulo MH-M28** | 1 | Receptor Bluetooth 4.2 y salida de Audio AUX. |
| **Resistencias 100kΩ** | 2 | Divisor de voltaje para DC Offset (Bias 2.5V). |
| **Capacitor 1nF** | 1 | Filtro paso-altos / Desacople de DC. |
| **Jack de Audio 3.5mm** | 1 | Salida para audífonos o amplificador externo. |

### Interfaz Háptica (Guante)
| Componente | Cantidad | Función Técnica |
| :--- | :---: | :--- |
| **LEDs Alta Luminosidad** | 5 | 3 Azules (Agudos/Medios) + 2 Rojos (Graves). |
| **Resistencias 220Ω** | 5 | Limitación de corriente para LEDs. |
| **Hilo Conductivo** | 3m | Acero inoxidable (Bus de datos flexible). |
| **Broches (Snaps)** | 6 | Conectores mecánicos desmontables (VCC, GND, Señales). |
| **Sustrato** | 1 | Piel/Cuero sintético y tela de forro. |

---

## Esquema de Conexiones

### Mapeo de la Matriz en "X"
Los LEDs están dispuestos formando una "X" sobre el dorso de la mano y antebrazo. El mapeo de frecuencias va desde el centro hacia los extremos.

| Pin Arduino | Frecuencia (FFT) | Color LED | Ubicación en la "X" |
| :---: | :--- | :---: | :--- |
| **D2** | Graves (Low) | 🔴 Rojo | Centro / Muñeca |
| **D3** | Medios-Graves | 🔴 Rojo | Extremo Inferior (Antebrazo) |
| **D4** | Medios (Voz) | 🔵 Azul | Centro Superior |
| **D5** | Medios-Altos | 🔵 Azul | Extremo Superior Izq (Índice) |
| **D6** | Agudos (High) | 🔵 Azul | Extremo Superior Der |
| **A0** | Entrada Audio | - | Salida "L" del MH-M28 (Post-Filtro) |

> **Nota de Manufactura:** Las resistencias de 220Ω se soldaron directamente a las patas de los LEDs antes de integrarlos en la piel, aislándolos con termo-retráctil (heat shrink) para protegerlos de la flexión.

---

[Ver Código](./codigo){: .btn .btn-outline .mr-2 }
[Ver Proceso](./proceso){: .btn .btn-outline .mr-2 }
[Ver Diseño](./diseno){: .btn .btn-outline .mr-2 }
[Volver al Índice](../){: .btn .btn-primary }