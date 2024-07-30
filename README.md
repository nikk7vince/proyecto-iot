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
        "id": "flow_sensor",
        "type": "rpi-gpio in",
        "z": "1",
        "name": "Flow Sensor Input",
        "pin": "11",
        "intype": "down",
        "debounce": "25",
        "read": false,
        "wires": [["func_calcular_caudal"]]
    },
    {
        "id": "pressure_sensor",
        "type": "rpi-gpio in",
        "z": "1",
        "name": "Pressure Sensor Input",
        "pin": "12",
        "intype": "down",
        "debounce": "25",
        "read": false,
        "wires": [["func_medicion_presion"]]
    },
    {
        "id": "ultrasonic_sensor",
        "type": "rpi-gpio in",
        "z": "1",
        "name": "Ultrasonic Sensor Input",
        "pin": "13",
        "intype": "down",
        "debounce": "25",
        "read": false,
        "wires": [["func_medicion_nivel_agua"]]
    },
    {
        "id": "air_quality_sensor",
        "type": "rpi-gpio in",
        "z": "1",
        "name": "Air Quality Sensor Input",
        "pin": "14",
        "intype": "down",
        "debounce": "25",
        "read": false,
        "wires": [["func_medicion_calidad_agua"]]
    },
    {
        "id": "func_calcular_caudal",
        "type": "function",
        "z": "1",
        "name": "Calcular Caudal",
        "func": "let pulseCount = context.get('pulseCount') || 0;\nlet lastTime = context.get('lastTime') || Date.now();\nlet flowRate = 0;\n\npulseCount += 1;\ncontext.set('pulseCount', pulseCount);\n\nlet currentTime = Date.now();\nlet elapsedTime = (currentTime - lastTime) / 1000;\nif (elapsedTime >= 1) {\n    flowRate = (pulseCount / 7.5) / elapsedTime;\n    pulseCount = 0;\n    context.set('pulseCount', pulseCount);\n    context.set('lastTime', currentTime);\n}\n\nmsg.payload = { flowRate: flowRate, pulseCount: pulseCount };\nreturn msg;",
        "outputs": 1,
        "wires": [["control_actuadores", "mysql_flow", "func_detectar_anomalias_flow", "debug_flow"]]
    },
    {
        "id": "func_medicion_presion",
        "type": "function",
        "z": "1",
        "name": "Medir Presión",
        "func": "let pressure = msg.payload;\nmsg.payload = { pressure: pressure };\nreturn msg;",
        "outputs": 1,
        "wires": [["control_actuadores", "mysql_pressure", "func_detectar_anomalias_presion", "debug_pressure"]]
    },
    {
        "id": "func_medicion_nivel_agua",
        "type": "function",
        "z": "1",
        "name": "Medir Nivel de Agua",
        "func": "let waterLevel = msg.payload;\nmsg.payload = { waterLevel: waterLevel };\nreturn msg;",
        "outputs": 1,
        "wires": [["control_actuadores", "mysql_nivel_agua", "func_detectar_anomalias_nivel_agua", "debug_nivel_agua"]]
    },
    {
        "id": "func_medicion_calidad_agua",
        "type": "function",
        "z": "1",
        "name": "Medir Calidad de Agua",
        "func": "let airQuality = msg.payload;\nmsg.payload = { airQuality: airQuality };\nreturn msg;",
        "outputs": 1,
        "wires": [["control_actuadores", "mysql_calidad_agua", "func_detectar_anomalias_calidad_agua", "debug_calidad_agua"]]
    },
    {
        "id": "func_detectar_anomalias_flow",
        "type": "function",
        "z": "1",
        "name": "Detectar Anomalías Flow",
        "func": "let flowRate = msg.payload.flowRate;\nif (flowRate > 10 || flowRate < 1) {\n    msg.payload = `Alerta: Tasa de flujo anómala detectada - ${flowRate} L/min`;\n    return [msg, null];\n} else {\n    return [null, msg];\n}",
        "outputs": 2,
        "wires": [["email_flow"], ["debug_flow"]]
    },
    {
        "id": "func_detectar_anomalias_presion",
        "type": "function",
        "z": "1",
        "name": "Detectar Anomalías Presión",
        "func": "let pressure = msg.payload.pressure;\nif (pressure < 1) {\n    msg.payload = `Alerta: Presión baja detectada - ${pressure} bar`;\n    return [msg, null];\n} else {\n    return [null, msg];\n}"
    }
]
``` 
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

