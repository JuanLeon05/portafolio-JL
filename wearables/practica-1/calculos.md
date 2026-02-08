---
layout: default
title: 2. Cálculos
parent: P1 - Pulsera RockBand
grand_parent: Wearables
nav_order: 2
---

# Validación Matemática (Ley de Ohm)
{: .fs-9 }

Dado que cada color de LED tiene una composición química diferente, su **Voltaje Forward ($V_f$)** varía. Para garantizar un brillo uniforme y evitar quemar los componentes, se calculó la resistencia limitadora específica para cada uno.

---

## Fórmulas Aplicadas

Utilizamos la Ley de Ohm para determinar el valor de la resistencia ($R$) necesaria:

$$R = \frac{V_{fuente} - V_{led}}{I_{deseada}}$$

* **$V_{fuente}$:** 5V (Fuente externa estándar).
* **$I_{deseada}$:** 20mA (0.02A) para brillo óptimo sin sobrecalentamiento.

---

## Tabla de Valores

| Color LED | Voltaje Típico ($V_f$) | Cálculo | Resistencia Comercial Usada |
| :--- | :--- | :--- | :--- |
| 🔴 **Rojo** | 1.8V - 2.0V | $(5V - 2.0V) / 0.02A = 150\Omega$ | **150 $\Omega$** |
| 🟠 **Naranja** | 2.0V - 2.1V | $(5V - 2.0V) / 0.02A = 150\Omega$ | **150 $\Omega$** |
| 🟡 **Amarillo** | 2.1V - 2.2V | $(5V - 2.1V) / 0.02A = 145\Omega$ | **150 $\Omega$** |
| 🟢 **Verde** | 3.0V - 3.2V | $(5V - 3.0V) / 0.02A = 100\Omega$ | **100 $\Omega$** |
| 🔵 **Azul** | 3.0V - 3.2V | $(5V - 3.0V) / 0.02A = 100\Omega$ | **100 $\Omega$** |

> **Nota de Ingeniería:** Se optó por valores comerciales estándar superiores al cálculo teórico para añadir un margen de seguridad, considerando que la resistencia del hilo conductor añade una pequeña impedancia extra al circuito.

---

[Ver Hardware](./hardware.md){: .btn .btn-outline } [Ver Proceso](./proceso.md){: .btn .btn-outline } [Ver Diseño](./diseno.md){: .btn .btn-outline } [Practica 1](../practica-1){: .btn .btn-outline } 