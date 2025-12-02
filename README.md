# REPORTE DE PROYECTO FINAL MODULO 04
## SISTEMA AUTOMATIZADO DE RIEGO Y CONTROL AMBIENTAL PARA SUCULENTAS

- INTRODUCCIÓN 
El presente proyecto tiene como finalidad el diseño de un sistema automatizado de riego y control ambiental para suculentas, empleando tecnologías de automatización industrial como sensores, actuadores, plataformas IoT, y herramientas de simulación como Wokwi y Node-RED.
Se buscan soluciones capaces de mantener condiciones estables y eficientes para el crecimiento vegetal. En particular, las suculentas demandan parámetros específicos de temperatura, humedad y luminosidad, por lo que la automatización resulta un recurso fundamental para garantizar su bienestar y reducir la intervención humana
Se adjuntan los parametros que se tomaran en cuenta para la realizacion del proyecto: 
  ![]( ADJUNTAR IMAGEN)
- OBJETIVO 
Desarrollar y simular un sistema automatizado que controle el riego, la temperatura, la humedad ambiental y la iluminación necesaria para plantas suculentas, utilizando un ESP32, sensores DHT22, sensor ulrasonico, fotoresistencias (LDR) y actuadores como ventiladores, bombas de agua y servomotores, integrados a un panel de monitoreo en Node-RED.

- MATERIAL 
  -Sensor DHT22 (Temperatura y Humedad)
  -Sensor Ultrasónico HC-SR04
  -Sensor LDR (Control de iluminación natural)
  -Actuadores
•	Ventilador: regula temperatura.
•	Bomba de agua: activa el riego.
•	Servomotor: abre/cierra ventanas.
•	Display: indica estados del sistema.

# Diseño del Sistema en Wokwi
El proyecto se desarrolla en la plataforma Wokwi, utilizando un ESP32 como controlador principal.
En el diagrama se integran:
•	DHT22 conectado a un pin digital del ESP32
•	Sensor ultrasónico conectado a pines TRIG y ECHO
•	LDR conectado a entrada analógica
•	Actuadores conectados a salidas PWM y digitales
•	Comunicación serial para enlazar con Node-RED
La simulación permite probar condiciones extremas, verificar la lógica de control y depurar errores antes de implementar físicamente el sistema.

##Código 

#Node-Red
Node-RED se utiliza como:
Panel de monitoreo de temperatura, humedad, luz y nivel de agua.

  ![]( ADJUNTAR IMAGEN)
# Conclusión
El proyecto demuestra la aplicación de técnicas de automatización industrial en un entorno agrícola controlado, integrando sensores, actuadores y herramientas IoT.
La simulación en Wokwi y la visualización en Node-RED permiten validar el funcionamiento antes de implementar un sistema físico real.
Además, la solución es escalable y reutilizable en sistemas de riego más grandes, invernaderos o cultivos automatizados.
