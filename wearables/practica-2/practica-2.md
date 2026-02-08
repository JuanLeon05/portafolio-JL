---
layout: default
title: P2 - SonicGauntlet
parent: Wearables
has_children: true
nav_order: 2
---

# Práctica 2: SonicGauntlet (Stage Edition)
{: .fs-9 }

**Instrumento de Visualización Escénica**
{: .fs-6 .fw-300 }

Evolución directa de la *Pulsera RockBand*. Mientras que el proyecto anterior exploraba la electrónica pasiva para la audiencia, el **SonicGauntlet** es una herramienta activa diseñada para el artista en el escenario. Este guante traduce la energía musical en luz en tiempo real, convirtiendo la mano del intérprete en un ecualizador visual.

---

## Demostración en Vivo

<div class="responsive-embed">
  <iframe src="https://www.youtube.com/embed/TU_ID_DE_VIDEO" title="SonicGauntlet Demo" allowfullscreen></iframe>
</div>

*(Nota: Reemplaza TU_ID_DE_VIDEO por el ID real de tu video)*

---

## Evolución Técnica: De Pasivo a Reactivo

Este proyecto representa un salto de ingeniería significativo respecto a la Práctica 1, pasando de la conmutación mecánica al procesamiento digital de señales (DSP).

| Característica | Práctica 1 (Pulsera) | Práctica 2 (SonicGauntlet) |
| :--- | :--- | :--- |
| **Rol** | Accesorio de Fan | Herramienta de Performance |
| **Tecnología** | Analógica (Ley de Ohm) | Digital (Microcontrolador de 32 bits) |
| **Activación** | Interruptor Mecánico | Análisis de Audio (FFT) |
| **Complejidad** | Circuito Paralelo Simple | Matriz Direccionable + Algoritmo |

---

## Arquitectura del Sistema

El guante utiliza un **Arduino R4 WiFi** (arquitectura Renesas) para capturar audio ambiental. Mediante un algoritmo de **Transformada Rápida de Fourier (FFT)**, descompone la señal en 5 bandas de frecuencia críticas (Graves, Medios-Graves, Medios, Medios-Altos, Agudos) y asigna cada una a un dedo específico, logrando una sincronización visual de latencia cero (<20ms).

---

## Documentación del Proyecto

Explore los módulos técnicos para entender cómo se construyó esta herramienta de performance:

### 1. Hardware y Electrónica
Diagrama del circuito digital, integración del módulo de micrófono y gestión de energía para el escenario.
[Ver Hardware](./hardware){: .btn .btn-primary .mr-2 }

### 2. Algoritmo y Código (El Cerebro)
Explicación del firmware en C++, configuración del FFT, filtrado de ruido y la máquina de estados que detecta el silencio.
[Ver Código](./codigo){: .btn .btn-primary .mr-2 }

### 3. Proceso y Manufactura
Bitácora de construcción: transición de la protoboard a la costura con hilo conductor y ocultamiento de cables.
[Ver Proceso](./proceso){: .btn .btn-outline .mr-2 }

### 4. Diseño y Estética Rockstar
Colaboración con Diseño Textil para crear una estética agresiva y funcional, ocultando la tecnología para no interferir con la imagen del artista.
[Ver Diseño](./diseno){: .btn .btn-outline .mr-2 }