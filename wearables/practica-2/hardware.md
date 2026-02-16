---
layout: default
title: Hardware y Electrónica Textil
parent: P2 - SonicGauntlet
grand_parent: Wearables
nav_order: 1
---

# Hardware, Acondicionamiento y E-Textiles
{: .fs-9 }

El sistema **SonicGauntlet** es un *wearable* autónomo de visualización acústica. A diferencia de los prototipos rígidos en PCB o protoboard, toda la unidad de procesamiento, gestión de energía y visualización interactúa directamente sobre un sustrato textil flexible (guante/antebrazo). 

Esta naturaleza flexible exige una arquitectura electromecánica híbrida para garantizar la integridad de las señales analógicas y la estabilidad de la potencia frente al movimiento constante del usuario.

---

## 1. Arquitectura de Energía y Prevención de Brownouts

El mayor reto en la electrónica textil es la distribución de potencia de corriente continua (DC). Los hilos conductivos (acero inoxidable o plata) presentan una resistencia parásita significativa (10Ω - 50Ω por metro). Durante los picos de consumo (*Inrush Current*) del microcontrolador y la antena Bluetooth, esta resistencia genera una caída de tensión severa (Ley de Ohm: $V = I \times R$), provocando reinicios en bucle o *Brownouts*.

Para solucionar esto de forma definitiva, se diseñó una **Arquitectura de Ruteo Híbrido**:

* **Ruta de Potencia (Líneas VCC y GND):** Se utiliza **cable de cobre multifilar ultra flexible** (AWG 26) oculto en el forro de la tela. Esto garantiza una impedancia casi nula ($~0\Omega$) desde la batería hasta los procesadores, entregando todo el amperaje necesario sin pérdidas térmicas.
* **Ruta de Señales (ADC y GPIOs):** Se utiliza **hilo conductivo de acero inoxidable recubierto de plata** cosido visiblemente. Dado que los pines analógicos y de datos operan con microamperios ($\mu A$), la resistencia del hilo no genera caídas de voltaje perceptibles, permitiendo mantener la estética *rockera* del wearable.

---

## 2. Acondicionamiento de Señal y Filtros (Uso de Capacitores)

El circuito utiliza **dos capacitores idénticos** (capacitores electrolíticos), pero aplicados con propósitos físicos diametralmente opuestos para estabilizar el sistema:

### A. Capacitor 1: Desacople y Estabilización (Bypass)
Ubicado en paralelo entre las líneas de **VCC y GND** (compartidas por el ESP32 y el MH-M28). 
* **Función:** Actúa como un "tanque de reserva" de electrones de acción rápida. Cuando el módulo Bluetooth enciende su antena de radiofrecuencia (RF), exige un pico violento de corriente. El capacitor suministra esta energía instantáneamente, evitando que el voltaje de la batería colapse y manteniendo al ESP32 estable.

### B. Capacitor 2: Acople de Audio y Bloqueo DC (DC Block)
Ubicado en serie entre el **canal de salida de audio del MH-M28** y el **Pin Analógico (A0)** del microcontrolador.
* **El Problema:** La señal de audio es Corriente Alterna (AC) centrada en 0V (con valores negativos). El ADC del ESP32-C3 opera a 3.3V estrictamente positivos y se dañaría con voltajes negativos.
* **La Solución:** Este capacitor permite que las ondas de frecuencia de la música (AC) crucen hacia el microcontrolador, pero **bloqueea** cualquier voltaje de corriente continua (DC) proveniente del Bluetooth. 
* **El Piso Virtual (DC Bias):** Después del capacitor, la señal de audio "limpia" se inyecta en el centro de un divisor de voltaje (dos resistencias de 100kΩ conectadas a 3.3V y GND). Esto "monta" la onda de música sobre un centro matemático perfecto de **1.65V**, permitiendo que la FFT lea la onda completa sin recortes.

---

## 3. Lista de Materiales y Componentes (BOM)

