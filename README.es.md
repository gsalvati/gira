# G.I.R.A
# Firmware de Tocadiscos Inteligente

*Lea esto en otros idiomas: [English](README.md) | [Português](README.pt.md)*

Este repositorio contiene el firmware para un proyecto de **Tocadiscos Inteligente** basado en el microcontrolador ESP32-C3. Utiliza control de motor paso a paso de alta precisión, lectura angular avanzada e interfaz web para ofrecer una experiencia automatizada y moderna de reproducción de discos de vinilo.

## 📸 Vista previa de la interfaz web

<p align="center">
  <a href="assets/webApp-01.png"><img src="assets/webApp-03.png" width="45%" alt="Interfaz Web 1"></a>
  <a href="assets/webApp-02.png"><img src="assets/webApp-04.png" width="45%" alt="Interfaz Web 2"></a>
</p>

## 🌟 Características y funcionalidades

### 1. Control preciso y silencioso del plato (TMC2209)
* **Microstepping y StealthChop2**: Usa el driver TMC2209 para controlar el motor paso a paso (por ejemplo, NEMA17) de forma ultrasilenciosa y suave, minimizando resonancias y asegurando audio de alta fidelidad.
* **Ajuste de RPM**: Soporte nativo para 33 ⅓ RPM y 45 RPM, configurable de forma remota vía panel web, con cálculos de velocidad precisos enviados por comunicación UART.
* **Rampas de aceleración**: Cuenta con aceleración y desaceleración suaves para iniciar y detener la rotación del plato de forma gradual.

### 2. Lectura angular del brazo (Tonearm)
* **Sensor magnético MT6701**: Realiza la lectura absoluta de la posición y el ángulo del brazo del tocadiscos usando el bus I2C.
* **Detección automática**: Puede identificar el movimiento inicial y detectar el final del disco de vinilo según la angulación, deteniendo el motor automáticamente.

### 3. Elevación automática de la aguja (Tonearm Lift)
* **Servo integrado**: Actúa en el mecanismo de elevación del brazo, garantizando que la aguja baje o suba con precisión mecánica y seguridad.
* **Operación integrada**: Al mover el brazo hacia el plato, después de un retraso seguro (debounce programable), el motor se enciende y la aguja baja suavemente. Cuando se detecta el límite final del disco, la aguja se levanta y la rotación se detiene.

### 4. Interfaz web y monitoreo
* **Web App (alojamiento interno)**: Aplicación PWA (HTML/JS/CSS) completamente almacenada en el propio microcontrolador gracias a LittleFS.
* **WebSockets en tiempo real**: Comunicación bidireccional continua de telemetría, mostrando la velocidad actual, el ángulo del brazo y el estado de la elevación en el navegador o la pantalla del smartphone.
* **Modos de control**: Cambia entre modo **Automático** o **Manual** sin necesidad de cambiar el código.

### 5. Configuración inteligente de WiFi (modo Punto de Acceso)
* **Sin credenciales codificadas**: El SSID y la contraseña de WiFi nunca se almacenan en el código fuente; las credenciales se guardan de forma segura en la NVS del ESP32.
* **Punto de acceso automático**: En el primer arranque (o si la red guardada no está disponible), el dispositivo crea automáticamente una red WiFi abierta llamada `GIRA_Setup`.
* **Portal de configuración**: Conéctese a `GIRA_Setup` y vaya a `http://192.168.4.1` — el dispositivo sirve una página para ingresar las credenciales de su red doméstica. Después de guardar, el ESP32 se reinicia y se conecta automáticamente.
* **Reconfiguración en tiempo real**: Una vez conectado a su red doméstica, la configuración de WiFi puede actualizarse en cualquier momento desde la página `config.html` sin volver a flashear el firmware.

### 6. Sistema moderno y conectividad
* **OTA (Over-The-Air)**: Capacidad para actualizar el firmware de forma remota por la red Wi-Fi, sin necesidad de cable USB después del montaje.
* **Depuración por Telnet**: Servidor Telnet integrado en el puerto 23 que actúa como monitor serial virtual para diagnóstico y seguimiento práctico.
* **Persistencia de datos**: Configuraciones detalladas como límites máximo/mínimo del servo y tiempo de debounce se guardan permanentemente en la memoria NVS.

## 🛠️ Stack y hardware
* **Microcontrolador**: Seeed Studio XIAO ESP32C3
* **Driver del motor**: TMC2209 (Comunica via UART y modo Legacy)
* **Transductor de rotación**: Motor paso a paso NEMA17
* **Sensor de posición**: MT6701 Magnético
* **Actuador de elevación**: Micro Servo
* **Framework**: PlatformIO con interfaz Arduino

## 🧩 Archivos para impresión 3D
Los modelos 3D necesarios para imprimir están disponibles en `assets/stl`. Descarga el archivo ZIP con todos los STL o el proyecto 3MF antes de imprimir:
* [Descargar todos los archivos STL (ZIP)](assets/stl/gira-stl.zip)
* [Descargar proyecto 3MF](assets/stl/GIRA.3mf)

## � Esquemáticos y documentación
Los esquemáticos de cableado están disponibles en [assets/Schematics.pdf](assets/Schematics.pdf). Descárgalo antes de conectar los componentes.
Para más información y explicaciones sobre el tocadiscos, visita [https://labs.salvati.com.br](https://labs.salvati.com.br).

## �📌 Configuración de pines (Pinout)
Según el mapeo del proyecto:
* **TMC2209 (UART)**: `RX = Pin 5` / `TX = Pin 4` / `Enable = Pin 2`
* **Servo**: `Pin 10`
* **Sensor MT6701 (I2C)**: `SDA = Pin 6` / `SCL = Pin 7`

## ⚙️ Instalación y despliegue (PlatformIO)

1. Clona el repositorio y abre la carpeta raíz en un IDE con PlatformIO (como VS Code).
2. Asegúrate de ejecutar la tarea **Upload Filesystem Image** (en el menú PlatformIO) para enviar la carpeta `data/` que contiene la interfaz web y la página de configuración de WiFi al LittleFS del ESP32.
3. Ejecuta **Build** y luego **Upload** de la aplicación por cable.
4. En el primer arranque, el dispositivo emitirá una red WiFi abierta llamada **`GIRA_Setup`**.
5. Conéctate a ella y abre `http://192.168.4.1` en el navegador para ingresar las credenciales de tu red doméstica.
6. Después de guardar, el dispositivo se reinicia y se conecta a tu red. Accédelo en `http://gira.local` o por la IP asignada.
