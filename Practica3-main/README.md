# Practica3
Participants: Alexandre Pascual / Marti Vila
# Informe de Laboratorio: Servidor Web y Bluetooth en ESP32-S3

## Introducción

Este laboratorio tiene como objetivo explorar las capacidades de la placa **ESP32-S3**, configurándola como un **servidor web** y habilitando la **comunicación Bluetooth** para transmitir información, específicamente la temperatura del procesador en tiempo real.

El trabajo se divide en **dos partes**:

1. Creación de un **servidor web** accesible desde una red WiFi local.
2. Implementación de **Bluetooth** para la transmisión de datos a un dispositivo móvil.

---

## Parte 1: Servidor Web con ESP32-S3

### Desarrollo

Para este apartado, el ESP32 se conecta a la red WiFi de la clase y aloja una página web accesible desde cualquier dispositivo en la misma red. Se utilizó la librería **WiFi.h** y **WebServer.h** para manejar la conexión.

#### Código Inicial

El siguiente código establece la conexión WiFi y crea un servidor web en el puerto 80:

```cpp
#include <WiFi.h>
#include <WebServer.h>
#include <Arduino.h>

// Credenciales de la red WiFi
const char* ssid = "Nautilus"; 
const char* password = "20000Leguas"; 

// Creación del servidor en el puerto 80
WebServer server(80);

// Prototipo de la función handle_root
void handle_root();

// Página HTML que se mostrará en el navegador
String HTML = "<!DOCTYPE html>\
<html>\
<head>\
    <meta charset='UTF-8'>\
    <meta name='viewport' content='width=device-width, initial-scale=1.0'>\
    <title>ESP32 WebServer</title>\
    <style>\
        body { font-family: Arial, sans-serif; text-align: center; margin: 50px; }\
        h1 { color: #333; }\
    </style>\
</head>\
<body>\
    <h1>Mi Primera Página con ESP32 - Station Mode &#128522;</h1>\
</body>\
</html>";

// Función para manejar la raíz del servidor
void handle_root() {
    server.send(200, "text/html", HTML);
}

// Configuración inicial del ESP32
void setup() {
    Serial.begin(115200);
    Serial.println("Conectando a WiFi...");

    WiFi.begin(ssid, password);
    
    // Esperar conexión WiFi
    while (WiFi.status() != WL_CONNECTED) {
        delay(1000);
        Serial.print(".");
    }

    Serial.println("\nWiFi conectado con éxito.");
    Serial.print("Dirección IP: ");
    Serial.println(WiFi.localIP());  // Muestra la IP asignada

    // Configuración del servidor
    server.on("/", handle_root);
    server.begin();
    Serial.println("Servidor HTTP iniciado.");
}

// Bucle principal del ESP32
void loop() {
    server.handleClient();
}

```

**La respuesta que debería salir por pantalla es:**

```c++
Conectando a WiFi...
.
WiFi conectado con éxito.
Dirección IP: 192.168.50.119
Servidor HTTP iniciado.
```

##### Modificación del Código

Luego de esto, el ejercicio pedía que cambiaramos la pagina web, por lo que decidiomos incluir algunas variaciones del codigo HTML personalizadas.
Algunas de las variaciones incluyen:

- *Cambio dinámico de colores de fondo.*
- *Un sol que se mueve de arriba a abajo.*
- *Un dinosaurio creado con CSS. (El CSS es un lenguaje de diseño web que permite dar estilo y diseño a las páginas web escritas en HTML)*

