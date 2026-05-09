---
layout: default
title: P2 - SonicGauntlet
parent: Wearables
has_children: true
nav_order: 1
---

# Práctica 2: SonicGauntlet (Stage Edition)
{: .fs-9 }

**Instrumento de Visualización Acústica y Tech-Wear**
{: .fs-6 .fw-300 }

Evolución directa de nuestro primer acercamiento a los e-textiles. Mientras que el proyecto anterior exploraba la electrónica pasiva, el **SonicGauntlet** es un sistema wearable autónomo y activo. Partiendo de las metodologías de innovación estructurada (como el pensamiento de diseño y los enfoques sistemáticos de Pahl y Beitz), programamos y prototipamos una manga que expresa una clara intención estética mediante una secuencia temporal de encendido y apagado de 5 LEDs controlados por un microcontrolador.

---

## Evolución Técnica: De Pasivo a Reactivo

Este proyecto representa un salto de ingeniería mecatrónica significativo, pasando de la conmutación mecánica al Procesamiento Digital de Señales (DSP) sobre sustratos textiles, manteniendo la premisa de que la secuencia visual inicie al colocarse y abrocharse la prenda.

| Característica | Práctica 1 (Pulsera) | Práctica 2 (SonicGauntlet) |
| :--- | :--- | :--- |
| **Rol** | Accesorio Estático | Pieza de Performance Dinámico |
| **Tecnología** | Analógica | Digital (ESP32-C3 RISC-V) |
| **Fuente de Señal** | N/A | Streaming Bluetooth de Audio |
| **Arquitectura de Poder** | Alimentación externa | LiPo 3.7V + Ruteo Híbrido |

---

## Documentación del Proyecto

Explora los módulos técnicos para entender la ingeniería y el diseño detrás de esta pieza:

### 1. Hardware y Electrónica Textil
Arquitectura de energía híbrida y esquemas de integración.
[Ver Hardware](./hardware.md){: .btn .btn-primary .mr-2 }

### 2. Algoritmo y Firmware (DSP)
Lógica de programación, control de secuencias y filtros de señal.
[Ver Código](./codigo.md){: .btn .btn-primary .mr-2 }

### 3. Diseño y Estética
Concepto visual, confort y ergonomía del wearable.
[Ver Diseño](./diseno.md){: .btn .btn-primary .mr-2 }

### 4. Proceso y Manufactura
Bitácora de fabricación, iteraciones y ensamblaje físico.
[Ver Proceso](./proceso.md){: .btn .btn-primary .mr-2 }

### 5. Evidencia y Pruebas
Registro multimedia demostrando el funcionamiento, ajuste y secuencias.
[Ver Evidencia](./evidencia.md){: .btn .btn-primary .mr-2 }

### 6. Conclusiones
Reflexión analítica sobre los resultados obtenidos.
[Ver Conclusiones](./conclusiones.md){: .btn .btn-primary .mr-2 }