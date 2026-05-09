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
Las patas de los LEDs y resistencias son rígidas. Para integrarlas al textil sin causar daños:
* **Técnica "Curly":** Con pinzas de punta redonda, se formaron anillas (bucles) en los terminales de cada componente. Esto permite pasar la aguja e hilo conductor a través de ellas.

> **Evidencia:** [Inserta foto de los LEDs con los curlies]

## Fase 2: Costura e Integración
1.  **Trazado:** Se dibujó el esquema sobre la tela, planificando la ruta del hilo conductor para evitar cruces que generen cortocircuitos.
2.  **Costura Conductiva:**
    * **Línea VCC (+):** Conecta desde el pad de cobre positivo hacia las resistencias, ánodos y el broche macho.
    * **Línea GND (-):** Conecta desde el pad de cobre negativo hacia los cátodos y el broche hembra.
3.  **Mecanismo de Switch:** El hilo conductor se cosió en contacto directo con la base metálica de los broches.

> **Evidencia:** [Inserta fotos del reverso de la tela mostrando las costuras]

## Fase 3: Pruebas e Iteraciones
Durante el proceso se realizaron pruebas para garantizar el ajuste, flexibilidad y comodidad en la muñeca.

* **Aislamiento:** Se aplicó esmalte transparente en los nudos finales del hilo conductor para evitar deshilaches.
* **Validación:** Se comprobó que los 5 LEDs encienden correctamente al abrochar la pulsera. Se evaluó que la pulsera fuera cómoda, flexible y adecuada para la muñeca.

> **Evidencia:** [Inserta video de la prueba de flexibilidad y encendido]

---

[Ver Diseño](./diseno.md){: .btn .btn-outline } [Ver Evidencia](./evidencia.md){: .btn .btn-outline } [Inicio](../practica-1){: .btn .btn-primary }