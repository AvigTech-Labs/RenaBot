Componentes Electrónicos
========================

Placa de Control
----------------

La **placa de control** del RENA-BOT contiene un diseño **PCB/SMD** optimizado para educación.  
Permite la integración de motores, sensores y actuadores de manera ordenada, con conectores accesibles y pines de expansión para futuros proyectos.  
Está basada en un microcontrolador **ESP32 WROOM 1**, lo que brinda flexibilidad para programar el robot con distintos entornos como **Arduino IDE, Python o fREERTOS**.  


.. figure:: ./img/placa_control.jpg
   :alt: modelorobot
   :align: center
   :width: 400px

   Placa de Control v1

Sensores
--------

El RENA-BOT incluye la siguiente lista de sensores, los cuales permiten que el robot interactúe con su entorno y ejecute diferentes actividades educativas:  

Seguidor de línea
~~~~~~~~~~~~~~~~~

El sensor **QTR8** está compuesto por un arreglo de sensores infrarrojos que permiten detectar el contraste entre superficies claras y oscuras.  
Su uso principal es el seguimiento de trayectorias o líneas en el suelo, lo que facilita actividades como **robots seguidores de línea** o **laberintos**.  

Sensor de distancia
~~~~~~~~~~~~~~~~~~~

El sensor **ultrasónico HC-SR04** mide la distancia hasta un objeto enviando y recibiendo ondas ultrasónicas.  
Se utiliza para evitar obstáculos, medir proximidad o ejecutar maniobras de navegación segura en entornos controlados.  

Sensor de intensidad lumínica
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

El sensor **LDR (Light Dependent Resistor)** cambia su resistencia según la cantidad de luz recibida.  
Se usa para medir niveles de iluminación en el ambiente, permitiendo actividades como:  
- Detección de luz y oscuridad.  
- Encendido automático de luces.  
- Reacciones del robot a diferentes condiciones de iluminación.  

Sensor de temperatura
~~~~~~~~~~~~~~~~~~~~~

El **LM35** es un sensor analógico de temperatura que entrega una salida proporcional a los grados Celsius.  
Permite medir la temperatura del ambiente de manera sencilla y precisa, con aplicaciones educativas en:  
- Experimentos de ciencia y medio ambiente.  
- Registro de condiciones térmicas.  
- Automatización básica de sistemas sensibles a la temperatura.  

Actuadores
----------

El RENA-BOT incluye la siguiente lista de actuadores, los cuales permiten que el robot realice acciones físicas: 

Motores DC
~~~~~~~~~~

Los **motores de corriente directa (DC)** son los responsables de mover el robot.  
Se encuentran acoplados a reductores que aumentan el torque y permiten un movimiento controlado de las ruedas.  
Su uso principal es el desplazamiento del robot, aplicando control diferencial (mover ambos motores a distinta velocidad/dirección para girar, avanzar o retroceder).  

Servomotor
~~~~~~~~~~

El **servomotor SG90** es un actuador de pequeño tamaño que permite un movimiento angular limitado (0° a 180°).  
Se utiliza en el RENA-BOT para controlar eL manipulador de objetos. 


Fuente de energía
-----------------

El RENA-BOT utiliza una batería recargable **LiPo (Polímero de Litio) de 2 celdas (2S), 7.4 V, 1500 mAh y 35C**.  

Características principales:  
- **Voltaje nominal:** 7.4 V (3.7 V por celda).  
- **Capacidad:** 1500 mAh, lo que ofrece un tiempo de operación adecuado para actividades educativas de corta y mediana duración.  
- **Tasa de descarga:** 35C, permitiendo suministrar la corriente suficiente para los motores y actuadores del robot sin caídas de voltaje.  

Recomendaciones de seguridad:  
- Nunca descargar la batería por debajo de **6.0 V (3.0 V por celda)**, ya que puede dañarse permanentemente.  
- Utilizar siempre un cargador balanceado para LiPo, que garantice la seguridad y prolongue la vida útil de la batería.  
- Evitar golpes, perforaciones o exposición a altas temperaturas.  
- Durante el almacenamiento prolongado, mantener la batería en un nivel de **carga de almacenamiento (~3.8 V por celda)**.  

.. tip::
   Con un uso responsable, esta batería puede tener una larga vida útil y es suficiente para múltiples sesiones educativas antes de requerir recarga.

	