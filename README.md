# ATL-Guardian  
Detectar fugas y captar agua de las lluvia, con sensores que envian una notificacion a nuestro celular

**Este proyecto utiliza Node-RED para gestionar diferentes sensores y actuar en consecuencia**
## Tabla de Contenidos

- [Instalación](#instalación)
- [Uso](#uso)
- [Contribución](#contribución)
- [Integrantes](#Integrantes)
- [Agradecimientos](#agradecimientos)
## Instalación 
Para instalar el proyecto 
```bash
# Clona el repositorio
De la parte del codigo copiarlo y colocarlo en la terminal .
git clone https://github.com/tu-usuario/tu-proyecto.git

# Entra en el directorio del proyecto
cd tu-proyecto

# Instala las dependencias
npm install
```
## Uso

 Para importar el flujo de Node-RED, copia el siguiente código y pégalo en el editor de importación de Node-RED.
```bash
[
    {
        "id": "63625b706ff6379a",
        "type": "tab",
        "label": "Flow 9",
        "disabled": true,
        "info": "",
        "env": []
    },
    {
        "id": "6e98fd523b1bf1ed",
        "type": "mqtt in",
        "z": "63625b706ff6379a",
        "name": "",
        "topic": "codigoIoT/mqtt/flujo",
        "qos": "2",
        "datatype": "auto-detect",
        "broker": "a9128d20175a87ac",
        "nl": false,
        "rap": true,
        "rh": 0,
        "inputs": 0,
        "x": 230,
        "y": 140,
        "wires": [
            [
                "a4ff96f0f1498bb8",
                "a6f4bab881826c11",
                "eac75981ce9b80a1",
                "204c50015f4ab441"
            ]
        ]
    },
    {
        "id": "a4ff96f0f1498bb8",
        "type": "function",
        "z": "63625b706ff6379a",
        "name": "Extraer Temp",
        "func": "msg.topic =msg.payload.ID;\nmsg.payload=msg.payload.temp;\nreturn msg;",
        "outputs": 1,
        "timeout": 0,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 450,
        "y": 100,
        "wires": [
            [
                "027af0bb582ea761",
                "897919b988262090"
            ]
        ]
    },
    {
        "id": "a6f4bab881826c11",
        "type": "function",
        "z": "63625b706ff6379a",
        "name": "Extraer Hum",
        "func": "msg.topic = msg.payload.ID;\nmsg.payload=msg.payload.hum;\n\nreturn msg;",
        "outputs": 1,
        "timeout": 0,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 450,
        "y": 180,
        "wires": [
            [
                "e068672a21c3d970",
                "897919b988262090"
            ]
        ]
    },
    {
        "id": "027af0bb582ea761",
        "type": "ui_gauge",
        "z": "63625b706ff6379a",
        "name": "",
        "group": "08d40e027fb0f7f9",
        "order": 0,
        "width": 0,
        "height": 0,
        "gtype": "gage",
        "title": "Temp. Actual",
        "label": "°C",
        "format": "{{value}}",
        "min": 0,
        "max": "50",
        "colors": [
            "#20cdff",
            "#2ee600",
            "#ff0202"
        ],
        "seg1": "",
        "seg2": "",
        "diff": false,
        "className": "",
        "x": 730,
        "y": 60,
        "wires": []
    },
    {
        "id": "e068672a21c3d970",
        "type": "ui_gauge",
        "z": "63625b706ff6379a",
        "name": "",
        "group": "f323906c191efaa9",
        "order": 0,
        "width": 0,
        "height": 0,
        "gtype": "wave",
        "title": "Hum. Actual",
        "label": "%",
        "format": "{{value}}",
        "min": 0,
        "max": "100",
        "colors": [
            "#00b500",
            "#e6e600",
            "#ca3838"
        ],
        "seg1": "",
        "seg2": "",
        "diff": false,
        "className": "",
        "x": 730,
        "y": 260,
        "wires": []
    },
    {
        "id": "897919b988262090",
        "type": "ui_chart",
        "z": "63625b706ff6379a",
        "name": "",
        "group": "b78994697d2872cb",
        "order": 0,
        "width": 0,
        "height": 0,
        "label": "Temp. y Hum. en el tiempo",
        "chartType": "line",
        "legend": "true",
        "xformat": "HH:mm:ss",
        "interpolate": "linear",
        "nodata": "Esperando datos",
        "dot": false,
        "ymin": "0",
        "ymax": "100",
        "removeOlder": "12",
        "removeOlderPoints": "",
        "removeOlderUnit": "3600",
        "cutout": 0,
        "useOneColor": false,
        "useUTC": false,
        "colors": [
            "#ff790b",
            "#31d0ff",
            "#006fbf",
            "#2ca02c",
            "#98df8a",
            "#d62728",
            "#ff9896",
            "#9467bd",
            "#c5b0d5"
        ],
        "outputs": 1,
        "useDifferentColor": false,
        "className": "",
        "x": 760,
        "y": 140,
        "wires": [
            []
        ]
    },
    {
        "id": "0e268b4d2f6e2142",
        "type": "mysql",
        "z": "63625b706ff6379a",
        "mydb": "06fc599aef97a462",
        "name": "",
        "x": 770,
        "y": 380,
        "wires": [
            []
        ]
    },
    {
        "id": "eac75981ce9b80a1",
        "type": "function",
        "z": "63625b706ff6379a",
        "name": "Generar consulta",
        "func": "// Verificar que los datos de la carga útil no estén indefinidos\nvar id = msg.payload.ID || 'Jose23_03'; // Usa el ID proporcionado o un valor predeterminado\nvar temp = msg.payload.temp;\nvar hum = msg.payload.hum;\nvar caud;\n\n// Asegurarse de que flow_rate tenga un valor y formatearlo\nif (msg.payload.flow_rate) {\n    caud = msg.payload.flow_rate + \" Litros/min\";\n} else {\n    caud = \"0.00 Litros/min\";\n}\n\n// Generar inicio del query\nmsg.topic = \"INSERT INTO agua (nombre, temperatura, humedad, Caudal) VALUES (\";\n// Concatenar identidad del usuario\nmsg.topic += \"'\" + id + \"'\" + \", \";\n// Concatenar temperatura\nmsg.topic += temp + \", \";\n// Concatenar humedad\nmsg.topic += hum + \", \";\n// Concatenar CAUDAL como cadena con el formato adecuado\nmsg.topic += \"'\" + caud + \"'\" + \");\";\nreturn msg;\n\n\n",
        "outputs": 1,
        "timeout": 0,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 470,
        "y": 380,
        "wires": [
            [
                "0e268b4d2f6e2142"
            ]
        ]
    },
    {
        "id": "c712985e7d2f659c",
        "type": "inject",
        "z": "63625b706ff6379a",
        "name": "",
        "props": [
            {
                "p": "topic",
                "vt": "str"
            }
        ],
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "topic": "INSERT INTO agua (nombre, temperatura, humedad, Caudal) VALUES ('VINCE_NK', 24, 65, '120.50 Litros/min - Sensor en el tanque de agua');",
        "x": 470,
        "y": 520,
        "wires": [
            [
                "0e268b4d2f6e2142"
            ]
        ]
    },
    {
        "id": "204c50015f4ab441",
        "type": "function",
        "z": "63625b706ff6379a",
        "name": "function 12",
        "func": "msg.topic = msg.payload.ID;\nmsg.payload = msg.payload.flow_rate;\n\n\nreturn msg;",
        "outputs": 1,
        "timeout": 0,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 490,
        "y": 280,
        "wires": [
            []
        ]
    },
    {
        "id": "a9128d20175a87ac",
        "type": "mqtt-broker",
        "name": "Mosquitto RPi",
        "broker": "localhost",
        "port": "1883",
        "clientid": "",
        "autoConnect": true,
        "usetls": false,
        "protocolVersion": "4",
        "keepalive": "60",
        "cleansession": true,
        "autoUnsubscribe": true,
        "birthTopic": "",
        "birthQos": "0",
        "birthRetain": "false",
        "birthPayload": "",
        "birthMsg": {},
        "closeTopic": "",
        "closeQos": "0",
        "closeRetain": "false",
        "closePayload": "",
        "closeMsg": {},
        "willTopic": "",
        "willQos": "0",
        "willRetain": "false",
        "willPayload": "",
        "willMsg": {},
        "userProps": "",
        "sessionExpiry": ""
    },
    {
        "id": "08d40e027fb0f7f9",
        "type": "ui_group",
        "name": "Temperatura",
        "tab": "7d055cfb531cd518",
        "order": 1,
        "disp": true,
        "width": "6",
        "collapse": false,
        "className": ""
    },
    {
        "id": "f323906c191efaa9",
        "type": "ui_group",
        "name": "Humedad",
        "tab": "7d055cfb531cd518",
        "order": 2,
        "disp": true,
        "width": "6",
        "collapse": false,
        "className": ""
    },
    {
        "id": "b78994697d2872cb",
        "type": "ui_group",
        "name": "Histórico",
        "tab": "7d055cfb531cd518",
        "order": 3,
        "disp": true,
        "width": "6",
        "collapse": false,
        "className": ""
    },
    {
        "id": "06fc599aef97a462",
        "type": "MySQLdatabase",
        "name": "",
        "host": "127.0.0.1",
        "port": "3306",
        "db": "proyecto_pr",
        "tz": "-07:00",
        "charset": "UTF8"
    },
    {
        "id": "7d055cfb531cd518",
        "type": "ui_tab",
        "name": "Clima",
        "icon": "wi-wu-mostlysunny",
        "order": 2,
        "disabled": false,
        "hidden": false
    }
]
## Contribución

¡Gracias por tu interés en contribuir a este proyecto! Aquí te explicamos cómo puedes colaborar.

### Cómo Contribuir

1. **Haz un Fork del Proyecto**

    Haz un fork del repositorio en tu cuenta de GitHub haciendo clic en el botón "Fork" en la parte superior derecha de la página del repositorio.

2. **Clona tu Fork**

    Clona el repositorio forkeado a tu máquina local para que puedas empezar a trabajar en él.
    ```bash
    git clone https://github.com/tu-usuario/tu-proyecto-forkeado.git
    cd tu-proyecto-forkeado
    ```

3. **Crea una Nueva Rama**

    Es una buena práctica crear una nueva rama para trabajar en tus cambios. Esto mantiene tu trabajo organizado y separado de la rama principal.
    ```bash
    git checkout -b nombre-de-tu-rama
    ```

4. **Realiza tus Cambios**

    Realiza los cambios que deseas en el código

5. **Añade y Haz Commit de tus Cambios**

    Añade los archivos modificados a la zona de preparación y haz commit de los cambios con un mensaje descriptivo.
    ```bash
    git add . 
    git commit -m "Descripción de tus cambios"
    ```

6. **Haz Push de tu Rama**

    Sube tus cambios a tu fork en GitHub.
    ```bash
    git push origin nombre-de-tu-rama
    ```

7. **Abre un Pull Request**

    Ve a la página de tu fork en GitHub y haz clic en el botón "Compare & pull request". Llena la información requerida y crea el pull request. Describe tus cambios y por qué deberían fusionarse.



## Integrantes

- García Gutiérrez José Manuel
- Montoya Castillo Alberto
- Ruíz Morelos Abraham

Mira la lista de [contribuyentes](https://github.com/tu-usuario/tu-proyecto/contributors) que participaron en este proyecto.

### Agradecimientos
- Agradecemos tu interés en contribuir a este proyecto. Tus contribuciones son valiosas y ayudan a mejorar el proyecto para todos los usuarios.

- Agradecemos a  [Conocimiento Libre](https://conocimientolibre.mx/)  por capacitarnos e  impulsar este proyecto 

