# Flow5 Clima con almacenamiento en MySQL y gráficos con Grafana
Este repositorio contiene el ejercicio del flow 5 de NodeRed, basandose en el contenido de Internet de las cosas de [Codigo IoT](http://edu.codigoiot.com "Codigo IoT") con almacenamiento en MySQL además de que sustituirán los gráficos que nos brinda Node Red por los gráficos de Grafana.

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
- [Grafana](https://grafana.com/ "Grafana")

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
9. **Instalación de grafana**. 
 - `sudo apt-get install -y adduser libfontconfig1`
 - `wget https://dl.grafana.com/enterprise/release/grafana-enterprise_9.1.3_amd64.deb`
 - `sudo dpkg -i grafana-enterprise_9.1.3_amd64.deb`


### Intrucciones de preparación del entorno
1. Arrancar NodeRed con el comando `node-red`.
2. Importar el flow desde el repositorio.
3. Agregamos un nodo inject y un http request.
4. Actualizar la IP del broker público.
5. Actualizar los nodos, agregando uno nuevo para SQL.
6. Agregamos nodos template para inscrustar nuestras gráficas nuevas (debemos desactivar las que teníamos antes con NodeRed)
7. Hacer clic en el boton Deploy.

### Notas MySQL
- Para la instalación de MySQL server, necesitamos utilizar el comando en la terminal `sudo apt install mysql-server`.
- Iniciamos la terminal de MySQL con `sudo mysql`
- Agregaremos un usuario para evitar trabajar como root. Para ello utilizamos la siguiente consulta `CREATE USER 'usuario'@'localhost' IDENTIFIED BY 'contraseña';`
- Creamos una base de datos, así como una tabla para almacenar los datos que recibiremos
 - Crear base de datos: `CREATE DATABASE NombreBD;`(la usamos con `USE Nombre`).
 - Crear tabla: `CREATE TABLE clima(id INT (6) UNSIGNED AUTO_INCREMENT PRIMARY KEY, fecha TIMESTAMP DEFAULT CURRENT_TIMESTAMP, nombre CHAR(248) NOT NULL, temperatura FLOAT(4,2), humedad INT (3));`
- En cualquier caso si llegamos a olvidar la estructura de la base de datos usamos la consulta : `DESCRIBE NombreBD`;
### Notas Grafana
- Una vez instalado Grafana necesitamos encender el servidor con el siguiente comando: `sudo /bin/systemctl start grafana-server`. Si quisieramos que al encender nuestro equipo, grafana inicie automáticamente, usamos el comando: `sudo /bin/systemctl enable grafana-server`.
- Para que grafa permita la visualización de sus gráficas en los templates, es necesario modificar el archivo de configuración del mismo. Nos vamos al archivo en la ruta /etc/grafana/grafana.ini. Buscamos en la sección *Security* buscamos la instrucción `;allow_embedding = false`, la cual cambiaremos de false a true y quitaremos el ;. Por último reiniciamos grafana con `sudo /bin/systemctl start grafana-server`.

### Intrucciones de operación NodeRed y MySQL
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

### Intrucciones actualización de gráficos

- Nos dirigimos al navegador y escribimos localhost:3000 para iniciar sesión.
- Por lo general grafana nos da el usuario y contraseña a lo cual la dejamos por default o podremos cambiar pues que nos pedirá que cambiemos la contraseña predeterminada.
- Agremos una nueva fuente de información haciendo click en el boton Data Source.
- En los campos agregamos
    - Host: localhost:3306
    - Database: nombre de la base de datos
    - User & Password: Nombre de usuario y contraseña.
    - Session TimeZone: -05:00
- Una vez iniciada sesión damos click en el bodon Add panel para agregar un nuevo panel.
- Cambiamos el diseño del gráfico para que se adapte a nuestras necesidades recordando que cada gráfico tiene requisitos.
- Para que la gráfica funcione, en la parte de abajo veremos que tenemos la opción MySQL (que agregamops en el Data Source), lo seleccionamos si no lo está y modificamos la instrucción que tenemos por default para hacer una nueva consulta. Por ejemplo si necesitamos saber la temperatura de los integrantes activos en el broker, modificamos el Query a `FROM Clima Timer Colum fecha Metric Column nombre SELECT column: temperatura`.
- Modificamoas el nodo JSON para que nos muestre el nombre de todos los integrantes y no solo el nuestro: `msg.topic = "INSERT INTO clima ('nombre','temperatura','humedad') VALUES ('" + msg.payload.id + "'," + msg.payload.temp + "," + msg.payload.hum + ");";` (cambiamos el valor nombre por la variable global id). No olvidemos guardar el panel.
- En el título de la gráfica abrimos la lista desplegable y hacemos click en share, vamos a la sección Embed y copiamos el enlace que nos da.
- Siguiendo la recomendación de las instrucciones de preparación de entorno, desabilitamos los nodos chart y gauge de NodeRed para reemplazarlos por los de grafana.
- Agregamos nodos template y en su contenido pegamos el enlace que nos dio grafana en la sección embed, redimensionamos la altura y anchura de los gráfcos para que se adapten a los layouts que teniamos las gráficas de NodeRed. 
Estos pasos lo hacemos reemplazando cada una de las gráficas de NodeRed.

### Resultados y evidencias grafana
**Graficas hechas en Grafana**
![](https://github.com/HilarioBarcenas/flow-5-Clima-y-Bases-de-datos/blob/main/Capturas/Grafana.png?raw=true)

**Gráficas reemplazados por Grafana**
![](https://github.com/HilarioBarcenas/flow-5-Clima-y-Bases-de-datos/blob/main/Capturas/ActualizacionNode-Grafana.png?raw=true)
![](https://github.com/HilarioBarcenas/flow-5-Clima-y-Bases-de-datos/blob/main/Capturas/Actualizaci%C3%B3nNode-Grafana2.png?raw=true)
### Evidencias

[YouTube](https://youtu.be/yonDjBt2Nt0)

[YouTube](https://youtu.be/x-0UTFE3Gow)
### Créditos
**Desarrollado por** `Hilario Barcenas`

[Github](https://github.com/HilarioBarcenas "Github")
