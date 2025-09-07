API Blockly + MQTT
===================

Esta documentación describe el funcionamiento actual de la API que conecta la interfaz visual Blockly con el backend basado en FastAPI y comunicación MQTT. El sistema permite construir programas visuales usando bloques, que luego se traducen en comandos JSON y se ejecutan en un robot o sistema IoT.

Arquitectura del sistema
------------------------

El sistema está dividido en tres componentes principales:

1. **Frontend (Blockly)**:
   - Interfaz visual que permite construir lógica con bloques.
   - Convierte los bloques en un JSON estructurado mediante un generador personalizado.
   - Envía el JSON al backend usando un `fetch()` al endpoint `/api/comando`.

2. **Backend (FastAPI)**:
   - Recibe comandos en formato JSON.
   - Interpreta secuencialmente los comandos: `publicar`, `esperar`, `for`, `if`.
   - Publica mensajes al tópico MQTT `/ra/motores`.
   - Escucha respuestas en el tópico `/estado`.

3. **Broker MQTT**:
   - Canal intermedio para la comunicación entre backend y dispositivos.
   - El backend publica en `/ra/motores`.
   - Se espera confirmación de dispositivos en `/estado`.

Endpoint disponible
-------------------

.. tabs:: 
   
   .. group-tab:: /api/comando

      Ejecuta una secuencia de comandos generada desde Blockly.

      **Entrada JSON**:

      .. code-block:: json

         {
         "comandos": [
            {
               "accion": "publicar",
               "mensaje": { "accion": "mover", "valor": "adelante" }
            },
            {
               "accion": "esperar"
            },
            {
               "accion": "for",
               "repeticiones": 3,
               "hacer": [
               {
                  "accion": "publicar",
                  "mensaje": { "accion": "mover", "valor": "izquierda" }
               }
               ]
            }
         ]
         }
   
   .. group-tab:: **Respuesta**:

      .. code-block:: json

         {
         "status": "ok"
         }  

Comandos disponibles
--------------------

Cada comando corresponde a un bloque de Blockly. El backend interpreta los comandos en orden.

**1. publicar**

Publica un mensaje MQTT al tópico ``/ra/motores``.

.. code-block:: json

   {
     "accion": "publicar",
     "mensaje": {
       "accion": "mover",
       "valor": "adelante"
     }
   }

**2. esperar**

Espera un mensaje de confirmación MQTT desde ``/estado``.

.. code-block:: json

   {
     "accion": "esperar"
   }

**3. for**

Repite una serie de instrucciones un número determinado de veces.

.. code-block:: json

   {
     "accion": "for",
     "repeticiones": 5,
     "hacer": [
       {
         "accion": "publicar",
         "mensaje": { "accion": "mover", "valor": "izquierda" }
       }
     ]
   }

**4. if**

Evalúa una condición y ejecuta instrucciones si se cumple.

.. code-block:: json

   {
     "accion": "if",
     "condicion": {
       "variable": "x",
       "operador": ">",
       "valor": 10
     },
     "entonces": [
       {
         "accion": "publicar",
         "mensaje": { "accion": "mover", "valor": "atras" }
       }
     ]
   }

Estado actual del sistema
-------------------------

- ✅ Soporte completo para estructuras anidadas (`for` con `if`, etc.).
- ✅ Generación automática de JSON desde bloques Blockly.
- ✅ Comunicación MQTT funcional (`/ra/motores`, `/estado`).
- ✅ Backend basado en FastAPI, extensible.
- ✅ Integración educativa y visual para control de robots o sistemas IoT.

**Código** ``41_bloques``: Generación de bloques personalizados Blockly

**Código** ``41_index``: Frontend

**Código** ``41_main``: Backend

**Código** ``41_equipo1``: Equipo prueba de MQTT

Codigo:


