---
layout: default
title: 2. Metodología
parent: Proyecto Final
grand_parent: Wearables
nav_order: 2
---

# Arquitectura Electrónica e Ingeniería de Sistemas
{: .fs-9 }

El sistema requería un alto grado de sincronización entre dos extremidades independientes, por lo que se diseñó una arquitectura de procesamiento distribuido (Master-Slave) operando sobre una red inalámbrica de latencia ultra baja.

---

## Distribución Biométrica de Componentes
El layout de los componentes se ubicó estratégicamente para optimizar su funcionamiento técnico sin entorpecer al músico:
* **Interfaz de Control:** Pantalla OLED SSD1306 y tres botones de navegación ubicados en el antebrazo de la manga maestra, permitiendo una lectura e interacción sencilla.
* **Actuadores Visuales:** LEDs (NeoPixels) ubicados directamente sobre la línea de los nudillos para indicar qué dedo debe accionar la tecla.
* **Sensores de Postura:** Módulos MPU6050 (Acelerómetro/Giroscopio) situados cerca de la muñeca para monitorear el ángulo de la mano respecto al teclado.
* **Actuadores Hápticos:** Motores vibradores controlados por drivers DRV2605, proporcionando retroalimentación táctil de distintas intensidades según la nota.

## Topología de Comunicación Inalámbrica
Para evitar el uso de cables restrictivos entre ambos brazos, se implementaron dos protocolos simultáneos:
1.  **ESP-NOW (Capa Física):** Protocolo de comunicación punto a punto directo de Espressif. La Manga Maestra (izquierda) envía arreglos de datos `struct` a la Manga Esclava (derecha) con la instrucción exacta de tiempo, color y vibración, logrando una latencia casi nula.
2.  **Bluetooth Low Energy (BLE):** La manga maestra habilita un servidor BLE (`4fafc201-1fb5-459e-8fcc-c5c9c331914b`) para recibir comandos de navegación remotos desde una aplicación móvil externa, permitiendo al tutor o maestro controlar la lección.

## Máquina de Estados y Lógica de Sincronización
El sistema operativo de la manga maestra gestiona el flujo de la lección mediante estados (`ESTADO_MENU`, `ESTADO_VELOCIDAD`, `ESTADO_WAIT_IMU`, `ESTADO_PLAYING`). 

Destaca el **Filtro de Sincronización Postural (`manoHorizontal`)**: Antes de iniciar la reproducción visual de una partitura, ambas mangas exigen que el usuario coloque sus dos manos completamente planas y horizontales sobre el teclado (eje Z de aceleración > 7.0) durante al menos 2 segundos. Esto garantiza que el alumno comience con la técnica y postura correctas.

---

[Ver Manufactura](./manufactura.md){: .btn .btn-outline .mr-2 } [Inicio](../proyecto-final){: .btn .btn-primary }