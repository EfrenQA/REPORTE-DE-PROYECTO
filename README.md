
# REPORTE DE PROYECTO FINAL MODULO 05

(INTERFACES PERSONA-MÁQUINA HMI)

Ponente

MTRO. DIEGO JASSO MIRANDA 

_________________________________________________________________________________________________________________________

### INTEGRANTES 
- AGUILAR LAGUNAS RAUL
- HERNANDEZ M. VICENTE ALEXANDER
- MEJIA BARRIOS DAVID JESUS 
- QUIROZ AYALA EFREN DAVID
- TORRES ANAYA HELEN XIMENA
- TORRES MONTES LIZBETH MAYTE
  
_________________________________________________________________________________________________________________________

NOMBRE DEL PROYECTO

# SISTEMA AUTOMATIZADO DE RIEGO Y CONTROL AMBIENTAL PARA SUCULENTAS

- INTRODUCCIÓN
  
El presente proyecto tiene como finalidad el diseño de un sistema automatizado de riego y control ambiental para suculentas, empleando tecnologías de automatización industrial como sensores, actuadores, plataformas IoT, y herramientas de simulación como Wokwi y Node-RED.

Se buscan soluciones capaces de mantener condiciones estables y eficientes para el crecimiento vegetal. En particular, las suculentas demandan parámetros específicos de temperatura, humedad y luminosidad, por lo que la automatización resulta un recurso fundamental para garantizar su bienestar y reducir la intervención humana
Se adjuntan los parametros que se tomaran en cuenta para la realizacion del proyecto: 

  ![](https://github.com/EfrenQA/REPORTE-DE-PROYECTO/blob/main/PARAMETROS.jpeg?raw=true)
  
  _________________________________________________________________________________________________________________________

- OBJETIVO
  
Desarrollar y simular un sistema automatizado que controle el riego, la temperatura, la humedad ambiental y la iluminación necesaria para plantas suculentas, utilizando un ESP32, sensores DHT22, sensor ulrasonico, fotoresistencias (LDR) y actuadores como ventiladores, bombas de agua y servomotores, integrados a un panel de monitoreo en Node-RED.

![](https://github.com/EfrenQA/REPORTE-DE-PROYECTO/blob/main/inverna.jpeg?raw=true)

_________________________________________________________________________________________________________________________

- MATERIAL 
  - Sensor DHT22 (Temperatura y Humedad)
  - Sensor Ultrasónico HC-SR04
  - Fotoresistencia (Control de iluminación natural)
  - Resistencias
  - Actuadores
  - Led amarillo ( simula calefacción regula temperatura).
  - Led blanco ( simula ventilación regula temperatura).
  - Led azul ( Bomba de agua: activa el riego).
  - Servomotor: abre/cierra ventanas.
  - Display: indica estados del sistema.
 
  ![](https://github.com/EfrenQA/REPORTE-DE-PROYECTO/blob/main/materiales.jpeg?raw=true)
_________________________________________________________________________________________________________________________
-DESARROLLO 

# Diseño del Sistema en Wokwi

El proyecto se desarrolla en la plataforma Wokwi, utilizando un ESP32 como controlador principal.

En el diagrama se integran:
- DHT22 conectado a un pin digital del ESP32
- Sensor ultrasónico conectado a pines TRIG y ECHO
- LDR conectado a entrada analógica
- Actuadores( LEDS ) conectados a salidas PWM y digitales
- Comunicación serial para enlazar con Node-RED
La simulación permite probar condiciones extremas, verificar la lógica de control y depurar errores antes de implementar físicamente el sistema.

![](https://github.com/EfrenQA/REPORTE-DE-PROYECTO/blob/main/CONEXIONES.jpeg?raw=true)

# Se adjuntan las bibliotecas necesarias para el funcionamiento del programa 

  ![](https://github.com/EfrenQA/REPORTE-DE-PROYECTO/blob/main/LIBRERIAS.jpeg?raw=true)
  
## Código 

Se adjunta el siguiente código, el cual nos permitirá tener control del sistema y poder vincularlo con node-red 


```
#include <WiFi.h>
#include <PubSubClient.h>
#include "DHTesp.h"
#include <ESP32Servo.h>
#include <ArduinoJson.h>
const int Trigger = 4;   //Pin digital 4 para el Trigger del sensor
const int Echo = 5;   //Pin digital 5 para el Echo del sensor
const int DHT_PIN = 15;
const int led1 = 17;
const int led2 = 16;
const int led3 = 0;
int pinventana = 14;
int LUZ = 34;
DHTesp dhtSensor;
Servo ventana;
#define BUILTIN_LED 2
int pos;
const char* ssid = "Wokwi-GUEST";
const char* password = "";
const char* mqtt_server = "3.121.19.141";
String username_mqtt="educatronicosiot";
String password_mqtt="12345678";

WiFiClient espClient;
PubSubClient client(espClient);
unsigned long lastMsg = 0;
#define MSG_BUFFER_SIZE  (50)
char msg[MSG_BUFFER_SIZE];
int value = 0;

void setup_wifi() {

  delay(10);
  // We start by connecting to a WiFi network
  Serial.println();
  Serial.print("Connecting to ");
  Serial.println(ssid);

  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  randomSeed(micros());

  Serial.println("");
  Serial.println("WiFi connected");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
}

void callback(char* topic, byte* payload, unsigned int length) {
  Serial.print("Message arrived [");
  Serial.print(topic);
  Serial.print("] ");
  for (int i = 0; i < length; i++) {
    Serial.print((char)payload[i]);
  }
  Serial.println();

  // Switch on the LED if an 1 was received as first character
  if ((char)payload[0] == '1') {
    digitalWrite(BUILTIN_LED, LOW);   
    // Turn the LED on (Note that LOW is the voltage level
    // but actually the LED is on; this is because
    // it is active low on the ESP-01)
  } else {
    digitalWrite(BUILTIN_LED, HIGH);  
    // Turn the LED off by making the voltage HIGH
  }

}

void reconnect() {
  // Loop until we're reconnected
  while (!client.connected()) {
    Serial.print("Attempting MQTT connection...");
    // Create a random client ID
    String clientId = "ESP8266Client-";
    clientId += String(random(0xffff), HEX);
    // Attempt to connect
    if (client.connect(clientId.c_str(), username_mqtt.c_str() , password_mqtt.c_str())) {
      Serial.println("connected");
      // Once connected, publish an announcement...
      client.publish("outTopic", "hello world");
      // ... and resubscribe
      client.subscribe("inTopic");
    } else {
      Serial.print("failed, rc=");
      Serial.print(client.state());
      Serial.println(" try again in 5 seconds");
      // Wait 5 seconds before retrying
      delay(5000);
    }}}
void setup() {
  Serial.begin(115200);//iniciailzamos la comunicación
  pinMode(Trigger, OUTPUT); //pin como salida
  pinMode(Echo, INPUT);  //pin como entrada
  digitalWrite(Trigger, LOW);//Inicializamos el pin con 0
  dhtSensor.setup(DHT_PIN, DHTesp::DHT22);
  pinMode(led1, OUTPUT);
  pinMode(led2, OUTPUT);
  pinMode(led3, OUTPUT);
ventana.attach(pinventana, 500 , 2500);
 pinMode(BUILTIN_LED, OUTPUT);     // Initialize the BUILTIN_LED pin as an output
  setup_wifi();
  client.setServer(mqtt_server, 1883);
  client.setCallback(callback);
}

void loop()
{

  long t; //timepo que demora en llegar el eco
  long d; //distancia en centimetros

  digitalWrite(Trigger, HIGH);
  delayMicroseconds(10);          //Enviamos un pulso de 10us
  digitalWrite(Trigger, LOW);
  
  t = pulseIn(Echo, HIGH); //obtenemos el ancho del pulso
  d = t/59;             //escalamos el tiempo a una distancia en cm
   int valor = analogRead(LUZ);
   Serial.print("LUZ: ");
   Serial.print(valor);
   Serial.println();
  Serial.print("Distancia: ");
  Serial.print(d);      //Enviamos serialmente el valor de la distancia
  Serial.print("cm");
  Serial.println();
    delay(2000);

    TempAndHumidity  data = dhtSensor.getTempAndHumidity();
  Serial.println("Temp: " + String(data.temperature, 1) + "°C");
  Serial.println("Humidity: " + String(data.humidity, 1) + "%");
  Serial.println("---");
     delay(2000);
if (data.temperature>= 28)
{
  digitalWrite(led1, LOW); //Luzincandecente 
}
else if(data.temperature<=15) 
{
  digitalWrite(led1, HIGH); //Luz incandecente
 
  if (data.humidity>=40)
{
  digitalWrite(led2, LOW);//Sistema de riego

}
else if(data.humidity<=22) 
{
  digitalWrite(led2, HIGH); //Sistema de riego
       //Hacemos una pausa de 100ms
}
}
if (data.temperature>= 29)
{
  digitalWrite(led3, HIGH);//Ventilador
}
else if(data.temperature<=20) 
{
  digitalWrite(led3, LOW);
}
static bool ventanaAbierta = false;  // memoriza estado anterior

if (valor >= 2500 && ventanaAbierta == false) {
    for (pos = 0; pos <= 180; pos += 1) {
        ventana.write(pos);
        delay(10);
    }
    ventanaAbierta = true;   // marca como abierta
}

else if (valor <= 2499 && ventanaAbierta == true) {
    for (pos = 180; pos >= 0; pos -= 1) {
        ventana.write(pos);
        delay(10);
    }
    ventanaAbierta = false;  // marca como cerrada
}
 if (!client.connected()) {
    reconnect();
  }
  client.loop();

  unsigned long now = millis();
  if (now - lastMsg > 2000) {
    lastMsg = now;
    //++value;
    //snprintf (msg, MSG_BUFFER_SIZE, "hello world #%ld", value);

    StaticJsonDocument<128> doc;

    doc["DEVICE"] = "Raul Aguilar";
    //doc["Anho"] = 2022;
    //doc["Empresa"] = "Educatronicos";
    doc["TEMPERATURA"] = String(data.temperature, 1);
    doc["HUMEDAD"] = String(data.humidity, 1);
   doc["DISTANCIA"] = String(d);
   doc["VALOR"]= String(valor); 
    String output;
    
    serializeJson(doc, output);

    Serial.print("Publish message: ");
    Serial.println(output);
    Serial.println(output.c_str());
    client.publish("RaulA", output.c_str());
}}
```
# Node-Red

Node-RED se utiliza como:
- Panel de monitoreo de temperatura, humedad, luz y nivel de agua (distancia).

  ![](https://github.com/EfrenQA/REPORTE-DE-PROYECTO/blob/main/node-red.jpeg?raw=true)

- Vizualizacion de planta suculeta a traves la configuracion de node-red en el dashboard para podeer obtener la imagen

  ![](https://github.com/EfrenQA/REPORTE-DE-PROYECTO/blob/main/node-red-2.jpeg?raw=true)
  
# Funcionamiento general Wokwi, Node-RED

1.	El ESP32 realiza la lectura de todos los sensores.
2.	La lógica compara los valores con los rangos establecidos:
- Si la suculenta necesita agua y hay suficiente nivel → activa la bomba.
- Si la temperatura está alta → activa ventilador.
- Si la luz es insuficiente → mueve servo para abrir ventana.
3.	Node-RED recibe todos los datos y los muestra en dashboards.
4.	El usuario puede modificar parámetros o forzar acciones desde Node-RED.
5.	El sistema opera de forma continua y autónoma.

# El Dashboard de Node-RED (Sistema Automático de Suculenta)
1. Visualización General
Imagen de la planta suculenta 
2. Lecturas en tiempo real desde el ESP32
   - Temperatura ambiente
   - Humedad del suelo
   - Nivel del tanque de agua
   - Intensidad de luz 
   - Humedad ambiental 

  ![](https://github.com/EfrenQA/REPORTE-DE-PROYECTO/blob/main/Fucionamiento.jpeg?raw=true)
  
  
# Conclusión
El proyecto demuestra la aplicación de técnicas de automatización industrial en un entorno agrícola controlado, integrando sensores, actuadores y herramientas IoT.
La simulación en Wokwi y la visualización en Node-RED permiten validar el funcionamiento antes de implementar un sistema físico real.
Además, la solución es escalable y reutilizable en sistemas de riego más grandes, invernaderos o cultivos automatizados.
