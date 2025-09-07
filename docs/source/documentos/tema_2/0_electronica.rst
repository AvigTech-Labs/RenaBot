Instalacion Arduino ID 
======================

Arduino IDE 2.x
---------------

Una vez configurado mosquitto, es necesario descargar arduino IDE, el
programa que se utilazará para programar la ESP32.

.. _instalación-1:

Instalación
~~~~~~~~~~~

1. **Descargar Arduino IDE**

Enlace de descargar, AppImage
`arduino <https://www.arduino.cc/en/software/>`__

En la carpeta donde se descargó el AppImage, cambiar el nombre al
archivo descargado por ``arduino-ide``

.. code:: bash

   sudo apt update
   sudo apt upgrade
   sudo install libfuse2
   sudo chmod +x arduino-ide.AppImage
   ./arduino-ide.AppImage

2. **Instalar soporte para ESP32**

   1. Ir a ``Archivo → Preferencias``
   2. En el campo **URLs adicionales para tarjetas**, agregar:

   ::

      https://espressif.github.io/arduino-esp32/package_esp32_index.json

   3. Ir a ``Herramientas → Placa → Gestor de tarjetas``
   4. Buscar ``esp32`` e instalar **versión 3.x**

Esta versión es compatible con las últimas bibliotecas MQTT y WiFi.

--------------

3. Acceso a puertos USB**\*

El ESP32 se conecta como un dispositivo serie (``/dev/ttyUSB*`` o
``/dev/ttyACM*``) generalmente la conexión se realiza en el puerto
ttyUSB. Para acceder sin sudo:

Añadir usuario a grupo dialout*\*

.. code:: bash

   sudo usermod -aG dialout $USER

Reiniciar sesión para aplicar cambios.

Ver dispositivos conectados

.. code:: bash

   ls /dev/ttyUSB*

Ver cambios al conectar o desconectar

.. code:: bash

   watch -n 1 ls /dev/ttyUSB*

--------------

4. Instalación de bibliotecas recomendadas en Arduino IDE

Tabla de librerías recomendadas para tu proyecto ESP32 con WiFiManager y MQTT

.. list-table::
   :header-rows: 1
   :widths: 15 20 20 60

   * - Librería
     - Autor
     - Versión recomendada
     - Notas importantes
   * - WiFiManager
     - tzapu
     - 2.0.17
     - Compatible con ESP32 core 3.x. Asegura el uso del namespace correcto para ESP32. Soporta parámetros personalizados.
   * - WiFi
     - Espressif
     - Incluida en el core
     - No requiere instalación manual. Ya viene con el core de ESP32.
   * - EEPROM
     - Espressif
     - Incluida en el core
     - Opcional migrar a Preferences si deseas mayor robustez.
   * - Websockets
     - Markus Sattler
     - 2.3.6
     - Conexion Websockets y Server
   * - Websockets_Generic
     - Markus Sattler
     - 2.15.0
     - Conexion Websockets y Server
   * - ArduinoJson
     - Benoit Blanchon
     - 7.x.x
     - Usa DynamicJsonDocument y StaticJsonDocument.

