---
layout: default
title: Diseño y Estética
parent: P2 - SonicGauntlet
grand_parent: Wearables
nav_order: 4
---

# Estética Industrial, Grunge y Tech-Wear
{: .fs-9 }

Para el **SonicGauntlet**, la ingeniería se subordina a la imagen. En colaboración con el área de **Diseño de Indumentaria**, definimos que este dispositivo no podía parecer un simple "prototipo de laboratorio", sino una pieza de vestuario de alto impacto lista para un escenario de Rock, Metal Industrial o pasarela Cyberpunk.

El resultado es una silueta agresiva y texturizada, construida con materiales urbanos pesados que priorizan tanto la durabilidad electromecánica como la narrativa visual.

---

## Morfología: Manga Continua y "Tech-Wear"

El diseño evolucionó de un guante asimétrico a una **Manga Continua (Sleeve)** que integra toda la mano y se extiende a lo largo del antebrazo. 

### Decisiones de Diseño (Form Factor y Materiales)
1.  **Guante Completo y Extensión:**
    * A diferencia de los primeros bocetos, se optó por cubrir la mano completa y extender el diseño hasta el codo.
    * *Beneficio:* Proporciona un "lienzo" (Real Estate) mucho más grande para el ruteo de los hilos conductivos y permite alojar todo el hardware de forma autónoma en el brazo, eliminando la necesidad de cables que viajen hacia el cinturón del usuario.
2.  **Sustrato Principal: Mezclilla Negra (Denim):**
    * Se abandonó el cuero en favor de la mezclilla de alto gramaje con acabado desgastado.
    * *Propósito:* La mezclilla ofrece una estética urbana, oscura y *grunge*. A nivel técnico, su estructura fibrosa y resistente soporta perfectamente el peso de la batería y permite puntadas de hilo conductivo extremadamente firmes y tensas.
3.  **Forro Interno y Confort (Tela Gris):**
    * El interior de la manga está forrado con una tela gris suave.
    * *Propósito:* Protege la piel del usuario de la fricción con los hilos de acero, aísla térmicamente el hardware y oculta las rutas de "energía bruta" (los cables de cobre flexibles).
4.  **Hardware como Ornamento (Cadenas):**
    * Se incorporó un sistema de cadenas metálicas drapeadas alrededor del brazo.
    * *Impacto Visual:* Acentúa el peso y la agresividad de la estética industrial, interactuando visualmente con los destellos de los LEDs al moverse.

---

## Distribución de Componentes (Layout Autónomo)

La ubicación de la electrónica dejó de ser modular/externa para convertirse en un sistema 100% "All-in-One", respondiendo a criterios visuales y funcionales:

### 1. La Línea Espectral (Matriz LED)
En lugar de geometrías complejas, los 5 LEDs de alta luminosidad se posicionan formando una **línea longitudinal a lo largo del antebrazo**.
* Esta disposición emula un "vúmetro" o barra de carga de energía implantada en el brazo, creando una lectura visual directa e intuitiva de las frecuencias (desde los graves cerca del codo hasta los agudos hacia la mano).

### 2. Arquitectura de Bolsillos Integrados
Se diseñaron compartimentos específicos de mezclilla cosidos directamente sobre la manga para albergar el hardware, logrando un balance de pesos:
* **Bolsillo Superior (Procesamiento):** Aloja el módulo de audio Bluetooth. Mantiene el "receptort" en la zona de menor interfetencia electrica, asi asegurandose de obtener una señal limpia.
* **Bolsillo Inferior (Energía):** Un compartimento diseñado a medida para insertar la batería LiPo y el módulo de carga. Permite extraer la batería fácilmente para recargarla o cambiarla sin desarmar el guante.

### 3. Estética "Cyberpunk" (Costuras Visibles)
En lugar de ocultar la tecnología, el **hilo conductivo de plata se dejó expuesto** intencionalmente sobre la mezclilla negra. El contraste del metal brillante contra la tela oscura tejiendo rutas geométricas refuerza el concepto de "tecnología vestible" y transhumanismo.

---

## Colaboración Interdisciplinaria

El éxito del proyecto dependió de la fusión de dos disciplinas rigurosas:

| Área | Aporte al SonicGauntlet |
| :--- | :--- |
| **Ingeniería (Juan León)** | Diseño de la arquitectura de energía híbrida (Cobre + Hilo conductivo) para evitar caídas de tensión. Programación del DSP (FFT y Auto-Gain) en el ESP32-C3. Acondicionamiento de señales de hardware y calibración acústica. |
| **Diseño (Cecilia)** | Patronaje y confección de la manga en mezclilla y forro. Diseño e integración de los bolsillos estructurales para el hardware. Aplicación estética de cadenas y remaches. Costura de precisión del circuito e-textil. |

---

> **Nota de Estado:** El proyecto se encuentra **Finalizado y Validado**. El *wearable* opera de manera 100% autónoma por batería, demostrando que es posible fusionar procesamiento digital de señales de alta complejidad con un diseño de indumentaria textil resistente y estéticamente congruente con la escena musical industrial.

---

[Ver Hardware](./hardware){: .btn .btn-outline .mr-2 }
[Ver Proceso](./proceso){: .btn .btn-outline .mr-2 }
[Ver Código](./codigo){: .btn .btn-outline .mr-2 }
[Inicio](../practica-2){: .btn .btn-primary } 