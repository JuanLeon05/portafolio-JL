---
layout: default
title: Hardware y Electrónica
parent: P2 - SpectraGlove
grand_parent: Wearables
nav_order: 1
---

# Hardware y Electrónica
{: .fs-9 }

El corazón del **SpectraGlove** combina procesamiento digital de señales con técnicas de e-textiles.

---

##  Lista de Materiales (BOM)

| Componente | Función | Especificaciones |
| :--- | :--- | :--- |
| **Microcontrolador** | Cerebro | Arduino Uno R4 WiFi |
| **Módulo de Audio** | Recepción | MHM-28 (Bluetooth 4.2) |
| **Actuadores** | Visualización | 5x LEDs (2 Rojos, 3 Azules) |
| **Conexión** | Conductividad | Hilo Conductor de Plata |
| **Resistencias** | Protección | 5x 100Ω |

---

##  Diagrama de Conexiones

### Pinout del Arduino

| Pin | Función | Color | Ubicación |
| :---: | :--- | :---: | :--- |
| **D2** | Graves | 🔴 Rojo | Muñeca |
| **D3** | Medios-Graves | 🔴 Rojo | Anular |
| **D4** | Medios | 🔵 Azul | Medio |
| **D5** | Medios-Altos | 🔵 Azul | Índice |
| **D6** | Agudos | 🔵 Azul | Pulgar |
| **A0** | Audio IN | - | Salida MHM-28 |

> **Nota Técnica:** Las resistencias de 100Ω compensan la resistencia interna del hilo conductor (~15Ω/m).