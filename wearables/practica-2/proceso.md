---
layout: default
title: Proceso y Manufactura
parent: P2
grand_parent: Wearables
nav_order: 2
---

# Bitácora de Diseño y Manufactura
{: .fs-9 }

El desarrollo del **SpectraGlove** siguió un ciclo iterativo de diseño, comenzando con la validación del circuito en protoboard antes de la integración textil definitiva.

---

## Fase 1: Concepción y Bocetaje
El objetivo inicial fue definir la ergonomía. Se determinó que los componentes rígidos (Arduino, Batería) debían situarse en el antebrazo o dorso de la mano para no impedir la flexión de los dedos.

* **Reto:** Rutear 6 líneas de conexión (5 señales + 1 tierra común) sin que los hilos se toquen entre sí al cerrar la mano (cortocircuito).
* **Solución:** Se diseñó un patrón de "rutas" donde cada hilo viaja por un canal específico de la tela, separados por costuras aislantes.

*(Espacio para foto de tu boceto o dibujo en papel)*

---

## Fase 2: Prototipado Electrónico (Breadboard)
Antes de coser, se validó el algoritmo FFT y el circuito de amplificación.

1.  **Prueba Unitaria:** Se conectó el módulo MHM-28 al Arduino para verificar la lectura analógica en el Serial Plotter.
2.  **Validación de LEDs:** Se probaron las resistencias de 100Ω para asegurar que el brillo fuera suficiente considerando la futura resistencia del hilo conductor.

---

## Fase 3: Integración Textil (Costura)
Esta fue la etapa más crítica. Se utilizó hilo de acero inoxidable de 2 cabos.

### Técnica de Conexión "Curly"
Para conectar los LEDs (componentes rígidos) al hilo (flexible), no se usó soldadura directa ya que tiende a quebrarse. Se realizaron anillas en las patas de los componentes y se cosió a través de ellas, asegurando contacto mecánico y eléctrico robusto.

### Gestión de Cables
* **Revés del guante:** Se utilizaron puntadas invisibles para mantener los hilos aislados de la piel.
* **Nudos:** Se aplicó esmalte transparente en los nudos finales para evitar que el hilo conductor se deshilache y cause cortos.

---

## Fase 4: Pruebas y Resultados
Al finalizar la costura, se realizaron pruebas de continuidad con multímetro.

* **Incidencia:** El dedo índice tenía una resistencia inusualmente alta (>50Ω).
* **Corrección:** Se reforzó la costura en el nodo del LED y se redujo la longitud del trazado.

---


[Ver Hardware](./hardware.md){: .btn .btn-outline } [Ver Código](./codigo.md){: .btn .btn-outline } [Ver Diseño](./diseno.md){: .btn .btn-outline } [Practica 2](../practica-2){: .btn .btn-outline } 