---
layout: default
title: 3. Manufactura y Firmware
parent: Proyecto Final
grand_parent: Wearables
nav_order: 3
---

# Manufactura, Integración y Programación
{: .fs-9 }

La fase de fabricación fusionó el ruteo de cables flexibles de cobre revestido con el montaje sobre la gabardina stretch. A nivel de software, el código en C++ gestiona estructuras de datos musicales complejas.

---

## Implementación de Firmware (Fragmentos Clave)

Se diseñaron algoritmos para pre-cargar partituras completas (como "Twinkle Star", "Fur Elise") utilizando arreglos de estructuras (`struct Nota`) que dictan el tiempo en milisegundos, el dedo correspondiente y la alteración musical (sostenido/bemol) de cada pulso.

### Manga Maestra (Controlador y Servidor ESP-NOW)
Encargada del menú OLED, procesamiento de BLE y cálculos de factor de velocidad dinámicos para ralentizar el tempo de aprendizaje (50%, 100%, 125%).

```cpp
// Lógica de Sincronización Bi-Manual (Fragmento)
case ESTADO_WAIT_IMU: {
  bool yoHorizontal = manoHorizontal(); // MPU6050 Maestra
  
  if (yoHorizontal && esclavaHorizontal) { // Validación cruzada
    if (millis() - tiempoInicioHorizontal >= 2000) {
      estadoActual = ESTADO_COUNTDOWN;
    }
  } else {
    tiempoInicioHorizontal = millis(); // Reinicia si se rompe la postura
  }
  break;
}
```


### Manga Esclava (Receptor y Renderizado)
Su función es puramente responsiva. Para evitar sobrecalentamientos térmicos del regulador de voltaje pegado al cuerpo del usuario, se aplicó una técnica de Underclocking, reduciendo la frecuencia del procesador a 80MHz y limitando el brillo de la matriz NeoPixel.

```cpp
// Decodificación de Paquetes y Colorimetría (Fragmento)
void OnDataRecv(const esp_now_recv_info_t *info, const uint8_t *incomingData, int len) {
  if (len == sizeof(Nota)) {
    Nota notaRecibida;
    memcpy(&notaRecibida, incomingData, sizeof(notaRecibida));

    // Lógica de Mezcla (Diccionario Visual)
    int r = C_BASE, g = C_BASE, b = C_BASE; 
    if (notaRecibida.sostenido) { b = C_FULL; }               // Azul
    if (notaRecibida.bemol)     { r = C_FULL; g = C_FULL; }   // Amarillo
    if (notaRecibida.arriba)    { r = C_FULL; }               // Rojo
    
    tira.setPixelColor(nudilloIdx, tira.Color(min(r, 255), min(g, 255), min(b, 255)));
    tira.show();
    haptic.setWaveform(0, 1); haptic.setWaveform(1, 14); haptic.go(); // Feedback táctil
  }
}
```

[Ver Evidencia](./evidencia.md){: .btn .btn-outline .mr-2 } [Inicio](../proyecto-final){: .btn .btn-primary }