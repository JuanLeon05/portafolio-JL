---
layout: default
title: Conclusiones
parent: P2 - SonicGauntlet
grand_parent: Wearables
nav_order: 7
---

# Reflexión Crítica y Aprendizajes
{: .fs-9 }

En esta sección presento un análisis crítico sobre la evolución entre el diseño conceptual y el resultado físico de este instrumento wearable.

---

## Análisis de la Integración Tecnológica
El mayor éxito de este proyecto fue lograr un divorcio funcional entre el procesamiento digital de alta frecuencia y las limitantes físicas de la manufactura textil. Comprender que no se podía forzar a un material resistivo (como el hilo conductivo) a transportar grandes corrientes de energía fue clave; la adopción de una arquitectura de ruteo híbrida salvó el proyecto de ser un prototipo inestable.

Desde la perspectiva del usuario, el salto de un simple encendido mecánico a una secuencia matemática generada por transformada de Fourier eleva el nivel de interacción de la prenda, cumpliendo sobradamente con el objetivo de provocar una experiencia sensorial.

## Oportunidades de Mejora
* **Gestión Térmica y de Espacio:** Aunque los bolsillos de mezclilla son funcionales, la acumulación de calor del módulo TP4056 al cargar la batería requiere un diseño de ventilación más eficiente para versiones futuras.
* **Escalabilidad del Código:** El algoritmo actual funciona perfectamente para 5 bandas, pero el microcontrolador tiene capacidad de sobra. Podría integrarse un módulo inercial (IMU) para que la respuesta lumínica no solo dependa del audio, sino también de la velocidad de los movimientos del brazo del usuario.

---

[Ver Evidencia](./evidencia.md){: .btn .btn-outline .mr-2 } [Inicio](../practica-2){: .btn .btn-primary }