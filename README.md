# ATL-Guardian  
Detectar fugas y captar agua de las lluvia, con sensores que envian una notificacion a nuestro celular

**Este proyecto utiliza Node-RED para gestionar diferentes sensores y actuar en consecuencia**
Promueve el control de consumo de agua consiente y otras aplicaciones
## Tabla de Contenidos

- [Instalación](#instalación)
- [Uso](#uso)
- [Contribución](#contribución)
- [Integrantes](#Integrantes)
- [Agradecimientos](#agradecimientos)
## Instalación 
Para instalar el proyecto 
```bash
Código en python : Recuerda instalar mqtt y actualizar los paquetes 
#!/usr/bin/python
import os
import glob
import time
import paho.mqtt.client as mqtt
import json
import RPi.GPIO as GPIO

# Configuración del cliente MQTT
broker = "localhost"
port = 1883
topic = "codigoIoT/mqtt/flujo"
client_id = "Jose23_03"

# Configuración de los pines GPIO
GPIO.setmode(GPIO.BOARD)
GPIO.setup(23, GPIO.OUT)
GPIO.setup(10, GPIO.IN, pull_up_down=GPIO.PUD_DOWN)
GPIO.setup(12, GPIO.IN, pull_up_down=GPIO.PUD_DOWN)
GPIO.setup(13, GPIO.IN, pull_up_down=GPIO.PUD_UP)

# Funciones para conectarse al broker MQTT y publicar mensajes
def connect_mqtt():
    client = mqtt.Client(client_id=client_id)
    client.on_connect = on_connect
    client.on_disconnect = on_disconnect
    client.connect(broker, port)
    client.loop_start()
    return client

def on_connect(client, userdata, flags, rc):
    if rc == 0:
        print("Connected to MQTT Broker!")
    else:
        print(f"Failed to connect, return code {rc}")

def on_disconnect(client, userdata, rc):
    print("Disconnected from MQTT Broker")

def publish(client, topic, message):
    try:
        result = client.publish(topic, message)
        status = result[0]
        if status == 0:
            print(f"Published: {message} to topic: {topic}")
        else:
            print(f"Failed to send message to topic {topic}")
    except RuntimeError as error:
        print(f"Error publishing data: {error}")

# Funciones para leer los sensores
def read_flow_sensor():
    global count
    global last_state
    current_state = GPIO.input(13)
    if current_state != last_state:
        if current_state == GPIO.LOW:
            count += 1
        last_state = current_state

def read_temperature():
    list = os.listdir("/sys/bus/w1/devices/")
    if "w1_bus_master1" in list:
        list.remove("w1_bus_master1")
    file_count = len(list)
    if file_count > 0:
        ruta = glob.glob('/sys/bus/w1/devices/' + '28*')[0]
        if os.path.exists(ruta):
            with open(ruta + '/w1_slave') as fSensor:
                linSensor = fSensor.readlines()
                if len(linSensor) > 0:
                    posTemp = linSensor[1].find('t=')
                    if posTemp != -1:
                        strTemp = linSensor[1][posTemp+2:]
                        return float(strTemp) / 1000.0
    return None

# Conectar al broker MQTT
client = connect_mqtt()

# Variables globales
count = 0
last_state = GPIO.input(13)
estado_anterior = False

try:
    while True:
        try:
            # Leer el sensor de flujo
            start_time = time.time()
            count = 0
            while time.time() - start_time < 3:
                read_flow_sensor()
            flow = (count * 60 * 2.25 / 1000)

            # Leer el sensor de temperatura
            temp = read_temperature()

            # Leer el nivel de agua
            estado_pin10 = GPIO.input(10)
            estado_pin12 = GPIO.input(12)
            GPIO.output(23, estado_pin10)
            if estado_pin10 and not estado_anterior:
                porcentaje = 80
                print("Nivel de agua alto, tienes 5 minutos para apagar la bomba")
            elif estado_pin12 and not estado_pin10:
                porcentaje = 50
                print("Nivel de agua medio")
            elif not estado_pin10 and not estado_pin12:
                porcentaje = 10
                print("Nivel de agua bajo")

            # Formatear flow_rate para incluir "Litros/min"
            flow_rate_str = f"{flow:.2f} Litros/min"

            # Publicar el flujo, temperatura y nivel del agua en un solo tema
            if temp is not None:
                print(f"Temperatura: {temp} C")
                print(f"Flujo: {flow_rate_str}")
                print(f"Nivel de agua: {porcentaje}%")
                message = {
                    "ID": client_id,
                    "temp": temp,
                    "flow_rate": flow_rate_str,
                    "water_level": porcentaje
                }
                publish(client, topic, json.dumps(message))
            estado_anterior = estado_pin10
            time.sleep(5)
        except KeyboardInterrupt:
            print('\nInterrupción por teclado')
            GPIO.cleanup()
            client.loop_stop()
            break
        except Exception as e:
            print(f"Error: {e}")
            GPIO.cleanup()
            client.loop_stop()
except Exception as e:
    print(f"Error general: {e}")
    GPIO.cleanup()
    client.loop_stop()

# Clona el repositorio
De la parte del codigo copiarlo y colocarlo en la terminal .
git clone https://github.com/tu-usuario/tu-proyecto.git

# Entra en el directorio del proyecto
cd tu-proyecto

# Instala las dependencias
npm install-este flujo en node red utiliza nodos de  telegram 
```
## Uso

 Para importar el flujo de Node-RED, copia el siguiente código y pégalo en el editor de importación de Node-RED.
```bash
[
    {
        "id": "885338b9862bf770",
        "type": "ui_gauge",
        "z": "87f7775ba2d86b8a",
        "name": "",
        "group": "114c7ecd83be776e",
        "order": 0,
        "width": 0,
        "height": 0,
        "gtype": "wave",
        "title": "TINACO",
        "label": "%",
        "format": "{{value}}",
        "min": "0",
        "max": "100",
        "colors": [
            "#e01b24",
            "#e6e600",
            "#1a5fb4"
        ],
        "seg1": "",
        "seg2": "",
        "diff": false,
        "className": "",
        "x": 520,
        "y": 560,
        "wires": []
    },
    {
        "id": "114c7ecd83be776e",
        "type": "ui_group",
        "name": "Nivel",
        "tab": "7d055cfb531cd518",
        "order": 1,
        "disp": true,
        "width": "6",
        "collapse": false,
        "className": ""
    },
    {
        "id": "7d055cfb531cd518",
        "type": "ui_tab",
        "name": "Clima",
        "icon": "opacity",
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

