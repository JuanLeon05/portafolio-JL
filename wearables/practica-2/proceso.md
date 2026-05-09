---
layout: default
title: Proceso y Manufactura
parent: P2 - SonicGauntlet
grand_parent: Wearables
nav_order: 5
---

# Bitácora de Construcción e Iteración
{: .fs-9 }

La fabricación representó un salto en complejidad. Al pasar a un sustrato textil de alto gramaje, la gestión del espacio y el ruido electromagnético dictaron las iteraciones de diseño y manufactura.

---

## Fase 1: Manufactura Textil
Se confeccionaron las piezas de mezclilla y el forro interno protector. Se integraron bolsillos a medida para distribuir el peso del microcontrolador y la batería, asegurando que la prenda no perdiera su flexibilidad.

> **Evidencia:** [Inserta aquí foto del patronaje o costura a máquina]

## Fase 2: Iteraciones de Firmware (Ruido)
* **El Problema:** Picos electromagnéticos del Bluetooth interrumpían la secuencia de reposo.
* **La Solución:** Implementación de un filtro de confirmación (Debounce) en el código, requiriendo validación continua de la señal para activar las luces.

## Fase 3: Iteraciones Físicas y Ruteo Híbrido
* **El Problema:** Alimentar el sistema solo con hilo conductivo causaba caídas de voltaje por la alta resistencia del material.
* **La Solución:** Implementación de cables de cobre ocultos para las líneas de poder principal, reservando el hilo conductivo superficial únicamente para las señales de datos de los LEDs.

> **Evidencia:** [Inserta aquí foto de las pruebas de electrónica]

## Fase 4: Ensamble Final
Se cosió el circuito con hilo conductivo y se aplicó sellador en los nudos para evitar cortocircuitos por el movimiento natural de la tela.

---

[Ver Evidencia](./evidencia.md){: .btn .btn-outline .mr-2 } [Inicio](../practica-2){: .btn .btn-primary }