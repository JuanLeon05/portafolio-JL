---
layout: default
title: 5. Conclusiones
parent: Proyecto Final
grand_parent: Wearables
nav_order: 5
---

# Reflexión Crítica sobre el Proyecto Final
{: .fs-9 }

**Melody Key** demuestra que la convergencia entre la ingeniería mecatrónica y el diseño de modas puede generar herramientas de alto valor pedagógico. 

A nivel técnico, el mayor logro fue garantizar un flujo de datos constante entre ambos brazos utilizando `ESP-NOW` sin generar bloqueos lógicos en la matriz LED, logrando que la experiencia del usuario sea fluida y sincronizada a la perfección con el feedback de los motores hápticos. El uso del acelerómetro MPU6050 para forzar una postura correcta antes de iniciar la canción añade un candado de calidad a la enseñanza musical.

Desde la perspectiva del diseño, el uso de capas contrastantes (gabardina estructurada vs. terciopelo difusor) resolvió el eterno problema de los wearables: la rigidez del hardware lastimando al usuario. 

**Áreas de Escalabilidad:**
* El sistema actual requiere compilar las canciones (partituras) en el firmware. Una futura iteración podría aprovechar la conectividad BLE existente para descargar archivos MIDI directamente desde un smartphone hacia la memoria flash del microcontrolador, expandiendo el repertorio musical al infinito sin necesidad de reprogramación.

---

[Inicio](../proyecto-final){: .btn .btn-primary }