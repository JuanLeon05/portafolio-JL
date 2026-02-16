---
layout: default
title: Proceso y Manufactura
parent: P2 - SonicGauntlet
grand_parent: Wearables
nav_order: 3
---

# De la Teoría al Escenario: Bitácora de Construcción e Iteración
{: .fs-9 }

La fabricación del **SonicGauntlet** representó un salto crítico en complejidad respecto a prototipos anteriores. Al pasar de un circuito en protoboard a un sistema integrado en un sustrato textil multicapa de alto gramaje (mezclilla), la física de los materiales, la gestión del espacio y el ruido electromagnético se convirtieron en los principales desafíos.

Esta bitácora documenta la metodología de diseño iterativo y la resolución de problemas aplicados tanto en la confección del hardware textil como en el firmware.

---

## Fase 1: Diseño Conceptual y Definición de Materiales

El objetivo era crear un *wearable* robusto y autónomo, con una estética que fusionara la tecnología con un estilo urbano y resistente.

### 1. Estética y Selección de Sustrato
Se definió una estética **Grunge Industrial / Rockero**.
* **Material Principal:** Se seleccionó **Mezclilla (Denim) de alto gramaje en color negro**.
    * *Razón Técnica:* Ofrece una excelente resistencia mecánica y durabilidad para soportar el peso de los componentes y la tensión del movimiento, además de permitir costuras firmes para el hilo conductivo.
* **Material de Contraste:** Se utilizó **tela gris de refuerzo** en el reverso para dar estructura y un acabado limpio, ocultando el cableado de potencia.

### 2. Morfología y Distribución
* **El Formato "Manga":** Se diseñó una manga que cubre desde el antebrazo hasta la muñeca, maximizando el área de visualización.
* **La Matriz de LEDs:** Se dispusieron 5 LEDs de alta luminosidad (azules y rojos) en la parte superior del antebrazo para una visibilidad óptima.
* **Gestión de Componentes:** Se diseñaron **bolsillos específicos de mezclilla** integrados en la prenda.
    * Un bolsillo superior aloja la unidad de bluetooth (MH-M28), manteniendo la señal protegida y sin ruidos significativos.
    * Un bolsillo/compartimento inferior aloja la batería LiPo, manteniendo el centro de gravedad bajo y facilitando su reemplazo.
    * En la capa de tela gris se pusieron el ESP32 C3 y el TP4056 junto con el resto de componentes para mantener las rutas cortas y manejables.

---

## Fase 2: Iteraciones de Firmware y DSP (El Reto del Ruido)

Al integrar la electrónica en la tela y alimentar el sistema, surgieron desafíos críticos relacionados con la interferencia eléctrica.

### Iteración 2.1: Transitorios de Radiofrecuencia
* **El Problema:** El módulo Bluetooth genera picos electromagnéticos al comunicarse. La transformada de Fourier (FFT) interpretaba estos picos como "música", interrumpiendo la animación de reposo (*Standby*) constantemente.
* **La Solución (DSP):** Se implementó un **Filtro de Confirmación (Debounce Analógico)** en el firmware. El sistema ahora exige que la energía del audio supere el umbral durante al menos dos ciclos de lectura consecutivos para considerarlo música real, ignorando eficazmente los transitorios de un solo ciclo.

### Iteración 2.2: Calibración con Energía Limpia
* **El Avance:** Al migrar de la alimentación USB (ruidosa) a la **batería LiPo de 3.7V** (corriente continua pura), el ruido de fondo se desplomó.
* **El Ajuste Fino:** Esto permitió reducir drásticamente los umbrales de filtrado (`deadband` a 30, `masterThreshold` a 800) y aumentar los multiplicadores de ganancia digital, logrando una sensibilidad excepcional incluso a volúmenes bajos.

---

## Fase 3: Iteraciones de Hardware e Integración E-Textil

El desafío físico fue asegurar que la energía fluyera correctamente a través de la mezclilla sin pérdidas.

### Iteración 3.1: El Problema de la Resistencia (Brownouts)
* **La Falla:** En las primeras pruebas, alimentar todo el sistema (ESP32 + Bluetooth) únicamente a través de hilo conductivo provocaba reinicios constantes.
* **El Diagnóstico:** La resistencia propia del hilo conductivo (~160Ω en el trayecto) causaba una caída de voltaje crítica durante los picos de consumo de la antena Bluetooth, dejando al procesador sin energía suficiente (*Brownout*).

### Iteración 3.2: Arquitectura de Ruteo Híbrido
Para solucionar esto sin sacrificar la estética de las costuras visibles:
* **Líneas de Potencia (VCC/GND):** Se instaló **cable de cobre multifilar delgado** oculto entre las capas de mezclilla y tela gris para transportar la corriente principal desde la batería con resistencia cero.
* **Líneas de Señal:** Se mantuvo el **hilo conductivo visible** sobre la mezclilla negra para las conexiones de los LEDs y la señal de audio analógica, ya que estas manejan corrientes mínimas.

### Iteración 3.3: Estabilización Capacitiva
Se añadieron capacitores electrolíticos estratégicos: uno en la entrada de poder para absorber picos de demanda, y otro en la línea de audio para acoplar la señal AC y bloquear la componente DC, protegiendo la entrada del microcontrolador.

---

## Fase 4: Manufactura y Acabado

El ensamble final requirió técnicas de costura y electrónica combinadas.

1.  **Confección:** Se cortaron y cosieron las piezas de mezclilla y tela gris, incluyendo la creación de los bolsillos a medida para los módulos y la batería.
2.  **Costura de Circuitos:** Se cosió el hilo conductivo siguiendo el patrón diseñado para los 5 LEDs, asegurando puntadas firmes a través de la mezclilla.
3.  **Sellado de Conexiones:** Debido a la naturaleza fibrosa de la mezclilla, se aplicó sellador (esmalte) en todos los nudos de hilo conductivo en los LEDs y la placa para evitar que se aflojaran o hicieran corto con fibras sueltas.
4.  **Integración Final:** Se alojaron los módulos en sus respectivos bolsillos, se conectaron los cables de potencia JST y se realizaron las pruebas de estrés mecánico para asegurar la fiabilidad del *wearable*.

[Ver Hardware](../hardware){: .btn .btn-outline .mr-2 }
[Ver Código](../codigo){: .btn .btn-outline .mr-2 }
[Ver Diseño](../diseno){: .btn .btn-outline .mr-2 }
[Inicio](../practica-2){: .btn .btn-primary } 