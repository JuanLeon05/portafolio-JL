---
layout: default
title: 3. Proceso
parent: P1 - Pulsera RockBand
grand_parent: Wearables
nav_order: 3
---

# Proceso de Desarrollo: Diseño e Ingeniería
{: .fs-9 }

El desarrollo de la **Pulsera RockBand** siguió una metodología híbrida, integrando el pensamiento de diseño (Design Thinking) con la validación ingenieril. A continuación se detalla el flujo de trabajo desde el boceto hasta el prototipo funcional.

---

## Fase 1: Diseño y Experiencia de Usuario (UX)

Antes de tocar cualquier componente electrónico, se definieron los requerimientos de uso y estética en colaboración con el área de Diseño Textil.

### Perfil de Usuario y Contexto
* **Contexto:** Conciertos, festivales de música y vida nocturna.
* **Necesidad:** Un accesorio que destaque visualmente en la oscuridad pero que sea discreto cuando no está en uso.
* **Interacción (Gestos):** Se decidió que el usuario no debería buscar un botón pequeño e incómodo. La propia acción de **ponerse la pulsera** debe ser el detonante del encendido.

### Decisiones Estéticas
1.  **Paleta de Color:** Se seleccionó tela negra de algodón para maximizar el contraste con la luz de los LEDs.
2.  **Distribución:** Los 5 LEDs se alinearon horizontalmente para cubrir la muñeca visible.
3.  **Ocultamiento:** Se diseñó un patrón de "sándwich" (doble capa) para que el cableado y las resistencias quedaran invisibles, protegiendo la piel del usuario de roces con el metal.

---

## Fase 2: Validación Electrónica

Una vez definido el diseño, se procedió a validar la viabilidad técnica.

1.  **Solución de Energía:** Se optó por una alimentación externa (Power Bank / Fuente) para la demostración, diseñando pads de conexión en cobre expuesto.
2.  **Prueba de Continuidad:** Se verificó la resistencia del hilo conductor (aprox. 10 $\Omega$/metro) para asegurar que no causara una caída de voltaje significativa.

---

## Fase 3: Manufactura y Costura (Hard-Modelling)

La construcción física requirió técnicas manuales precisas para integrar lo rígido (LEDs) con lo flexible (Tela).

### Preparación de Componentes
Las patas de los LEDs y resistencias son rectas y no se pueden coser, se soldaron las resistencias a los LEDs.
* **Técnica "Curly":** Con pinzas de punta redonda, se formaron anillas (bucles) en los terminales de cada componente. Esto permite pasar la aguja e hilo conductor a través de ellas, asegurando un contacto mecánico firme y duradero.

### Integración del Circuito
1.  **Trazado:** Se dibujó el esquema sobre la tela con tiza de sastre.
2.  **Costura Conductiva:**
    * **Línea VCC (+):** Conecta desde el pad de cobre positivo -> Resistencias -> Ánodos de los LEDs -> Broche Macho.
    * **Línea GND (-):** Conecta desde el pad de cobre negativo -> Cátodos de los LEDs -> Broche Hembra.
3.  **Mecanismo de Switch:** El hilo conductor se cosió en contacto directo con la base metálica de los broches. Al cerrar la pulsera, el broche macho toca al hembra, cerrando el circuito físicamente.

---

## Fase 4: Pruebas Finales y Acabados

* **Aislamiento:** Se aplicó esmalte transparente en los nudos finales del hilo conductor para evitar que se deshilachen y causen cortocircuitos accidentales.
* **Prueba de Estrés:** Se sometió la pulsera a movimientos de flexión y torsión simulando el baile en un concierto para verificar que la luz no parpadeara (falsos contactos).

---

[Ver cálculos](./calculos.md){: .btn .btn-outline } [Ver Hardware](./hardware.md){: .btn .btn-outline } [Ver Diseño](./diseno.md){: .btn .btn-outline } [Practica 1](../practica-1){: .btn .btn-outline } 