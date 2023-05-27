# Despliegue de backend

Tanto el servidor API (Flask), como el servidor WebSocket (MQTT) se desplegarán en una instancia EC2 de AWS.

## Crear la instancia:
* Lanzar instancia en región deseada.
* Seleccionar Ubuntu, 64 bits (x86)
* Tipo de instancia t2.micro -> Coste de 0.0126/hora supuestamente.
* Crear par de llaves, rsa y descargar en .pem.
* En configuración de red, añadir los siguientes tipos:
** tipo: HTTP, origen: cualquier lugar
** tipo: TCP, origen: cualquier lugar, intervalo de puertos: 1883
** tipo: TCP, origen: cualquier lugar, intervalo de puertos: 9001
** tipo: TCP, origen: cualquier lugar, intervalo de puertos: 8080
* En configuración de almacenamiento, marcar 20 GiB en gp2

Tras estos pasos ya podemos lanzar la instancia.

## Configuraciones de la instancia:
* Una vez lanzada la instancia nos vamos al apartado de direcciones IP elásticas
* Damos a asignar dirección IP elástica y asignar.
* Con la IP creada la seleccionamos, pinchamos en acciones y le damos a asociar la dirección elástica. El tipo de recurso en instancia, la instancia la que hemos creado anteriormente y dirección privada la que nos ofrece amazon. Seleccionamos la opción de permitir que se vuelva a asociar esta dirección IP elástica.

## Configurar servidor remoto con PuTTY:

* Después de instalar PuTTY, abrir el Key Generator.
* Importar la clave que descargamos al crear la instancia (desde conversions).
* Damos en save private key.
* Abrimos configurador de PuTTY.
* Copiamos la dirección elástica de nuestra instancia.
* Vamos al apartado de ssh, auth y credentials.
* Añadimos nuestra clave descargada en el apartado de private key.
* Vamos a data.
* Ponemos el nombre del usuario, como estamos trabajando con Ubuntu el nombre será ubuntu o root (recomiendo ubuntu).
* Vamos a session.
* Añadimos un nombre descriptivo a esta configuración, por ejemplo AWS Hans Backend.
* Damos a save y load

## Configuración de servidores en ec2
Después de seguir los pasos del apartado anterior estaremos conectados a nuestra instancia EC2. Pasamos ahora a configurar nuestro servidor API y WebSocket:
* sudo apt-get update
* sudo apt-get mosquitto
* cd /etc/mosquitto/conf.d
* sudo touch mosquitto.conf
* sudo nano mosquitto.conf 
Añadir al archivo mosquitto.conf la configuración necesaria del servidor de WebSocket, ahora mismo a día 27/05/2023 la configuración es la siguiente:

listener 9002 
protocol mqtt
listener 9001
protocol websockets
allow_anonymous true

* cd
* git clone https://github.com/GuillermoSantosMolero/Hans-Platform-BackEnd 
(Después añadir nuestro usuario de github y cuando nos pide la contraseña debemos de insertar el token classic, si insertamos la contraseña de la cuenta nos dará error).
* cd Hans..../server
* sudo apt-get install python3-virtualenv
* virtualenv backenv
* source backenv/bin/activate
* python3 --version
* python -m pip install --upgrade pip
* pip install -r requirements.txt
* python3 -m src.main

Tras esto hecho deberíamos de actualizar el proxy del documento package.json de nuestro frontend y actualizar la ip.
También tendremos que actualizar la ip en la clase session.js del front cuando se hace la conexión al WebSocket.