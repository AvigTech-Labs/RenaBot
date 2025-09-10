Componentes Electrónicos
========================

Placa de Control
----------------

La **placa de control** del RENA-BOT contiene un diseño **PCB/SMD** optimizado para educación.  
Permite la integración de motores, sensores y actuadores de manera ordenada, con conectores accesibles y pines de expansión para futuros proyectos.  
Está basada en un microcontrolador **ESP32 WROOM 1**, lo que brinda flexibilidad para programar el robot con distintos entornos como **Arduino IDE, Micro-Python o FreeRTOS**.  


.. figure:: ./img/placa_control.jpg
   :alt: modelorobot
   :align: center
   :width: 400px

   Placa de Control v1


Características
~~~~~~~~~~~~~~~

La placa de control del **RENA-BOT** ha sido diseñada con un **ensamblaje de montaje superficial (SMD)**, lo que garantiza un acabado compacto, ligero y confiable.  
Sus dimensiones son **100 x 150 x 2 mm**, cuenta con **recubrimiento protector** para mayor durabilidad y resistencia, y está optimizada para integrar de forma ordenada los diferentes módulos del robot en formato
plug and play.  

Entre los principales elementos que incorpora se encuentran: 

.. list-table::
   :header-rows: 1
   :widths: 8 28 66
   :class: fit-table

   * - N°
     - Elemento
     - Descripción
   * - 1
     - Microcontrolador ESP32-WROOM-32 (SMD)
     - Módulo WiFi + Bluetooth con doble núcleo Xtensa LX6 a 240 MHz, 520 KB de SRAM y hasta 16 MB de flash externa. Control principal del RENA-BOT.
   * - 2
     - Regulador de voltaje MP1584EN (step-down, 3A)
     - Conversor DC-DC buck que convierte 7.4 V de la batería LiPo a 5 V estables para lógica y periféricos. Alta eficiencia y hasta 3 A.
   * - 3
     - Diodo Schottky
     - Protección por polaridad inversa y mejora de eficiencia gracias a su baja caída de tensión.
   * - 4
     - Conector USB tipo C
     - Programación del ESP32 y alimentación auxiliar durante pruebas.
   * - 5
     - Conectores Molex JST XH2.54 (2, 3 o 4 pines)
     - Conexión rápida y segura para sensores, actuadores y expansiones.
   * - 6
     - Multiplexor analógico 74HC4067 (16 canales)
     - Expansión de entradas analógicas/digitales usando 4 pines de control.
   * - 7
     - Driver de motores L293D
     - Puente H dual para dos motores DC con control de dirección y PWM.
   * - 8
     - Pulsadores SMD
     - Entradas para la selección manual de movimiento del RENA-BOT en el modo seguidor de línea.
   * - 9
     - Sensores LDR 
     - El sensor **LDR (Light Dependent Resistor)** integrado en la placa posee unas dimensiones de 5 mm de diámetro.
   * - 10
     - Switch SMD 
     - Entrada para la selección de conexión con el RENA-BOT, como cliente WiFi o punto de acceso WiFi.

Sensores
--------

El RENA-BOT incluye la siguiente lista de sensores, los cuales permiten que el robot interactúe con su entorno y ejecute diferentes actividades educativas:  

Seguidor de línea
~~~~~~~~~~~~~~~~~

El sensor **QTR8** está compuesto por un arreglo de 8 sensores infrarrojos (IR) que permiten detectar el contraste entre superficies claras y oscuras.  
Funciona emitiendo luz infrarroja y midiendo la cantidad de reflexión en el suelo: superficies claras reflejan más y las oscuras menos.  

Características técnicas:  
- Tipo: arreglo de sensores IR reflectivos.  
- Canales: 8 independientes.  
- Salida: analógica. 
- Voltaje de operación: 3.3 V – 5 V.  

Uso en el RENA-BOT:  
- Seguimiento de trayectorias y circuitos impresos en el suelo.  
- Implementación de robots **seguidores de línea** en competiciones educativas.  
- Desarrollo de proyectos como **laberintos y rutinas de navegación autónoma**.  

.. figure:: ./img/sensor_qtr8.jpg
   :alt: sensor_qtr8
   :align: center
   :width: 400px

   Sensor QTR8 en el RENA-BOT