.. tabs:: 
   
   .. group-tab:: 41_Bloques

      **Entrada JSON**:

      .. [START codigo-41-1]

      .. code-block:: js

         // bloque movimientos

         Blockly.defineBlocksWithJsonArray([

         {
         "type": "enviar_comando_api",
         "message0": "Mover Robot Movil %1",
         "args0": [
                  {
                  "type": "field_dropdown",
                  "name": "VALOR",
                  "options": [
                        ["adelante", "adelante"],
                        ["izquierda", "giro_izquierda"],
                        ["derecha", "giro_derecha"],
                        ["atras", "giro_180"]
                  ]
                  },
                  ],
         "previousStatement": null,
         "nextStatement": null,
         "colour": 230,
         "tooltip": "Activa los movimientos del RENA Bot"
         },

         // Bloque Esperar 
         {
         "type": "bloque_esperar",
         "message0": "Esperar  %1 segundos",
            "args0": [
               { "type": "input_value", "name": "TIME", "check": "Number" },
                     ],
         "previousStatement": null,
         "nextStatement": null,
         "colour": 230,
         "tooltip": "Espera una cantidad de tiempo"
         },

         // Bloque condicional IF
         {
         "type": "bloque_if",
         "message0": "si %1 entonces %2",
         "args0": [
               {
               "type": "input_value",
               "name": "VAR",  // recibe expresiones booleanas como output: "Boolean"
               "check": "Boolean"
               },
               {
               "type": "input_statement",
               "name": "ENTONCES"
               }
         ],
         "colour": 120,
         "tooltip": "Evalúa una condición lógica y ejecuta si se cumple",
         "previousStatement": null,
         "nextStatement": null
         },

         // Bloque For
         {
         "type": "bloque_for",
         "message0": "repetir %1 veces %2 hacer %3",
         "args0": [
               { "type": "input_value", "name": "VECES", "check": "Number" },
               { "type": "input_dummy" },
               { "type": "input_statement", "name": "HACER" }
         ],
         "colour":120,
         "tooltip": "Repite instrucciones varias veces",
         "previousStatement": null,
         "nextStatement": null
         },

         // BLoque Comparación entre variables 
         {
         "type": "logic_compare",
         "message0": "%1 %2 %3",
         "args0": [
         { "type": "input_value", "name": "A", "check": "Number" },
         {
               "type": "field_dropdown",
               "name": "OP",
               "options": [
               ["=", "=="],
               ["≠", "!="],
               ["<", "<"],
               ["≤", "<="],
               [">", ">"],
               ["≥", ">="]
               ]
         },
         { "type": "input_value", "name": "B", "check": "Number" }
         ],
         "inputsInline": true,
         "output": "Boolean",
         "colour": 210,
         "tooltip": "Compara dos valores numéricos"
         },

         // Bloque condicional entre IFs
         {
         "type": "logic_operation",
         "message0": "%1 %2 %3",
         "args0": [
         {
               "type": "input_value",
               "name": "A",
               "check": "Boolean"
         },
         {
               "type": "field_dropdown",
               "name": "OP",
               "options": [["y", "AND"], ["o", "OR"]]
         },
         {
               "type": "input_value",
               "name": "B",
               "check": "Boolean"
         }
         ],
         "inputsInline": true,
         "output": "Boolean",
         "colour": 210,
         "tooltip": "Operador lógico AND/OR"
         },

         // Bloque If/Else
         {
         "type": "bloque_if_else",
         "message0": "si %1 entonces %2 si no %3",
         "args0": [
         { "type": "input_value", "name": "COND", "check": "Boolean" },
         { "type": "input_statement", "name": "ENTONCES" },
         { "type": "input_statement", "name": "SINO" }
         ],
         "previousStatement": null,
         "nextStatement": null,
         "colour": 120,
         "tooltip": "Si la condición se cumple, hace algo; si no, otra cosa."
         },

         // Bloque Booleano
         {
         "type": "logic_boolean",
         "message0": "%1",
         "args0": [
         {
               "type": "field_dropdown",
               "name": "BOOL",
               "options": [["verdadero", "True"], ["falso", "False"]]
         }
         ],
         "output": "Boolean",
         "colour": 210,
         "tooltip": "Devuelve verdadero o falso"
         },

         // Bloque Iniciar el Robot
         {
         "type": "bloque_inicar",
         "message0": "Iniciar kit de robótica %1",
         "args0": [
                  { "type": "field_input", "name": "NOMBRE", "text": "Rena1" },
                  ],
         "hat": "cap",
         "nextStatement": null,
         "colour": "#f19f10",
         "tooltip": "Iniciar Rena Robot"
         },

         // Bloque modificar velocidad 
         {
         "type": "bloque_velocidad",
         "message0": "Velocidad %1 porcentaje",
         "args0": [
                  { "type": "input_value", "name": "VELOCIDAD", "check": "Number" }
                  ],
         "previousStatement": null,
         "nextStatement": null,
         "colour": 230,
         "tooltip": "Define la velocidad a la que se mueve el robot"
         },

         // Bloque activar el gripper
         {
         "type": "bloque_gripper",
         "message0": "Gripper %1",
         "args0": [
               {
               "type": "field_dropdown",
               "name": "GRIPPER",
               "options": [
                  ["Abrir", "Abrir"],
                  ["Cerrar", "Cerrar"],
               ]
               },
                  ],
         "previousStatement": null,
         "nextStatement": null,
         "colour": 230,
         "tooltip": "Cambiar el estado del gripper"
         },

         // Bloque Encender LEDs
         {
         "type": "bloque_led",
         "message0": "LED %1",
         "args0": [
               {
               "type": "field_dropdown",
               "name": "LED",
               "options": [
                  ["ON", "ON"],
                  ["OFF", "OFF"],
               ]
               },
                  ],
         "previousStatement": null,
         "nextStatement": null,
         "colour": 230,
         "tooltip": "Encender LEDS"
         },

         // Bloque crear variable
         {
         "type": "crear_variable",
         "message0": "crear variable %1 con valor %2",
         "args0": [
         { "type": "field_input", "name": "NOMBRE", "text": "mi_variable" },
         { "type": "input_value", "name": "VALOR" }
         ],
         "previousStatement": null,
         "nextStatement": null,
         "colour": 290,
         "tooltip": "Crea una nueva variable con valor inicial"
         },

         //  BLoque modificar variables
         {
         "type": "set_variable",
         "message0": "establecer %1 a %2",
         "args0": [
         { "type": "field_input", "name": "NOMBRE", "text": "mi_variable" },
         { "type": "input_value", "name": "VALOR" }
         ],
         "previousStatement": null,
         "nextStatement": null,
         "colour": 290,
         "tooltip": "Asigna un nuevo valor a una variable"
         },

         // Bloque Obtener variable
         {
         "type": "get_variable",
         "message0": "valor de %1",
         "args0": [
         { "type": "field_input", "name": "NOMBRE", "text": "mi_variable" }
         ],
         "output": null,
         "colour": 290,
         "tooltip": "Obtiene el valor actual de la variable"
         },

         // Bloque leer dato del sensor
         {
         "type": "bloque_leer_sensor",
         "message0": "Leer el sensor %1",
         "args0": [
               {
               "type": "field_dropdown",
               "name": "SENSOR",
               "options": [
                  ["ultrasonico", "ultrasonico"],
                  ["LDR", "LDR"],
               ]
               },
         ],
         "previousStatement": null,
         "nextStatement": null,
         "colour": 290,
         "tooltip": "Actualiza el valor de un sensor"
         },

         // Variable de sensor
         {
         "type": "get_sensor",
         "message0": "nombre de variable %1",
         "args0": [
         {
         "type": "field_input",
         "name": "NUM",
         "text": "ultrasonico"
         }
         ],
         "output": "Number",  // o "String" si es texto, o null si es genérico
         "colour": 290,
         "tooltip": "Devuelve el nombre de la variable para usar en comparaciones"
         }

         ]);

         // Generadores de bloques 

         const jsonGen = new Blockly.Generator("JSON");
         jsonGen.ORDER_ATOMIC = 0;

         jsonGen.init = function(workspace) {
            jsonGen.commands_ = [];
         };

         jsonGen.scrub_ = function(block, code) {
            const next = block.getNextBlock();
            if (next) jsonGen.blockToCode(next);
            return block.outputConnection ? code : "";
         };

         // Generador bloque enviar movimiento 
         jsonGen.forBlock['enviar_comando_api'] = function(block) {
            const valor = block.getFieldValue("VALOR");
            jsonGen.commands_.push({
            accion: "publicar",
            mensaje: { accion: "publicar", valor }
            });
            return '';
         };

         //  Generador bloque Esperar
         jsonGen.forBlock['bloque_esperar'] = function(block) {
            let timer = 0;
            const b = block.getInputTargetBlock("TIME");
            if (b && b.type === 'math_number') timer = parseInt(b.getFieldValue('NUM')) || 0;

            jsonGen.commands_.push({ 
            accion: "esperar",
            mensaje: {timer}
            });
            return '';
         };

         // Generadores de variables NUM, TEXT y BOOL
         jsonGen.forBlock['math_number'] = block => [block.getFieldValue('NUM'), jsonGen.ORDER_ATOMIC];
         jsonGen.forBlock['text'] = block => [JSON.stringify(block.getFieldValue("TEXT")), jsonGen.ORDER_ATOMIC];
         jsonGen.forBlock['logic_boolean'] = block => [block.getFieldValue('BOOL'), jsonGen.ORDER_ATOMIC];

         // Generadores codigo logica comparar
         jsonGen.forBlock['logic_compare'] = function(block) {
            const op = block.getFieldValue('OP') || '==';
            let izquierda = '0';
            let derecha = '0';

            const leftBlock = block.getInputTargetBlock("A");
            if (leftBlock) {
            const leftCode = jsonGen.blockToCode(leftBlock);
            izquierda = Array.isArray(leftCode) ? leftCode[0] : leftCode;
            }

            const rightBlock = block.getInputTargetBlock("B");
            if (rightBlock) {
            const rightCode = jsonGen.blockToCode(rightBlock);
            derecha = Array.isArray(rightCode) ? rightCode[0] : rightCode;
            }

            const resultado = `(${izquierda} ${op} ${derecha})`;
            console.log("✅ logic_compare:", resultado);
            return [resultado, jsonGen.ORDER_ATOMIC];
         };

         // Generador comparador de condicionales
         jsonGen.forBlock['logic_operation'] = function(block) {
            const a = jsonGen.blockToCode(block.getInputTargetBlock("A"))[0] || 'False';
            const b = jsonGen.blockToCode(block.getInputTargetBlock("B"))[0] || 'False';
            const op = block.getFieldValue("OP") === "AND" ? "and" : "or";
            return [`(${a} ${op} ${b})`, jsonGen.ORDER_ATOMIC];
         };

         // Generador bloque if 
         jsonGen.forBlock['bloque_if'] = function(block) {
            const exprBlock = block.getInputTargetBlock("VAR");
            let exprCode = exprBlock ? (Array.isArray(jsonGen.blockToCode(exprBlock)) ? jsonGen.blockToCode(exprBlock)[0] : "False") : "False";
            const outer = jsonGen.commands_;
            jsonGen.commands_ = [];
            const inner = block.getInputTargetBlock("ENTONCES");
            if (inner) jsonGen.blockToCode(inner);
            const acciones = jsonGen.commands_;
            jsonGen.commands_ = outer;

            jsonGen.commands_.push({ accion: "if", expresion: exprCode, entonces: acciones });
            return '';
         };

         // Generador bloque for
         jsonGen.forBlock['bloque_for'] = function(block) {
            let repeticiones = 1;
            const b = block.getInputTargetBlock("VECES");
            if (b && b.type === 'math_number') repeticiones = parseInt(b.getFieldValue('NUM')) || 1;

            const outer = jsonGen.commands_;
            jsonGen.commands_ = [];
            const inner = block.getInputTargetBlock("HACER");
            if (inner) jsonGen.blockToCode(inner);
            const acciones = jsonGen.commands_;
            jsonGen.commands_ = outer;

            jsonGen.commands_.push({ accion: "for", repeticiones, hacer: acciones });
            return '';
         };

         // Generador bloque If/ Else
         jsonGen.forBlock['bloque_if_else'] = function(block) {
            const exprBlock = block.getInputTargetBlock("COND");
            const exprCode = exprBlock ? jsonGen.blockToCode(exprBlock)[0] : "False";

            const outer = jsonGen.commands_;
            jsonGen.commands_ = [];
            const si = block.getInputTargetBlock("ENTONCES");
            if (si) jsonGen.blockToCode(si);
            const entonces = jsonGen.commands_;

            jsonGen.commands_ = [];
            const no = block.getInputTargetBlock("SINO");
            if (no) jsonGen.blockToCode(no);
            const sino = jsonGen.commands_;

            jsonGen.commands_ = outer;
            jsonGen.commands_.push({ accion: "if_else", expresion: exprCode, entonces, sino });
            return '';
         };

         // Generador bloque velocidad 
         jsonGen.forBlock['bloque_velocidad'] = function(block) {
            let porcentaje = 60;
            const b = block.getInputTargetBlock("VELOCIDAD");
            if (b && b.type === 'math_number') porcentaje = parseInt(b.getFieldValue('NUM')) || 60;

            jsonGen.commands_.push({
            accion: "set_velocidad",
            mensaje: { accion: "publicar", porcentaje }
            });
            return '';
         };

         // Generador bloque iniciar
         jsonGen.forBlock['bloque_inicar'] = function(block) {
            const nombre = block.getFieldValue("NOMBRE");
            jsonGen.commands_.push({
            accion: "iniciar",
            mensaje: {nombre}
            });
            return '';
         };

         // Generador gripper
         jsonGen.forBlock['bloque_gripper'] = function(block) {
            const valor = block.getFieldValue("GRIPPER");
            jsonGen.commands_.push({
            accion: "set_gripper",
            mensaje: {valor}
            });
            return '';
         };

         //  Generador Crear variable
         jsonGen.forBlock['crear_variable'] = function(block) {
            const nombre = block.getFieldValue("NOMBRE");
            const valorBlock = block.getInputTargetBlock("VALOR");
            const valor = valorBlock ? jsonGen.blockToCode(valorBlock, jsonGen.ORDER_ATOMIC)[0] : "0";

            jsonGen.commands_.push({
            accion: "definir_variable",
            nombre,
            valor
            });

            return '';
         };

         // Generador set veriuable 
         jsonGen.forBlock['set_variable'] = function(block) {
            const nombre = block.getFieldValue("NOMBRE");
            const valorBlock = block.getInputTargetBlock("VALOR");
            const valor = valorBlock ? jsonGen.blockToCode(valorBlock, jsonGen.ORDER_ATOMIC)[0] : "0";

            jsonGen.commands_.push({
            accion: "asignar_variable",
            nombre,
            valor
            });

            return '';
         };

         // Generador get variable
         jsonGen.forBlock['get_variable'] = function(block) {
            const nombre = block.getFieldValue("NOMBRE");
            return [nombre, jsonGen.ORDER_ATOMIC];
         };

         // Generador leer sensor
         jsonGen.forBlock['bloque_leer_sensor'] = function(block) {
            const nombre = block.getFieldValue("SENSOR");
            jsonGen.commands_.push({
            accion: "leer_sensor",
            mensaje: {sensor: nombre}
            });
            return '';
         };

         // Generador variable de sensor
         jsonGen.forBlock['get_sensor'] = block => {
            const nombre = block.getFieldValue('NUM');
            return [nombre, jsonGen.ORDER_ATOMIC];
         };

      .. [END codigo-41-1]

   .. group-tab:: 41_index

      .. [START codigo-41-2]

      .. code-block:: html

         <!DOCTYPE html>
         <html>
         <head>
         <meta charset="utf-8">
         <title>Blockly JSON API MQTT</title>
         <script src="https://unpkg.com/blockly/blockly.min.js"></script>
         <script src="bloques.js"></script>

         <style>
            html, body {
               margin: 0; padding: 0;
               height: 100vh;
               font-family: sans-serif;
            }

            #mainContainer {
               position: relative;
               width: 100%;
               height: 100vh;
            }

            #blocklyDiv {
               width: 100%;
               height: 100%;
            }

            /* Botón flotante derecho */
            #toggleCodeBtn,
            #runBtn {
               position: absolute;
               right: 0;
               background-color: orange;
               border: none;
               border-radius: 20px 0 0 20px;
               padding: 18px;
               font-size: 12px;
               cursor: pointer;
               z-index: 10;
               box-shadow: 0 0 5px rgba(0,0,0,0.3);
            }

            #toggleCodeBtn {
               top: 300px;
            }

            #runBtn {
               top: 0px;
            }

            /* Panel lateral derecho para JSON */
            #codePanel {
               position: absolute;
               top: 0;
               right: -50%;
               width: 50%;
               height: 100%;
               background: #181824;
               color: white;
               padding: 20px;
               box-sizing: border-box;
               transition: right 0.3s ease;
               z-index: 9;
               display: flex;
               flex-direction: column;
            }

            #codePanel.open {
               right: 0;
            }
            #codeArea {
               flex: 1;
               width: 100%;
               font-family: monospace;
               font-size: 14px;
               background-color: #181824;
               color: #eee;
               border: none;
               resize: none;
               padding: 10px;
               overflow: auto;
            }
         </style>
         </head>

         <body>
         <div id="mainContainer">
            <div id="blocklyDiv"></div>

            <!-- Botón flotante derecho -->
            <button id="toggleCodeBtn">&lt;/&gt;</button>
            <button id="runBtn">Ejecutar</button>

            <!-- Panel deslizante con código JSON -->
            <div id="codePanel">
               <textarea id="codeArea" readonly></textarea>
            </div>
         </div>

         <!-- TOOLBOX DEFINIDO -->
         <xml id="toolbox" style="display: none">
            <category name="Rena 1" colour="#f19f10">
               <block type="bloque_inicar"></block>
               <block type="enviar_comando_api"></block>
               <block type="bloque_esperar"></block>
               <block type="bloque_velocidad"></block>
               <block type="bloque_gripper"></block>
            </category>

            <category name="Sensores" colour="#f19f10">
               <block type="bloque_leer_sensor"></block>
               <block type="get_sensor"></block>
            </category>


            <category name="Valores" colour= "#2777c3">
               <block type="math_number"></block>
               <block type="text"></block>
               <block type="logic_boolean"></block>
            </category>
            <category name="Control" colour="#2fa92f">
               <block type="bloque_if"></block>
               <block type="bloque_for"></block>
               <block type="bloque_if_else"></block>
            </category>
            <category name="Lógica" colour="#2196f3">
               <block type="logic_compare"></block>
               <block type="logic_operation"></block>
               <block type="logic_boolean"></block>
            </category>
            <category name="Variables" colour="#ba68c8">
               <block type="crear_variable"></block>
               <block type="set_variable"></block>
               <block type="get_variable"></block>
            </category>

         </xml>

            <script>
               const workspace = Blockly.inject('blocklyDiv', {
                  toolbox: document.getElementById('toolbox'),
                  theme: Blockly.Theme.defineTheme('miTemaClaro', {
                     base: Blockly.Themes.Classic,
                     fontStyle: { size: 14 }
                  }),
                  scrollbars: true,
                  trashcan: true,
                  zoom: { controls: true },
                  renderer: "zelos",
                  sounds: false
               });

               async function ejecutarComandos() {
                  jsonGen.init(workspace);
                  jsonGen.commands_ = [];
                  jsonGen.workspaceToCode(workspace);
                  const comandos = jsonGen.commands_;

                  document.getElementById("codeArea").value = JSON.stringify(comandos, null, 2);
                  try {
                     const res = await fetch("http://localhost:8000/api/comando", {
                     method: "POST",
                     headers: { "Content-Type": "application/json" },
                     body: JSON.stringify({ comandos })
                     });
                     const data = await res.json();
                     document.getElementById("codeArea").value += "\n\n✅ Respuesta: " + JSON.stringify(data);
                  } catch (err) {
                     document.getElementById("codeArea").value += "\n\n❌ Error: " + err;
                  }
               }

               // Mostrar/ocultar panel
               document.getElementById("toggleCodeBtn").onclick = () => {
                  document.getElementById("codePanel").classList.toggle("open");
               };
               // Ejecutar comandos (solo botón ✉️)
               document.getElementById("runBtn").onclick = () => {
                  ejecutarComandos();
               };
            </script>

            
         </body>
         </html>
      
      .. [END codigo-41-2]

   .. group-tab:: 41_main.py

      .. [START codigo-41-3]

      .. code-block:: python

         from fastapi import FastAPI, Request
         from pydantic import BaseModel
         import asyncio
         import json
         import paho.mqtt.client as mqtt
         from fastapi.middleware.cors import CORSMiddleware
         import json 

         mensaje_actuadores={
            "auto":{
               "activar"    : 1,
               "velocidad"  : 60, # 0 a 100%
               "led"        : 0, # 0 apagado, 1 abierto
               "gripper"    : 0, # 0 cerrado, 1 abierto
               "movimiento" : 0, # 0 detenido, 1 adelante, 2 giro izquierda, 3 giro derecha, 4 giro 180 grados. 
               },
            "manual":{
               "estado":      0,
               "vel_derecho": 60,
               "vel_izquierdo": 60,
               "angulo_gripper": 0,
               } 
         }

         app = FastAPI()

         app.add_middleware(
            CORSMiddleware,
            allow_origins=["*"],
            allow_credentials=True,
            allow_methods=["*"],
            allow_headers=["*"],
         )

         estado_event = asyncio.Event()
         sensor_event = asyncio.Event()

         # MQTT
         mqtt_client = None
         robot       = None

         broker = "localhost"
         port = 1883

         topic_estado  = "/estado"
         topic_acciones = "/acciones"
         topic_sensores = "/sensores"

         # Variables de sensores
         ultrasonico = 0
         qtr8 = 0

         def on_connect(client, userdata, flags, rc):
            print("Conectado a MQTT con resultado: ", rc)
            client.subscribe(robot+topic_estado)
            client.subscribe(robot+topic_sensores)

         def on_message(client, userdata, msg):
            global ultrasonico, qtr8

            #print(f"Mensaje MQTT: {msg.topic} => {msg.payload.decode()}")
            payload = msg.payload.decode()

            if msg.topic == robot+topic_estado:
               estado_event.set()

            if msg.topic == robot+topic_sensores:
               sensores = json.loads(payload)
               ultrasonico = sensores["ultra"]
               qtr8 = sensores["qtr8"]
               #print(f'Valor del ultrasonico {ultrasonico}, {qtr8}')
               sensor_event.set()
            

         # MODELO AJUSTADO para recibir lista de comandos
         class Mensaje(BaseModel):
            comandos: list

         @app.get("/")
         async def root():
            return {"mensaje": "API de control del robot con MQTT"}

         # 🚀 FUNCIÓN RECURSIVA PARA INTERPRETAR COMANDOS
         async def ejecutar_instruccion(instr):
            global mqtt_client, robot
            if instr["accion"] == "publicar":
               if instr["mensaje"]["valor"]   == "adelante":
                     mensaje_actuadores["auto"]["movimiento"] = 1
               elif instr["mensaje"]["valor"] == "giro_izquierda":
                     mensaje_actuadores["auto"]["movimiento"] = 2
               elif instr["mensaje"]["valor"] == "giro_derecha":
                     mensaje_actuadores["auto"]["movimiento"] = 3
               elif instr["mensaje"]["valor"] == "giro_180":
                     mensaje_actuadores["auto"]["movimiento"] = 4    
               mqtt_client.publish(robot+topic_acciones, json.dumps(mensaje_actuadores))
               try:
                     await asyncio.wait_for(estado_event.wait(), timeout=2)
                     mensaje_actuadores["auto"]["movimiento"] = 0
                     estado_event.clear()
               except asyncio.TimeoutError:
                     mensaje_actuadores["auto"]["movimiento"] = 0
                     print("⚠️ Timeout esperando /estado")

               await asyncio.sleep(1)
            
            if instr["accion"] == "leer_sensor":
               print(f'Valuar Actual del sensor: {ultrasonico}, {qtr8}')
               try:
                     await asyncio.wait_for(estado_event.wait(), timeout=5)
                     sensor_event.clear()
               except asyncio.TimeoutError:
                     print("⚠️ Timeout esperando /sensor")
               await asyncio.sleep(1)
            
            if instr["accion"] == "set_velocidad":
               mensaje_actuadores["auto"]["velocidad"] = instr["mensaje"]["porcentaje"]
               mqtt_client.publish(robot+topic_acciones, json.dumps(mensaje_actuadores))
               await asyncio.sleep(0.1)
            
            if instr["accion"] == "set_gripper":
               if instr["mensaje"]["valor"] == "Abrir":
                     mensaje_actuadores["auto"]["gripper"] = 1

               if instr["mensaje"]["valor"] == "Cerrar":
                     mensaje_actuadores["auto"]["gripper"] = 0
               mqtt_client.publish(robot+topic_acciones, json.dumps(mensaje_actuadores))
               await asyncio.sleep(0.1)

            elif instr["accion"] == "if":
               cond = instr["expresion"]
               if eval(f"{cond}"):
                     for sub in instr["entonces"]:
                        await ejecutar_instruccion(sub)
            
            elif instr["accion"] == "if_else":
               cond = instr["expresion"]
               if eval(f"{cond}"):
                     for sub in instr["entonces"]:
                        await ejecutar_instruccion(sub)
               else:
                     for sub in instr["sino"]:
                        await ejecutar_instruccion(sub)
                     
            elif instr["accion"] == "for":
               veces = instr.get("repeticiones", 1)
               for _ in range(veces):
                     for sub in instr["hacer"]:
                        await ejecutar_instruccion(sub)

            elif instr["accion"] == "esperar":
               time =  instr["mensaje"]["timer"]
               await asyncio.sleep(time)
            
            elif instr["accion"] == "iniciar":
               robot =  instr["mensaje"]["nombre"]
               mqtt_client = mqtt.Client()
               mqtt_client.on_connect = on_connect
               mqtt_client.on_message = on_message
               mqtt_client.connect(broker, port)
               mqtt_client.loop_start()
               await asyncio.sleep(2)

         async def ejecutar_script(script):
            for instruccion in script:
               await ejecutar_instruccion(instruccion)
            return {"status": "ok"}

         # ✅ CONEXIÓN ENTRE FRONTEND Y BACKEND
         @app.post("/api/comando")
         async def recibir_comandos(mensaje: Mensaje):
            print(f"➡️ Comandos recibidos: {mensaje.comandos}")
            resultado = await ejecutar_script(mensaje.comandos)
            return resultado


      .. [END codigo-41-3]

   .. group-tab:: 41_main.py

      .. [START codigo-41-4]

      .. code-block:: python

         import paho.mqtt.client as mqtt
         import json
         import time

         # Configuración del broker
         BROKER            = "localhost" # Ip del computador o localhost
         TOPIC_SUB         = "Rena1/acciones"
         TOPIC_PUB         = "Rena1/sensores"
         CLIENT_ID         = "cliente_rm1"

         # Callback cuando se conecta al broker
         def on_connect(client, userdata, flags, rc, properties=None):
            if rc == 0:
               print("Conectado al broker MQTT")
               client.subscribe(TOPIC_SUB)
            else:
               print(f"Error de conexión: código {rc}")

         # Callback al recibir un mensaje
         def on_message(client, userdata, msg):
            try:
               mensaje = msg.payload.decode("utf-8")
               data = json.loads(mensaje)
               if msg.topic == TOPIC_SUB:
                     print("Mensaje recido en la ESP32:", data["auto"])
            except Exception as e:
               print("Error procesando mensaje:", e)

         client = mqtt.Client(client_id=CLIENT_ID, protocol=mqtt.MQTTv311)

         # Asociar funciones de callback
         client.on_connect = on_connect
         client.on_message = on_message

         # Conexión al broker
         client.connect(BROKER)

         # Iniciar loop en segundo plano
         client.loop_start()

         # Envío continuo de mensajes cada segundo
         try:
            while True:
               payload = {
                     "ultra": 8,
                     "qtr8" : 5,    
               }
               mensaje = json.dumps(payload)
               client.publish(TOPIC_PUB, mensaje)
               time.sleep(1)

         except KeyboardInterrupt:
            print("\n Finalizando conexión MQTT...")

         finally:
            client.loop_stop()
            client.disconnect()
            print(" Desconectado correctamente.")

      .. [END codigo-41-4]



