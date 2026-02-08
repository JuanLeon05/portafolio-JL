---
layout: default
title: Proceso y Manufactura
parent: P2 - SonicGauntlet
grand_parent: Wearables
nav_order: 3
---

# De la Teoría al Escenario: Bitácora de Construcción
{: .fs-9 }

La fabricación del **SonicGauntlet** representó un reto de manufactura superior al de la Práctica 1. Al pasar de un sustrato textil suave (algodón) a uno rígido (piel sintética/cuero) y de un circuito analógico a uno digital de alta velocidad, la precisión en la construcción se volvió crítica.

Esta bitácora documenta la metodología de diseño iterativo, desde los primeros bocetos en servilletas hasta el ensamblaje final.

---

## Fase 1: Diseño Conceptual y Bocetaje (Brainstorming)

Esta etapa se realizó en estrecha colaboración con el área de Diseño de Indumentaria. El objetivo no era solo cubrir la mano, sino "vestir" la música.

### 1. Definición del Estilo (Moodboard)
Se estableció una estética **Cyberpunk / Industrial Rock**.
* **Referentes:** Se analizaron vestuarios de bandas como *Daft Punk* o *Nine Inch Nails*, buscando una fusión entre lo orgánico (piel, mano humana) y lo sintético (luz, metal).
* **Materiales:** Se seleccionó **Piel Sintética (Vinipiel)** de alto calibre en color negro mate.
    * *Razón Técnica:* Ofrece rigidez estructural para soportar los componentes sin deformarse.
    * *Razón Estética:* Aporta agresividad visual y durabilidad en el escenario.

### 2. Exploración Morfológica (Bocetos)
Se realizaron múltiples iteraciones en papel para definir la forma del guante.
* **La Decisión de la "X":** Inicialmente pensamos en una línea recta de LEDs, pero resultaba estática. Bocetamos una disposición en "X" que cruza desde los nudillos hasta el antebrazo. Esto dinamiza la visualización y permite usar el largo del brazo como lienzo.
* **Ergonomía Instrumental:** ¿Por qué cubrir solo el dedo índice?
    * *Análisis de Usuario:* Un guitarrista o bajista necesita la yema de sus dedos libre para sentir las cuerdas.
    * *Solución:* El diseño libera los dedos medio, anular y meñique. El dedo índice se cubre porque es el que usualmente se usa para "señalar" o activar efectos en consolas, convirtiéndolo en la "varita mágica" del sistema.

---

## Fase 2: Patronaje y Prototipado (Mockups)

Antes de cortar la piel costosa, se validó la forma.

### 1. El "Toile" (Prueba en Manta)
Se confeccionó un primer prototipo rápido en tela de manta (algodón barato) para probar el ajuste en la mano.
* **Ajuste:** Se corrigió la tensión en la muñeca, ya que el patrón original impedía la rotación completa de la mano.
* **Ubicación del Bluetooth:** En esta fase decidimos que el módulo **MH-M28** no podía ir sobre los nudillos (muy expuesto a golpes). Se movió al antebrazo, oculto entre capas de piel, donde hay menos movimiento.

### 2. Mapeo del Circuito sobre el Patrón
Una vez definido el patrón final en papel, dibujamos el diagrama electrónico directamente sobre él.
* Esto nos permitió ver dónde se cruzarían los hilos conductores y planificar los "puentes" de aislamiento antes de coser.

---

## Fase 3: Validación de Ingeniería (Lab)
Paralelamente al diseño físico, se aseguraba la viabilidad electrónica.

### 1. Acondicionamiento de Señal
El audio es una señal AC (positiva y negativa). El Arduino solo lee voltajes positivos.
* **Prueba en Protoboard:** Se montó el circuito divisor de tensión (Resistencias 100kΩ + Capacitor 1nF) para elevar la señal de audio a 2.5V. Se verificó con el *Serial Plotter* que la onda oscilara centrada sin recortarse.

### 2. Calibración del FFT
Se inyectaron tonos puros (Sine Waves) de 100Hz y 4000Hz para confirmar que el algoritmo encendía los LEDs correctos en la matriz "X" antes de soldar nada.

---

## Fase 4: Manufactura "Hard-Modelling"
El ensamblaje final en piel.

### 1. Corte y Preparación
* **Técnica:** La piel no permite errores (los agujeros son permanentes). Se utilizaron punzones para pre-marcar cada punto de costura y la ubicación exacta de los LEDs.

### 2. Integración de Componentes
* **Soldadura de Refuerzo:** Se soldaron las resistencias de 220Ω directamente a las patas de los LEDs *antes* de la integración, aislando cada unión con tubo termorretráctil (heat shrink) para evitar cortos internos al flexionar el guante.
* **Ocultamiento:** El módulo Bluetooth se "empotró" en una ventana interna del patrón del antebrazo, quedando invisible al exterior pero accesible para mantenimiento.

### 3. Costura de Circuitos (Routing)
Se cosió el hilo conductor de acero inoxidable siguiendo los canales marcados en el reverso.
* **Gestión de Cruces:** En la intersección de la "X", las líneas de datos debían cruzar la línea de tierra. Se utilizó una capa intermedia de tela aislante en el punto exacto del cruce para evitar cortocircuitos físicos.

---

## Fase 5: Arquitectura Modular (Snaps)
La innovación principal respecto a la P1 fue separar la batería pesada de la mano.
* **Instalación de Broches:** Se remacharon 6 broches de presión metálicos en el borde del antebrazo. Estos actúan como la interfaz de conexión robusta hacia el "Cerebro" (Arduino) que el usuario lleva en el cinturón.

---

[Ver Hardware](./hardware){: .btn .btn-outline .mr-2 }
[Ver Código](./codigo){: .btn .btn-outline .mr-2 }
[Ver Diseño](./diseno){: .btn .btn-outline .mr-2 }
[Volver al Índice](../){: .btn .btn-primary }