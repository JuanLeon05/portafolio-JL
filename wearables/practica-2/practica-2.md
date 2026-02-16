---
layout: default
title: P2 - SonicGauntlet
parent: Wearables
has_children: true
nav_order: 2
---

# Práctica 2: SonicGauntlet (Stage Edition)
{: .fs-9 }

**Instrumento de Visualización Acústica y Tech-Wear**
{: .fs-6 .fw-300 }

Evolución directa de la *Pulsera RockBand*. Mientras que el proyecto anterior exploraba la electrónica pasiva para la audiencia, el **SonicGauntlet** es un sistema *wearable* autónomo y activo. Esta manga de mezclilla traduce la energía musical en luz en tiempo real, convirtiendo el brazo del usuario en un ecualizador visual de alto impacto escénico.

---

## Demostración en Vivo

<div class="responsive-embed">
  <iframe src="https://www.youtube.com/embed/TU_ID_DE_VIDEO" title="SonicGauntlet Demo" allowfullscreen></iframe>
</div>

*(Nota: Reemplaza TU_ID_DE_VIDEO por el ID real de tu video)*

---

## Evolución Técnica: De Pasivo a Reactivo

Este proyecto representa un salto de ingeniería mecatrónica significativo respecto a la Práctica 1, superando los retos del ruido electromagnético y pasando de la conmutación mecánica al Procesamiento Digital de Señales (DSP) sobre sustratos textiles.

| Característica | Práctica 1 (Pulsera) | Práctica 2 (SonicGauntlet) |
| :--- | :--- | :--- |
| **Rol** | Accesorio de Fan (Estático) | Pieza de Performance (Dinámico) |
| **Tecnología** | Analógica (Ley de Ohm) | Digital (ESP32-C3 RISC-V de 32 bits) |
| **Fuente de Señal** | N/A | Streaming Bluetooth de Audio (MH-M28) |
| **Activación** | Interruptor Mecánico | Análisis Matemático Avanzado (FFT) |
| **Arquitectura de Poder** | Pila de Botón (Baja descarga) | LiPo 3.7V + Ruteo Híbrido (Cobre/Plata) |

---

## Arquitectura del Sistema

El sistema utiliza un **ESP32-C3 Super Mini** emparejado con un receptor **Bluetooth MH-M28**. En lugar de depender de un micrófono ambiental ruidoso, captura el audio directamente desde el dispositivo del usuario con alta fidelidad. 

Mediante un algoritmo de **Transformada Rápida de Fourier (FFT)**, el firmware descompone la señal analógica en 5 bandas de frecuencia críticas (Graves, Medios-Graves, Medios, Medios-Altos, Agudos). Cada banda controla dinámicamente un LED de alta luminosidad dispuesto en un arreglo lineal a lo largo del antebrazo, logrando una sincronización visual perfecta mediante Control Automático de Ganancia (AGC).

---

## Documentación del Proyecto

Explora los módulos técnicos para entender la ingeniería y el diseño detrás de esta pieza de *tech-wear*:

### 1. Hardware, Acondicionamiento y E-Textiles
Arquitectura de energía híbrida para evitar *brownouts*, uso de capacitores para acople AC/DC, y el diagrama de integración del ESP32-C3 con módulos Bluetooth y baterías LiPo.
[Ver Hardware](../hardware){: .btn .btn-primary .mr-2 }

### 2. Algoritmo y DSP (El Cerebro)
Explicación del firmware en C++, configuración del FFT, filtros de "Zona Muerta" (Deadband), algoritmo anti-transitorios (Debounce) y la máquina de estados responsiva.
[Ver Código](../codigo){: .btn .btn-primary .mr-2 }

### 3. Proceso y Manufactura
Bitácora de construcción: la resolución de caídas de tensión por la resistencia del hilo conductivo, técnicas de aislamiento con Kapton y la creación de bolsillos estructurales.
[Ver Proceso](../proceso){: .btn .btn-primary .mr-2 }

### 4. Diseño y Estética Industrial
Colaboración interdisciplinaria para crear una estética *Grunge / Cyberpunk* utilizando mezclilla de alto gramaje, costuras conductivas expuestas y hardware metálico ornamental (cadenas).
[Ver Diseño](../diseno){: .btn .btn-primary .mr-2 }