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
### Backend
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

### Frontend

Primero debemos instalar node y npm:

* sudo apt update && sudo apt upgrade
* curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
* sudo apt-get install nodejs
* node -v
* npm -v

Después clonamos el proyecto e instalamos las dependencias:

* sudo git clone https://github.com/GuillermoSantosMolero/Hans-Platform-FrontEnd.git
* cd Hans-Platform-FrontEnd/
* npm install 

Con esto deberíamos de poder lanzar el frontend.

* sudo npm start
## Modificaciones para la implementación de colecciones en el bucket

Para que la instancia pueda acceder a las colecciones debemos asignarle un rol IAM a la instancia, esto se puede hacer seleccionando la instancia y en el apartado de seguridad podemos darle un rol IAM a la instancia. El rol que le hemos dado se denomina Access_S3_with_EC2.

## Lanzamiento automático

Para el lanzamiento automático utilizaremos un servicio en systemd, el cual se ejecuta al iniciarse la instancia.
Dicho servicio ejecuta el script start_app.sh. Este spript lo primero que hace es llamar a otro script llamado collectionsSync.sh que sincroniza el contenido de nuestro repositorio de Github con el que vayamos a correr en la máquina.

El script de collectionsSync tiene este aspecto:
```sh
#!/bin/bash

git config --add safe.directory /home/ubuntu/Hans-Platform-BackEnd
# Cambia al directorio del repositorio
cd "/home/ubuntu/Copia-Frontend/Hans-Platform-FrontEnd" || exit

git checkout Dev
# Realiza git pull para traer los cambios
git pull

cd

sudo rsync -av --delete /home/ubuntu/Copia-Frontend/Hans-Platform-FrontEnd/src/* /home/ubuntu/Hans-Platform-FrontEnd/src
sudo rsync -av --delete /home/ubuntu/Copia-Frontend/Hans-Platform-FrontEnd/package* /home/ubuntu/Hans-Platform-FrontEnd

cd "/home/ubuntu/Hans-Platform-BackEnd"
git checkout Dev

git pull

```

El script que lanza los servidores de backend y frontend (start_app.sh) tiene este aspecto:
```sh
#!/bin/bash
/home/ubuntu/collectionsSync.sh
cd /home/ubuntu/Hans-Platform-FrontEnd
sudo npm start &
sleep 5
cd /home/ubuntu/Hans-Platform-BackEnd/server
source backenv/bin/activate
python -m src.main

```
Para que los cambios que hayamos hecho en nuestro repositorio surjan efecto tendremos dos opciones: 
1. Detener e iniciar la instancia.
2. Parar el servicio (sudo systemctl stop lanzamiento) e iniciar el servicio (sudo systemctl start lanzamiento).