Sensor de distancia
~~~~~~~~~~~~~~~~~~~

El sensor **ultrasónico HC-SR04** mide la distancia hasta un objeto enviando un pulso ultrasónico y calculando el tiempo que tarda en reflejarse.  

Características técnicas:  
- Rango de medición: 2 cm a 400 cm.  
- Precisión: ±3 mm.  
- Ángulo de detección: ~15°.  
- Voltaje de operación: 5 V.  

Uso en el RENA-BOT:  
- **Evitación de obstáculos** durante el recorrido.  
- Implementación de sistemas de **detención automática** cuando un objeto se acerca.  
- Actividades educativas sobre el **principio del sonar** y la propagación del sonido.  

Sensor de intensidad lumínica
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

El sensor **LDR (Light Dependent Resistor)** varía su resistencia eléctrica según la cantidad de luz que incide sobre él.  
Se utiliza como un divisor de tensión, conectado a una entrada analógica del microcontrolador.  

Características técnicas:  
- Rango espectral: 400 – 700 nm (luz visible).  
- Tiempo de respuesta: 20 – 30 ms.  
- Voltaje de operación: 3.3 V – 5 V.  

Uso en el RENA-BOT:  
- Detectar niveles de **luz y oscuridad**.  
- Encender automáticamente LEDs cuando baja la iluminación.  
- Ejercicios de programación donde el robot reaccione a condiciones ambientales.  

Sensor de temperatura
~~~~~~~~~~~~~~~~~~~~~

El **LM35** es un sensor analógico de temperatura con salida lineal en milivoltios proporcional a la temperatura en grados Celsius.  

Características técnicas:  
- Rango de medición: -55 °C a +150 °C.  
- Precisión: ±0.5 °C (a 25 °C).  
- Salida: 10 mV/°C.  
- Voltaje de operación: 4 V – 30 V.  

Uso en el RENA-BOT:  
- Experimentos de **ciencia ambiental** (medición de temperatura del entorno).  
- Registro de condiciones térmicas en proyectos escolares.   

Actuadores
----------

El RENA-BOT incluye la siguiente lista de actuadores, los cuales permiten que el robot realice acciones físicas:  

Motores DC
~~~~~~~~~~

Los **motores de corriente directa (DC)** son los responsables del movimiento del robot.  
Están acoplados a reductores que aumentan el torque y permiten controlar con precisión la velocidad y dirección de las ruedas.  

Características técnicas:  
- Voltaje de operación: 6 – 9 V.  
- Velocidad nominal: 150 RPM (sin carga).  

Uso en el RENA-BOT:  
- Desplazamiento mediante **control diferencial** (variando la velocidad de cada rueda).  
- Implementación de rutinas de navegación en trayectorias rectas, curvas o giros.  
- Actividades prácticas para enseñar conceptos de **cinemática de robots móviles**.- Resolución de encoder: 300 pulsos por revolución.    

Servomotor
~~~~~~~~~~

El **servomotor SG90** es un actuador de pequeño tamaño que permite un movimiento angular limitado en un rango de **0° a 180°**.  
Se controla enviando pulsos PWM desde el microcontrolador.  

Características técnicas:  
- Voltaje de operación: 4.8 V – 6 V.  
- Ángulo de rotación: 0° a 180°.  
- Torque: ~1.8 kg·cm.  
- Peso: 9 g.  

Uso en el RENA-BOT:  
- Control de un **manipulador de objetos** o gripper.  
- Movimiento de sensores (por ejemplo, rotación de un sensor ultrasónico).  
- Actividades para enseñar el control de **PWM y actuadores de precisión**.  


Módulo de Buzzer Pasivo 
~~~~~~~~~~~~~~~~~~~~~~~~

El **buzzer pasivo** es un actuador electrónico capaz de generar sonidos o tonos al recibir una señal de frecuencia desde el microcontrolador.  
A diferencia del **buzzer activo**, que emite un tono fijo con solo aplicarle voltaje, el buzzer pasivo requiere que se le envíen **señales PWM** para producir distintos sonidos y melodías.

Características técnicas:  
- Tipo: buzzer pasivo.  
- Voltaje de operación: 3.3 V – 5 V.  
- Control mediante señal PWM.  
- Tamaño compacto, fácil de integrar en la placa o en módulos externos.  



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

	