```cpp
void handle_root() {
  String HTML = "<!DOCTYPE html>\
  <html>\
  <head>\
      <meta charset='UTF-8'>\
      <meta name='viewport' content='width=device-width, initial-scale=1.0'>\
      <title>ESP32 WebServer</title>\
      <style>\
          body {\
              font-family: Arial, sans-serif;\
              text-align: center;\
              margin: 50px;\
              animation: changeBackground 10s infinite;\
              position: relative;\
          }\
          h1 {\
              color: #4CAF50;\
              animation: fadeIn 3s ease-in-out;\
          }\
          .dinosaurio {\
              position: relative;\
              width: 150px;\
              height: 100px;\
              background-color: #4CAF50;\
              border-radius: 10px;\
              margin: 20px auto;\
          }\
          .dinosaurio .cabeza {\
              position: absolute;\
              top: -10px;\
              left: 20px;\
              width: 50px;\
              height: 50px;\
              background-color: #2c6b31;\
              border-radius: 50%;\
          }\
          .dinosaurio .piernas {\
              position: absolute;\
              bottom: -10px;\
              left: 25px;\
              width: 100px;\
              height: 20px;\
              background-color: #2c6b31;\
              border-radius: 5px;\
          }\
          .dinosaurio .cola {\
              position: absolute;\
              bottom: 10px;\
              right: -40px;\
              width: 40px;\
              height: 15px;\
              background-color: #2c6b31;\
              border-radius: 15px;\
              transform: rotate(40deg);\
          }\
          .sun {\
              position: absolute;\
              bottom: 10px;\
              left: 50%;\
              transform: translateX(-50%);\
              width: 90px;\
              height: 90px;\
              background-color: #FFFF20;\
              border-radius: 50%;\
              animation: sunRiseAndSet 10s infinite;\
          }\
          @keyframes fadeIn {\
              0% { opacity: 0; }\
              100% { opacity: 1; }\
          }\
          @keyframes changeBackground {\
              0% { background-color: #ffb3b3; }\
              10% { background-color: #00FFFF; }\
              20% { background-color: #32CD32; }\
              30% { background-color: #ffcc99; }\
              40% { background-color: #663399; }\
              50% { background-color: #99ccff; }\
              60% { background-color: #FFFF00; }\
              70% { background-color: #c2ff99; }\
              80% { background-color: #FF8C00; }\
              90% { background-color: #DB7093; }\
              100% { background-color: #ffb3b3; }\
          }\
          @keyframes sunRiseAndSet {\
              0% { bottom: -1000px; }\
              10% { bottom: 1000px; }\
              20% { bottom: -1000px; }\
              30% { bottom: 1000px; }\
              40% { bottom: -1000px; }\
              50% { bottom: 1000px; }\
              60% { bottom: -1000px; }\
              70% { bottom: 1000px; }\
              80% { bottom: -1000px; }\
              90% { bottom: 1000px; }\
              100% { bottom: -1000px; }\
          }\
      </style>\
  </head>\
  <body>\
      <h1>¡Un Dinosaurio en la ONU! 🦖🌍</h1>\
      <div class='dinosaurio'>\
          <div class='cabeza'></div>\
          <div class='piernas'></div>\
          <div class='cola'></div>\
      </div>\
      <div class='sun'></div>\
      <p>¡Bienvenido a mi página web con ESP32! Hecho por Alexandre y Martí 😎 </p>\
  </body>\
  </html>";
  
  server.send(200, "text/html", HTML);  // Enviar la respuesta con el HTML y estilo
}
```

---

## Parte 2: Comunicación Bluetooth con ESP32-S3

El objetivo de esta segunda parte es configurar la placa **ESP32-S3** como un dispositivo Bluetooth para permitir la transmisión de datos en tiempo real a un teléfono móvil.
Como hubo algunos problemas con la placa ESP32-S3, el profesor nos recomendó usar los siguientes recursos para copletar el laboratorio:
- **Aplicación BLE Scanner** 
- **Librería NimBLE-Arduino** 
Con estas herramientas nos ha sido posible terminar la practica.

### Desarrollo

El siguiente código configura el ESP32 como un **dispositivo Bluetooth serie**, permitiendo recibir y enviar datos desde el móvil:

```cpp
#include "BluetoothSerial.h"
#if !defined(CONFIG_BT_ENABLED) || !defined(CONFIG_BLUEDROID_ENABLED)
#error Bluetooth is not enabled! Please run `make menuconfig` to enable it
#endif

BluetoothSerial SerialBT;

void setup() {
    Serial.begin(115200);
    SerialBT.begin("ESP32test"); // Nombre del dispositivo Bluetooth
    Serial.println("The device started, now you can pair it with Bluetooth!");
}

void loop() {
    if (Serial.available()) {
        SerialBT.write(Serial.read());
    }
    if (SerialBT.available()) {
        Serial.write(SerialBT.read());
    }
    delay(20);
}
```

### Visualización de Datos en el Móvil

Al conectarse con la aplicación **BLE Scanner**, se visualizava en *tiempo real* la **temperatura** del procesador de la placa. Esta funcionalidad permite monitorear el rendimiento térmico del ESP32-S3.

---

## Conclusiones

- Se logró configurar el **ESP32-S3** como un **servidor web**, permitiendo el acceso a una página personalizada desde una red WiFi.
- Se implementaron **animaciones en HTML y CSS**, mejorando la estética y la interactividad de la página.
- Se habilitó la **conexión Bluetooth** del ESP32-S3, permitiendo la transmisión de información a un teléfono móvil.
- Se visualizó en tiempo real la **temperatura del procesador**, lo que puede ser útil para el monitoreo del rendimiento del ESP32.
