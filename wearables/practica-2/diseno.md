---
layout: default
title: Diseño y Estética
parent: P2 - SonicGauntlet
grand_parent: Wearables
nav_order: 4
---

# Estética Industrial y Ergonomía
{: .fs-9 }

Para el **SonicGauntlet**, la ingeniería se subordina a la imagen. En colaboración con el área de **Diseño de Modas**, definimos que este dispositivo no podía parecer un "prototipo de laboratorio", sino una pieza de vestuario lista para un escenario de Rock o Metal Industrial.

El resultado es una morfología asimétrica construida en materiales pesados, priorizando la durabilidad y la agresividad visual.

---

## Morfología: El "Gauntlet" Asimétrico

A diferencia de un guante tradicional, este diseño se extiende hacia el antebrazo (tipo brazalete medieval o futurista) y deja la mayoría de la mano libre.

### Decisiones de Diseño (Form Factor)
1.  **Asimetría Funcional (Index-Only):**
    * Se decidió cubrir **únicamente el dedo índice**.
    * *Razón:* Permite al músico mantener la sensibilidad táctil en los demás dedos para tocar instrumentos (guitarra, bajo, sintetizador) mientras usa el dedo índice iluminado para señalar o activar efectos visuales.
2.  **Extensión al Antebrazo:**
    * El cuerpo del guante recorre la muñeca y parte del brazo.
    * *Beneficio:* Otorga una amplia superficie de trabajo ("Real Estate") para ocultar el cableado y alojar el módulo de audio sin estorbar el movimiento de la muñeca.
3.  **Materiales Rígidos:**
    * **Base:** Cuero (o Piel Sintética de alto calibre).
    * *Propósito:* Aporta rigidez estructural para soportar los componentes y ofrece esa estética "ruda" y resistente al desgaste del escenario.

---

## Distribución de Componentes (Layout)

La ubicación de la electrónica responde a criterios visuales y prácticos:

### La "X" Sónica
En lugar de una línea aburrida de luces, los 5 LEDs se posicionan formando una **"X" sobre el dorso de la mano y el antebrazo**.
* Esta disposición rompe con la estética tradicional de "vúmetro lineal", creando un símbolo visual propio del artista.

### Arquitectura Modular (La Herencia de la P1)
Siguiendo el principio de diseño de la *Pulsera RockBand*, separamos el procesamiento de la visualización.
* **Cerebro Externo:** El Arduino R4 y la batería pesada se ubican fuera del guante (en un cinturón o pocket externo) para no cansar el brazo del artista.
* **Conexión por Snaps:** La interfaz de conexión y energía utiliza **broches de presión metálicos**.
    * Al conectar el guante al sistema, los broches cierran el circuito de alimentación y datos, funcionando como un conector robusto y fácil de reparar.

### Ocultamiento "Stealth"
* El módulo de audio (Bluetooth/Micrófono) se encuentra "empotrado" dentro del grosor del cuero en la zona del antebrazo, invisible al público pero con acceso al sonido ambiente.

---

## Colaboración Interdisciplinaria

| Área | Aporte al SonicGauntlet |
| :--- | :--- |
| **Ingeniería (Juan León)** | Diseño de la matriz LED en "X", cálculo de resistencia del cableado extendido y sistema de conexión modular por broches, programación del microcontrolador. |
| **Diseño (Cecilia)** | Patronaje del guante asimétrico, selección del cuero, y técnica de costura para "canales" de cableado oculto, fase de diseño y fabricación. |

---

> **Nota de Fase:** Actualmente nos encontramos en la etapa de **Prototipado de Patrón**. Se están realizando pruebas de ergonomía para asegurar que el cuero no restrinja el movimiento de los tendones al cerrar el puño.

---

[Ver Hardware](./hardware){: .btn .btn-outline .mr-2 }
[Ver Proceso](./proceso){: .btn .btn-outline .mr-2 }
[Ver Código](./codigo){: .btn .btn-outline .mr-2 }
[Inicio](../practica-2){: .btn .btn-primary } 