Aplicación Movil
----------------

Si tu objetivo es crear una App para celular (Android o iOS) basada en los index.html que ya tienes con Blockly, aquí van las mejores formas de convertirlo en una app móvil real.
🚀 OPCIÓN RECOMENDADA: Convertir a App Móvil con Capacitor.js + Ionic (o Cordova)

Esta es la forma más directa y moderna de transformar tu HTML + Blockly en una app móvil instalable.
✅ PASOS CON Capacitor.js (de Ionic)

1. 📁 Estructura tu proyecto

Organiza tus archivos:

.. code-block:: bash

   mi_app_movil/
   ├── index.html           ← Tu interfaz Blockly
   ├── 02_bloques.js        ← Tus bloques personalizados
   ├── main.css / estilos
   ├── capacitor.config.ts
   ├── android/             ← Se generará automáticamente
   ├── ios/                 ← Se generará si usas Mac

2. ⚙️ Instala Capacitor

.. code-block:: bash

   npm init -y
   npm install @capacitor/core @capacitor/cli
   npx cap init blocklyApp com.avigtech.blocklyapp

* App name: blocklyApp

* App id: com.avigtech.blocklyapp

3. 📂 Crea una carpeta www/ y mueve tus archivos

.. code-block:: bash

   mkdir www
   mv index.html www/
   mv *.js www/

4. 🚀 Agrega plataforma Android

..  code-block:: bash

   npx cap add android

5. 📲 Abre y compila la app en Android Studio

..  code-block:: bash

   npx cap open android

Desde ahí puedes compilar y generar el APK para instalarlo en tu celular.
✅ Para enviar datos a backend o MQTT

Si ya tienes WebSocket o MQTT en tu HTML, funciona igual en el celular, solo asegúrate de que la IP sea accesible desde el dispositivo (por ejemplo, ws://192.168.X.X:5678).
✅ ¿Y para iOS?

Puedes hacer npx cap add ios, pero necesitas macOS + Xcode. Android es mucho más directo.
📦 ¿Y si no quieres usar Capacitor?

También puedes usar:

* Cordova (más antiguo)

* TWA (Trusted Web Activities) para empaquetar un sitio web como app en Android

* PWA (Progressive Web App) que se instala desde Chrome sin subir a Play Store

🧠 ¿Qué debo cuidar en mi index.html?

* Elimina rutas absolutas (file:///) y usa solo archivos locales o relativos

* Evita librerías externas si no tienes conexión

* Si usas WebSocket/MQTT, el servidor debe estar accesible desde el celular
