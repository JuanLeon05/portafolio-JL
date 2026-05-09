---
layout: default
title: 1. Hardware
parent: P1 - Pulsera RockBand
grand_parent: Wearables
nav_order: 1
---

# Hardware y Topología del Circuito
{: .fs-9 }

El desafío técnico de esta práctica no radica en la complejidad lógica, sino en la **integridad eléctrica** de las conexiones flexibles. Se implementó un circuito en paralelo para garantizar que todos los LEDs reciban el mismo voltaje de la fuente, independientemente de su consumo individual. [cite_start]Además, es uso obligatorio de hilo conductor para las conexiones[cite: 17].

---

## Diagrama Esquemático

El sistema funciona como un circuito cerrado por contacto mecánico:

1.  **Entrada:** Fuente de voltaje externa conectada a dos tiras de cobre (VCC y GND) en el extremo de la pulsera.
2.  [cite_start]**Conducción:** Hilo conductor (2 cabos) transporta la corriente a lo largo de la tela[cite: 17].
3.  **Interruptor:** Los broches (macho/hembra) cierran el circuito al abrocharse.
4.  [cite_start]**Carga:** 5 LEDs de colores diferentes en configuración paralela[cite: 17], cada uno con su resistencia limitadora de corriente.

> **Evidencia:** [Inserta aquí la imagen de tu diagrama esquemático]

---

## Lista de Materiales (BOM)

| Componente | Especificación | Función |
| :--- | :--- | :--- |
| **Actuadores** | 5 LEDs (Naranja, Amarillo, Verde, Rojo, Azul) | [cite_start]Salida visual multicolor[cite: 17]. |
| **Resistencias** | 5 unidades (Valores calculados) | Protección de corriente individual por LED. |
| **Conductor** | Hilo conductor | [cite_start]Pistas flexibles del circuito[cite: 17]. |
| **Interfaz** | 2 Tiras de cobre | Pads de conexión para caimanes. |
| **Interruptor** | 2 Broches de presión metálicos | Cierre mecánico y eléctrico. |
| **Sustrato** | Tela negra | Base estructural y aislamiento visual. |

---

[Ver cálculos](./calculos.md){: .btn .btn-outline } [Ver Proceso](./proceso.md){: .btn .btn-outline } [Inicio](../practica-1){: .btn .btn-primary }