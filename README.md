# Flow5-NodeRed-Clima y Base de datos
Este repositorio contiene el ejercicio del flow 5 de NodeRed, basandose en el contenido de Internet de las cosas de [Codigo IoT](http://edu.codigoiot.com "Codigo IoT") con almacenamiento en MySQL

## Introducción
Este flow consisten en un tablero que representa en forma de gráficos,  la información de temperatura y humedad locales. Para ello se recibe información con la ayuda de un API en [openweathermap.org](https://openweathermap.org/api "openweathermap.org").
También instalaremos un servidor de base de datos, en este caso MySQL para almacenar los datos que obtenemos de la API.
## Material necesario
- [Ubuntu 20.04](https://releases.ubuntu.com/20.04/ "Ubuntu 20.04")
- [NodeJS](https://nodejs.org/es/ "NodeJS")
 - [NPM](https://www.npmjs.com/ "NPM")
 - [NodeRed](https://nodered.org/ "NodeRed")
 - [Nodos de Dashboard](https://flows.nodered.org/node/node-red-dashboard "Nodos de Dashboard")
- [Mosquito MQTT](https://mosquitto.org/ "Mosquito MQTT")
- [Cuenta en OpenWeather](https://openweathermap.org/ "Cuenta en OpenWeather")
- [MySQL](https://www.mysql.com/ "MySQL")

## Referencias
Enlaces de apoyo para las configuraciones:
- [Instalación de Virtual Box y Ubuntu 20.04](https://edu.codigoiot.com/course/view.php?id=812 "nstalación de Virtual Box y Ubuntu 20.04")
- [Instalación de NodeRed](https://edu.codigoiot.com/course/view.php?id=817 "Instalación de NodeRed")
- [Introducción de NodeRed](https://edu.codigoiot.com/course/view.php?id=278 "Introducción de NodeRed")
- [Introducción a Mosquitto](https://edu.codigoiot.com/course/view.php?id=851 "Introducción a Mosquitto")
- [APIs de OpenWather](https://openweathermap.org/api "APIs de OpenWather")
- [MySQL documentación](https://dev.mysql.com/doc/ "MySQL documentación")
## Instrucciones
### Requisitos previos
1. **Instalación de NodeJS. **Se recomienda tener instalado NodeJS en alguna versión LTS. Al momento de creación de este documento, se usó la versión 16.17.0LTS. Esta instalación debe incluir las Build-Tools para hacer uso de NPM
2. **Instalación de NodeRed.**La instalación se realiza por NPM. Al momento de la creación de este contenido, se usó la versión 3.0.2
3. **Instalación de nodos node-red-dashboard.** Para ello, dirigete a la opcion "Manage Palet" de NodeRed y en la pestaña Install busca node-red-dashboard. Finalmente haz clic en instalar.
4. **Instalación de Broker local Mosquitto MQTT.**
5. **Cuenta de OpenWeather.** Es necesario tener una cuenta creada en OpenWeather, la cual nos proporcionará la información de temperatura y humedad.
6. **Coordenadas (latitud y longitud) de algún lugar. **
7. **Usar HiveMQ como broker público**
8. **Instalación de MySQL.**

### Intrucciones de preparación del entorno
1. Arrancar NodeRed con el comando `node-red`.
2. Importar el flow desde el repositorio.
3. Agregamos un nodo inject y un http request.
4. Actualizar la IP del broker público.
5. Actualizar los nodos, agregando uno nuevo para SQL.
6. Hacer clic en el boton Deploy.

### Notas
- Para la instalación de MySQL server, necesitamos utilizar el comando en la terminal `sudo apt install mysql-server`.
- Iniciamos la terminal de MySQL con `sudo mysql`
- Agregaremos un usuario para evitar trabajar como root. Para ello utilizamos la siguiente consulta `CREATE USER 'usuario'@'localhost' IDENTIFIED BY 'contraseña';`
- Creamos una base de datos, así como una tabla para almacenar los datos que recibiremos
 - Crear base de datos: `CREATE DATABASE NombreBD;`(la usamos con `USE Nombre`).
 - Crear tabla: `CREATE TABLE clima(id INT (6) UNSIGNED AUTO_INCREMENT PRIMARY KEY, fecha TIMESTAMP DEFAULT CURRENT_TIMESTAMP, nombre CHAR(248) NOT NULL, temperatura FLOAT(4,2), humedad INT (3));`
- En cualquier caso si llegamos a olvidar la estructura de la base de datos usamos la consulta : `DESCRIBE NombreBD`;
### Intrucciones de operación
- Para observar el resultado del flow, es necesario abrir un navegador  y dirigirse a localhost:1880/ui
- Con la API que obtenemos en la documentación `https://api.openweathermap.org/data/2.5/weather?lat={lat}&lon={lon}&appid={API key}`, colocamos las coordenadas de latitud en (lat) y longitud en (lon), así como la API key que obtenes al crear la cuenta en OpenWeather.
- Para poder actualizar la IP del broker público, utilizamos el comando `nslookup broker.hivemq.com` (aunque podemos utilizar cualquier broker público ).
- Colocamos la URL en el nodo http request.
- En el inject (timestamp) configuramos el intervalo para que este mande un mensaje cada minuto al nodo http request.
- Cambiamos los nodos de function con el siguiente código `msg.payload = msg.payload.main.temp;`
- Una vez actualizado los nodos, agregamos nuestro nuevo nodo SQL, lo configuramos colocando la dirección donde está alojada la base de datos, por ahora utilizamos el localhost. Cabe recalcar que debimos haber agregado un usuario para porder utilizar esta bd (para no utilizar al usuario root).
- Añadimos otro nodo ***Function*** ,  éste será el que hará los Querys. Dentro del nodo function pondremos la consulta con la siguiente definición: `msg.topic = "INSERT INTO clima ('nombre','temperatura','humedad') VALUES ('Usuario'," + global.get ("tempAPI")+ "," + global.get ("humAPI") + ");";`. 

### Resultados y Evidencias
**Nodos del flow**
![](https://github.com/HilarioBarcenas/flow-5-Clima-y-Bases-de-datos/blob/main/Capturas/NodoFlow5CBD.png?raw=true)

**Tablero resulado del flow**
![](https://github.com/HilarioBarcenas/flow-5-Clima-y-Bases-de-datos/blob/main/Capturas/DashboardFlow5CBD.png?raw=true)

**Consulta en la terminal de MySQL**
![](https://github.com/HilarioBarcenas/flow-5-Clima-y-Bases-de-datos/blob/main/Capturas/Query.png?raw=true)

### Evidencias

[YouTube](https://youtu.be/yonDjBt2Nt0)

### Créditos
**Desarrollado por** `Hilario Barcenas`

[Github](https://github.com/HilarioBarcenas "Github")