### Procesamiento y Alimentación
| Componente | Cantidad | Función Técnica en el Sistema |
| :--- | :---: | :--- |
| **ESP32-C3 Super Mini** | 1 | CPU (RISC-V 32-bit). Lógica 3.3V, ADC de 12-bits (0-4095) y DSP. |
| **Módulo MH-M28** | 1 | Receptor estéreo Bluetooth 4.2. Fuente de la señal de audio analógica. |
| **Módulo TP4056** | 1 | Controlador de carga USB-C con protección activa (DW01) contra sobredescarga. |
| **Batería LiPo 3.7V** | 1 | Fuente principal (Provee DC pura, eliminando el ruido de fuentes conmutadas). |
| **Capacitor Electrolítico 100nF** | 2 | Uno para Bypass (VCC/GND) y otro para Acople AC (Audio/ADC). |
| **Resistencias 100kΩ** | 2 | Divisor de tensión para el Bias de 1.65V (Referencia del ADC). |

### Interfaz Háptica e Integración E-Textil
| Componente | Cantidad | Función Técnica en el Sistema |
| :--- | :---: | :--- |
| **LEDs 3mm** | 5 | Indicadores visuales de espectro de frecuencia. |
| **Resistencias 68Ω** | 5 | Limitadores de corriente integrados en las patas de cada LED. |
| **Cable Cobre Flexible** | 1m | Ruteo oculto de potencia de alta corriente. |
| **Hilo Conductivo** | 3m | Ruteo superficial de bioseñales y datos (Alta flexibilidad). |
| **Tela Conductiva** | 1 | Tela con adhesivo conductivo para crear trazas planas soldables. |
| **Cinta de Aislar** | 1 | Cinta aislante usada para mantener separadas las rutas de los hilos conductivos. |
| **Esmalte** | 1 | Sellador dieléctrico mecánico para inmovilizar nudos. |

---

## 4. Esquema de Enrutamiento Físico (Pinout)

Las conexiones lógicas se asignaron evitando los pines de arranque (*Boot Strapping* 8 y 9) del C3 para prevenir bloqueos de hardware al encender con la batería.

| Pin ESP32-C3 | Conexión Física | Rango FFT Asignado | Medio de Transmisión |
| :---: | :--- | :---: | :--- |
| **GPIO 0 (ADC1_0)** | Salida Audio MH-M28 + Capacitor de Acople | Entrada Analógica | Hilo Conductivo |
| **GPIO 4** | LED 1 | Graves (Low) | Hilo Conductivo |
| **GPIO 3** | LED 2 | Medios-Graves | Hilo Conductivo |
| **GPIO 7** | LED 3 | Medios | Hilo Conductivo |
| **GPIO 6** | LED 4 | Medios-Altos | Hilo Conductivo |
| **GPIO 5** | LED 5 | Agudos (High) | Hilo Conductivo |
| **5V (VIN)** | Salida OUT+ del TP4056 / Batería | Potencia Lógica (+) | **Cable de Cobre** |
| **GND** | Salida OUT- del TP4056 / Batería | Tierra Común (-) | **Cable de Cobre** |

---

## 5. Ingeniería de Manufactura Textil (Wearable Design)

Para escalar este prototipo a un producto robusto tolerante al movimiento y a la deformación mecánica, se aplican los siguientes protocolos de ensamble e-textil:

1.  **Inmovilización de Terminales (Sellado Mecánico):** El hilo de acero/plata carece de fricción natural. Todo nudo que abrace un pad metálico o la pata de un LED se sella herméticamente con una gota de esmalte de uñas. Esto previene que el movimiento genere micro-arcos eléctricos (ruido).
2.  **Tensión Estructural Disociada:** La electrónica no debe sufrir estrés mecánico. Los componentes pesados (Batería, TP4056, ESP32) se anclan a la prenda usando hilo de costura de poliéster de alta resistencia. El hilo conductivo se cose con ligera holgura geométrica (rutas en zigzag) para que la tela pueda estirarse sin degollar las conexiones de datos.

---

[Ver Código](../codigo){: .btn .btn-outline .mr-2 }
[Ver Proceso](../proceso){: .btn .btn-outline .mr-2 }
[Ver Diseño](../diseno){: .btn .btn-outline .mr-2 }
[Inicio](../practica-2){: .btn .btn-primary } 