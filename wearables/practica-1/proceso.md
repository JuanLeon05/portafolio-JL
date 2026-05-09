---
layout: default
title: 3. Proceso
parent: P1 - Pulsera RockBand
grand_parent: Wearables
nav_order: 3
---

# Proceso de Manufactura e Iteraciones
{: .fs-9 }

La construcción física requirió integrar técnicas de manufactura textil con ensamble electrónico, asegurando que la pulsera debe poder colocarse y retirarse del cuerpo con facilidad.

---

## Fase 1: Preparación de Componentes (Hard-Modelling)
Las patas de los LEDs y resistencias son rígidas. Para integrarlas al textil sin causar daños se utilizó la técnica "Curly": con pinzas de punta redonda, se formaron anillas en los terminales para pasar la aguja y el hilo conductor. Posteriormente, se soldaron las resistencias a los LEDs.

<img src="../../../assets/img/practica-1/LedsCurly.JPG" alt="Leds Curly" width="49%">
<img src="../../../assets/img/practica-1/LedsYResistencia.JPG" alt="Leds y Resistencias Soldados" width="49%">

## Fase 2: Trazado y Patronaje
Se dibujó el esquema sobre la tela base, planificando la ruta exacta del hilo conductor para evitar cruces que generen cortocircuitos.

<img src="../../../assets/img/practica-1/TelaBase.jpeg" alt="Tela Base" width="100%">
<br><br>
<img src="../../../assets/img/practica-1/PatronCoseTela.jpeg" alt="Patrón 1" width="32%">
<img src="../../../assets/img/practica-1/PatronCoseTela2.jpeg" alt="Patrón 2" width="32%">
<img src="../../../assets/img/practica-1/PatronCoseTelaFinal.jpeg" alt="Patrón Final" width="32%">

## Fase 3: Costura e Integración
Se ejecutó la costura conductiva conectando la línea VCC (+) y GND (-) hacia los LEDs y los broches metálicos.

<img src="../../../assets/img/practica-1/CosiendoTela1.jpeg" alt="Cosiendo 1" width="49%">
<img src="../../../assets/img/practica-1/CosiendoTela2.jpeg" alt="Cosiendo 2" width="49%">
<br><br>
<img src="../../../assets/img/practica-1/LedsResistenciasEnTela.jpeg" alt="Leds en Tela" width="49%">
<img src="../../../assets/img/practica-1/EmbeberLedsTela.jpeg" alt="Embebiendo LEDs" width="49%">

El hilo conductor se cosió en contacto directo con la base metálica de los broches, operando como mecanismo de switch físico.
<img src="../../../assets/img/practica-1/VistaFinalDetras.jpeg" alt="Vista Trasera Final" width="100%">

## Fase 4: Pruebas e Iteraciones
Se comprobó la continuidad del circuito incluso antes de abrochar la pulsera para descartar falsos contactos o deshilaches en los nudos.

<img src="../../../assets/img/practica-1/PulseraAbiertaON.JPG" alt="Prueba pulsera abierta" width="100%">

---

[Ver Diseño](./diseno.md){: .btn .btn-outline } [Ver Evidencia](./evidencia.md){: .btn .btn-outline } [Inicio](../practica-1){: .btn .btn-primary }