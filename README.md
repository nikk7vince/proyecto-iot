# ATL-Guardian  
Detectar fugas y captar agua de las lluvia, con sensores que envian una notificacion a nuestro celular
_Este proyecto utiliza Node-RED para gestionar diferentes sensores y actuar en consecuencia_
## Tabla de Contenidos

- [Instalación](#instalación)
- [Uso](#uso)
- [Contribución](#contribución)
- [Licencia](#licencia)
- [Autores](#autores)
- [Agradecimientos](#agradecimientos)
## Instalación 
Para instalar el proyecto 
```bash
# Clona el repositorio
De la parte del codigo copiarlo y colocarlo en la terminal .
git clone https://github.com/tu-usuario/tu-proyecto.git

## Uso
Para importar el flujo de Node-RED, copia el siguiente código y pégalo en el editor de importación de Node-RED.
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
