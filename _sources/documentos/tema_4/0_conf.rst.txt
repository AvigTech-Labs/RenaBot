PySide6
=======

Estructura de la carpeta
------------------------

Los archivos originales de ``ui_main.py`` y ``resources_rc.py``, se
encuentran etiquetados como:

- ``0_ui_main.py``
- ``0_resources_rc.py``

Comandos para actualizar HMI
----------------------------

.. code:: python

   pyside6-uic main.ui > modules/ui_main.py
   pyside6-rcc resources.qrc -o modules/resources_rc.py
