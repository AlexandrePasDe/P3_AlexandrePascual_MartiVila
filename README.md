# P3_AlexandrePascual_MartiVila
Participants: Alexandre Pascual i Marti Frigola

# Informe de Laboratori: Servidor Web i Bluetooth en ESP32-S3

## Introducció

Es configurarà la placa com a un servidor web i s'habilitarà la comunicació via Bluetooth per a transmetre, en aquest cas, la temperatura del procesador en temps real.

El servidor web serà creat per ser accesible desde una red de Wifi local i la implementació Bluetooth transmitirà dades a un dispositiu mòbil.

---

## Servidor Web amb ESP32-S3

### Llibreries

La ESP32-S3 es connectara a un red local del laboratori anomenada Nautilus. S'usen les llibreries **WiFi.h** i **WebServer.h** per a controlar la connexió.

#### Codi Base

Estableix connexió Wifi i crea un servidor en el port 80.

```cpp
#include <WiFi.h>
#include <WebServer.h>
#include <Arduino.h>

// Informació de la red WiFi
const char* ssid = "Nautilus"; 
const char* password = "20000Leguas"; 

// Creació del servidor en el port 80
WebServer server(80);


void handle_root();

// Página feta amb HTML que es mostrarà al navegador
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

// Funció per controlar l'arrel del servidor
void handle_root() {
    server.send(200, "text/html", HTML);
}

// Configuració inicial del ESP32
void setup() {
    Serial.begin(115200);
    Serial.println("Conectando a WiFi...");

    WiFi.begin(ssid, password);
    
    // Esperar connexió WiFi
    while (WiFi.status() != WL_CONNECTED) {
        delay(1000);
        Serial.print(".");
    }

    Serial.println("\nWiFi conectado con éxito.");
    Serial.print("Dirección IP: ");
    Serial.println(WiFi.localIP());  // Muestra la IP asignada

    // Configuració del servidor
    server.on("/", handle_root);
    server.begin();
    Serial.println("Servidor HTTP iniciado.");
}

// Bucle principal del ESP32
void loop() {
    server.handleClient();
}

```

**LA resposta que hauría d'apareixer:**

```c++
Conectando a WiFi...
.
WiFi conectado con éxito.
Dirección IP: 192.168.50.119
Servidor HTTP iniciado.
```

##### Modificació del Codi

Haviem de modificar el codi HTML per fer-lo personalitzat, aquestes són les variacions que nosaltres vam implementar al codi: 

1. Canvi de colors de manera dinàmica al fons de la pàgina.
2. Un Sol que s'anava movent de a dalt abaix de la pàgina.
3. Un dinosaure creat amb CSS. (El CSS es un llenguatge de disseny web a les pàgines escrites amb HTML)

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

## Comunicació Bluetooth 

La idea principal es configurar la placa 'ESP32-S3' com a dispositu Bluetooth, cosa que permetrà la transmissió d'informació en temps real. 
En aquest cas es comunicarà amb un dispositiu mòbil i sabrem la temperatura del processador.
Vam utilitzar la aplicació 'BLE Scanner' i la llibreria 'NimBLE-Arduino'.


### Codi

```cpp
#include "BluetoothSerial.h"
#if !defined(CONFIG_BT_ENABLED) || !defined(CONFIG_BLUEDROID_ENABLED)
#error Bluetooth is not enabled! Please run `make menuconfig` to enable it
#endif

BluetoothSerial SerialBT;

void setup() {
    Serial.begin(115200);
    SerialBT.begin("ESP32test"); // Nom del dispositiu Bluetooth
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

### Visualització de dades en el mòbil

A la aplicació es mostraba en **temps real** la 'temperatura' del processador i d'aquesta manera es pot monitoreijar el rendiment tèrmic de la placa, però aquesta idea base es podria aplicar a moltes altres funcionalitats.


---

## Resum

Hem aconseguit crear una pàgina web, la qual estava personalitzada amb animacions en HTML i CSS, accesible desde una red Wifi local mitjançant la configuració de la ESP32-S3.
Hem establert una connexió Bluetooth entre la placa i el nostre telefon per a la transimissió de dades en temps real i monitoreig de la ESP32, la qual cosa obre portes a moltes funcionalitats diferents a la hora de voler extreure el màxim rendiment posible.


