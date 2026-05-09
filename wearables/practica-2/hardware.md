---
layout: default
title: Hardware y Electrónica Textil
parent: P2 - SonicGauntlet
grand_parent: Wearables
nav_order: 2
---

# Hardware, Acondicionamiento y E-Textiles
{: .fs-9 }

El sistema **SonicGauntlet** es un wearable autónomo. A diferencia de los prototipos rígidos, toda la unidad de procesamiento y gestión de energía interactúa directamente sobre un sustrato textil flexible. Para el prototipo inicial, la unidad central se planificó de forma modular, lo que permitió ubicar el microcontrolador de manera estratégica.

---

## 1. Arquitectura de Energía y Prevención de Caídas

El mayor reto en la electrónica textil es la distribución de potencia en corriente continua. Los hilos conductivos presentan una resistencia parásita significativa (10 a 50 Ohms por metro). Durante los picos de consumo de la antena Bluetooth, esta resistencia genera una caída de tensión severa (Ley de Ohm: V = I x R), provocando reinicios en bucle.

Para solucionar esto, se diseñó una **Arquitectura de Ruteo Híbrido**:

* **Ruta de Potencia:** Se utiliza cable de cobre multifilar ultra flexible oculto en el forro. Esto garantiza una impedancia casi nula (0 Ohms) desde la batería hasta los procesadores.
* **Ruta de Señales:** Se utiliza hilo conductivo de acero inoxidable recubierto de plata cosido visiblemente. Dado que los pines operan con microamperios, la resistencia del hilo no genera problemas, manteniendo la estética rockera.

<img src="{{ site.baseurl }}/assets/img/practica-2/LayoutElectricoTela.jpeg" alt="Layout Eléctrico" width="49%">
<img src="{{ site.baseurl }}/assets/img/practica-2/LayoutMicrocontroladorTela.jpeg" alt="Layout del Microcontrolador" width="49%">
<br><br>
<img src="{{ site.baseurl }}/assets/img/practica-2/BateriaLayout.jpeg" alt="Ubicación de la Batería" width="49%">
<img src="{{ site.baseurl }}/assets/img/practica-2/CargadorBateria.jpeg" alt="Módulo de Carga" width="49%">
<br><br>
<img src="{{ site.baseurl }}/assets/img/practica-2/ConexionBateria.jpeg" alt="Conexiones de Batería" width="49%">
<img src="{{ site.baseurl }}/assets/img/practica-2/ConexionBateria1.jpeg" alt="Cableado de Batería" width="49%">

## 2. Acondicionamiento de Señal (Capacitores)

El circuito utiliza dos capacitores aplicados con propósitos físicos opuestos para estabilizar el sistema:

1. **Bypass (Estabilización):** Ubicado en paralelo entre VCC y GND. Actúa como un tanque de reserva de energía rápida para cuando el Bluetooth exige picos de corriente, evitando que el voltaje de la batería colapse.
2. **DC Block (Acople de Audio):** Ubicado en serie entre el audio y el pin analógico. Permite que las ondas de frecuencia alterna crucen hacia el microcontrolador, pero bloquea cualquier voltaje continuo. Posteriormente, un divisor de voltaje monta la señal sobre un centro matemático de 1.65V.

<img src="{{ site.baseurl }}/assets/img/practica-2/DiagramaElectrico.PNG" alt="Diagrama Esquemático" width="49%">
<img src="{{ site.baseurl }}/assets/img/practica-2/ReproductorMP3Bluetooth.jpeg" alt="Módulo Receptor Bluetooth" width="49%">

## 3. Lista de Materiales (BOM)

| Componente | Función Técnica en el Sistema |
| :--- | :--- |
| **ESP32-C3 Super Mini** | CPU Lógica 3.3V, ADC de 12-bits y DSP. |
| **Módulo MH-M28** | Receptor estéreo Bluetooth. Fuente de audio. |
| **TP4056 + LiPo 3.7V** | Controlador de carga y fuente principal de energía. |
| **Capacitores / Resistencias** | Filtrado Bypass/Acople AC y divisores de tensión. |
| **LEDs 3mm (5x)** | Indicadores visuales de espectro integrados en la tela. |

---

[Ver Código](./codigo.md){: .btn .btn-outline .mr-2 } [Inicio](../practica-2){: .btn .btn-primary }