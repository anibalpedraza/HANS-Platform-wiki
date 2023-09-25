# Contexto
## init (AppContext)

Crea un objeto Namespace en el que marca que los puertos para los servidores son: 9001 para MQTT y 5000 para API. Tendremos otros atributos como mqtt_broker, api_service, sessions (Array de objetos Session) y collections (data set con las colecciones y las preguntas). Éste último atributo recibirá nuevos objetos gracias a la función reload_collections. Ésta función se conecta al bucket s3 en el que tenemos las colecciones (hans-platform-collections) y sacamos un listado con los objetos que encontramos dentro. Después filtramos la respuesta para obtener las colecciones y sus preguntas correspondientes.

## Mqtt_utils (MQTTClient)
Hereda de ABC. 
Servirá para controlar el comportamiento de un cliente del servidor MQTT.
Métodos o funciones:
1. _init_()(constructor), nos servirá para generar un objeto del tipo MQTTClient cuales atributos serán: host, port, connected,pending_suscriptions, client.
2. start(), utilizamos esta función para conectarnos sin bloqueo.
3. shutdown(), utilizamos esta función para detener el subproceso en segundo plano que generamos en start().
4. on_connect(), se utiliza cuando se nos responde a nuestra petición de conexión.
5. on_disconnet(), utilizada cuando se pierde la conexión con el servidor.
6. on_subscribe(), utilizada cuando el servidor MQTT responde a una petición de suscripción.
7. susbribe(), la utilizamos para suscribirnos al topic que le pasamos por parámetro.
8. publish_sync(), utilizamos esta función para enviar un mensaje al servidor y éste envíe ese mensaje a todos los clientes que estén subscritos a ese topic.
9. publish(), utilizamos esta función para ejecutar la función publish_sync en otro hilo de ejecución.
## Participant (Participant)
Dentro de la misma definiremos otra clase llamada status la cual será un atributo de la clase participante que definirá el estado del participante, el estado podrá tomar los valores joined, ready, active u offline.
El participante también tendrá asociado un id y un nombre. Para el manejo de los ids tendremos una variable que guardará el último id asociado a un participante, la manera de asignar el id a un nuevo participante será sumar uno al último id.

Dispondremos de varios métodos:
1. _init_()(constructor), nos servirá para generar un objeto participant. Este método asignará el id al usuario, guardará su username y marcará su status a joined.
2. status() (etiqueta property), retorna el valor del estado del usuario.
3. status() (etiqueta status.setter), comprobará que el estado entrante no sea el que ya tiene asignado, en ese caso cambia el estado al pasado por parámetro y emitimos una señal de que el status ha cambiado.
4. as_dict(),  retorna el valor de los atributos del participant (id, username, status).
## Question (Question - esta clase ya no existe en la versión actual)
Los atributos de un objeto de clase Question son: id, prompt, answers, img_path, img_is_local. La clase Question tiene una variable que guardará el último id asociado a una pregunta.
Métodos o funciones:
1. _init_()(constructor), nos ayudará a generar un objeto Question con los valores que le pasamos por parámetro y un id correcto.
2. as_dict(), retornará el valor de los atributos id, prompt y answers.
3. from_folder(), nos servirá para generar un objeto Question a partir de la dirección de una carpeta en la que deberemos tener la información referente a una pregunta. Lo primero que haremos es comprobar que exista tal archivo y abrir un buffer de lectura a éste. Almacenaremos los datos en una variable suponiendo que la información que contiene el buffer se encuentra en formato JSON. Tras esto pasamos a buscar dentro de esta información la dirección de la imagen de la pregunta. Por último retornamos un objeto Question con los valores cargados del archivo y con su id asociado.
## Session
Se apoya de MQTTClient y Participant.
### Objeto SessionCommunicator
Hereda de MQTTClient.
Contaremos con un atributo de SessionCommunicator llamado status que podrá tomar los valores disconnected, connected o subscribed. Tendremos varios métodos no definidos que podremos definir en otras clases (desde on_status_changed hasta on_participant_update).
También contaremos con los siguientes métodos:
1. _init()_(constructor), primero modificará el status de SessionCommunicator a disconnected, después haremos referencia al método __init__ de MqttUtils y después utilizamos la función message_callback_add que permite estar suscrito a un topic y marcar como manejador de los mensajes que recibamos de ese topic a una función de nuestra clase SessionCommunicator. Haremos esto último para el topic de control y para el de updates.
2. status (etiqueta property), retorna el valor del status de SessionCommunicator.
3. status (etiqueta status.setter), permite modificar el valor del atributo status.
4. connection_handler(), comprueba el parámetro de entrada connected, en caso de que éste sea falso, modifica el status a disconnected. En el caso contrario modifica el status a connected. Tras ello intenta suscribirse a la sesión con el id de ésta, si ésta se realiza correctamente cambia el estado de SessionCommunicator a suscribed.
5. control_message_handler(), primero recoge el id del cliente, la carga del mensaje y el tipo de mensaje. Si el mensaje es de tipo ready y el participante tiene su status en ready guardará el id del cliente en on_participant_ready.
6. control_admin_message_handler(), primero recoge la carga del mensaje y el tipo de mensaje. Si el mensaje es de tipo setup hacemos una llamada a on_setup_question(colección, pregunta). Si es de tipo start llamamos on_session_start(duración). Por último, si es de tipo stop hacemos llamada a on_session_stop().
7. updates_message_handler(), recoge también el id del client y la carga del mensaje. Hace una llamada a on_participant_update (id de cliente y data (del payload)).
 
### Objeto Session
En esta clase haremos uso de las otras clases de contexto: Participant y mqtt_utils.
Dispondremos de una variable que guardará el último id que tiene asociado una sesión.
Contaremos con una subclase de la sesión llamada status que podrá tomar los valores waiting o active.
1. _init_()(constructor), cómo siempre este método nos servirá para generar un objeto de tipo Session.
Los atributos del objeto Session son id, status (subclase de Session), question, collection, duration, participants (ArrayList de Participant),log_file, resume_file, last_session_time, target_date, answers y communicator (SessionCommunicator).
Asociamos funciones no definidas de communicator a funciones definidas en Session:
* on_participant_ready().
* on_participant_update().
* on_session_start().
* on_setup_question().
* on_session_stop().

Tras esto llamamos a start() de communicator.

2. _eq_(), nos permite saber si dos sesiones corresponden a la misma sesión, se basa en el id.
3. status (etiqueta property), retorna el valor del status de la sesión (Status).
4. status (etiqueta status.setter), permite modificar el valor del atributo status de la sesión.
5. ready_participants_count (etiqueta property) retorna la cantidad de participantes con el status en ready.
6. offline_participants_count(etiqueta property) retorna la cantidad de participantes con el status en offline.
7. as_dict (etiqueta property), retorna el id, status, id de pregunta, id de la colección y duración de la sesión.
8. add_participant(), permite añadir el participante que nos pasan por parámentro a la lista de participantes.
9. remove_participant(), permite poner a offline a un participante que nos pasan por parámentro en la lista de participantes.
10. participant_ready_handler(), espera el mensaje de tipo ready para modificar el valor status de Participant a ready en el participante que envía el mensaje.
11. active_question(), permite fijar la colección y la pregunta con los valores que nos pasan por parámetro.
12. session_start_handler(). Dentro de la función lo primero que comprobaremos será que el status de la sesión sea WAITING, tras ello fijaremos los valores de la duración con la entrada por parámetro y la fecha de la sesión con la fecha actual. Después, crearemos dentro de la carpeta session_log una subcarpeta cuya carpeta se llamará por la fecha antes cogida. En esta subcarpeta se guardará primero un archivo tipo JSON con la mayor parte de información de la sesión (fecha en la que se ha generado en archivo, id de la sesión, id de la colección, id de la pregunta, duración de la pregunta y la lista de participantes, ésta lista no se guardará en éste método). Para finalizar abriremos el buffer a dos archivos de tipo csv (el que guardará todas las respuestas que hagan durante el tiempo de contestar y otro con la última posición de cada participante) y cambiaremos el status de la sesión a active.
13. session_stop_handler(). Definimos una función llamada generate_zip() que como su propio nombre indica nos permite generar un .zip de la carpeta con el nombre de la fecha en la que empezó la sesión. Tras definir ésta función, comprobamos que el estado de la sesión es ACTIVE. Una vez pasamos la condición:
* Abrimos el json que se genera en session_start_handler() y guardamos la lista con la información de los participantes.
* Cerramos el csv llamado log.
* Recorremos la variable en la que guardamos la última posición enviada por cada participante y las escribimos en el csv llamado resume (también se recoge la posición media de las respuestas, enviada por el administrador).
* Hacemos llamada a generate_zip()
* Actualizamos el estado de la sesión a WAITING.
14. participant_update_handler(), recibe por parámetro el propio objeto Session, el id de participante y data (de la cual podemos extraer la posición de la respuesta del participante y la fecha en la que se envió). Después escribiremos el archivo csv el id de participante, la marca de tiempo y la posición de la respuesta del participante en este orden. En total tendremos 8 campos separados por ',' (el formato de la posición incluye 6 parámetros, se puede consultar el formato en la url "localhost:3000/debug" el ejemplo solo tiene 5 parámetros).
# Servicios
## init ()
Nos apoyaremos en la clase de contexto init (AppContext), BrokerWrapper y ServerAPI.
Define dos funciones:
1. start_services(): comprueba que tanto el broker de mqtt como el servidor API se pueda invocar, en cuyo caso inicializamos el atributo mqtt_broker de AppContext con un objeto broker mqtt. Después inicicaliza el broker. Luego hacemos lo mismo para el servidor API.
2. stop_services(): en caso de que los servicios estén en funcionamiento los para. En el caso del broker mqtt llamará a su método stop, el servidor API llamará a su método shutdown.

## Api (ServerAPI)
Se apoya en AppContext, Participant, Session.
Funciones:
1. _init_()(constructor), primero inicializamos un hilo, un QObject y un Flask. Definiremos URLs, según las cuales Flask activará la función asociada a esa ruta cuando se recoja una petición. También deberemos de fijar el tipo de petición (GET, POST, PUT, etc).
* Ruta("/api/session/<int:session_id>", tipo GET): llama a la función api_session_handle_get(), la cual intentará obtener la sesión asociada al id que entra por parámetro. Devuelve un JSON con la información de la sesión o un error 404.
* Ruta("/api/session", tipo POST): llama a la función api_get_all_sessions() que devolverá la información de todas las sesiones activas en caso de que sea el admin el que lo solicita.
* Ruta("/api/session", tipo POST): asociada a la función api_create_session, genera una nueva sesión y devuelve la información de la misma.
* Ruta("/api/session/<int:session_id>", tipo GET): asociada a la función api_edit_session, obtiene la sesión asociada al id pasado por parámetro, en caso de no existir esa session la función devuelve un error 404. Cuando los datos recibidos en la petición en formato JSON no se corresponden con la sesión devolvemos un error 400. Si los datos si que corresponden, actualizaremos el status de la session, el id de la pregunta y su duración (realizando distintas comprobaciones que pueden hacer que la función devuelva un error 400 o 404). Por último la función devuelve la información de la sesión ya actualizada en formato JSON.
* Ruta("/api/session/<int:session_id>/participants", tipo GET): asociada a la función api_session_get_all_participants, devuelve información en formato JSON de los participantes de la sesión asociada al id que recibimos por parámetro solo si el que lo solicita es el admin.
* Ruta("/api/session/<int:session_id>/participants", tipo POST): asociada a la función api_session_add_participant, primero comprueba que dentro de los datos de la petición se encuentre información referente al user, tras ello comprueba que exista una sesión asociada al id recibido por parámetro. La última comprobación es que no exista un participante dentro de la sesión con el mismo nombre que el username que se recoge de los datos de la petición y si está, que su status sea offline. Por último declaramos un objeto de tipo Participant, lo agregamos a la lista de participantes de la sesión y devolvemos la información del objeto Participant en formato JSON.
* Ruta("/api/session/<int:session_id>/participants/<int:participant_id>", tipo POST): asociada a la función api_session_remove_participant, sirve para poner a offline a un participante de una sesión en concreto.
* Ruta("/api/collection"): asociada a la función api_get_all_collections, devuelve una lista de todas las colecciones.
* Ruta("api/<string:collection>/question"): asociada a la función api_get_all_questions, devuelve una lista de todas las preguntas de una colección.
* Ruta("/api/question/<string:collection>/<string:question_id>"): asociada a la función api_question_handle, primero comprueba que exista una colección asociada al id recibido por parámetro y también que exista una pregunta asociada al id recibido por parámetro. Tras estas comprobaciones buscamos el archivo info.json de la pregunta en el bucket que contiene la información de la pregunta.
* Ruta("/api/question/<string:collection>/<string:question_id>/image"): asociada a la función api_question_image_handle, primero comprueba que exista una colección asociada al id recibido por parámetro y también que exista una pregunta asociada al id recibido por parámetro. Devuelve la imagen asociada a la pregunta que está en el bucket.
* Ruta('/', defaults={'path': ''}) y ruta("/<path:path>"): asociada a la función client_handler, redirecciona a las distintas páginas de nuestra app.
* Ruta("/api/admin/login"): asociada a la función api_admin_login, nos permite hacer el login del admin
* Ruta("/api/createSession"): asociada a la función api_create_session, nos permite crear una nueva sesión si la solicitud es del admin.
* Ruta("/api/downloadLog/<path:zip_filename>"): asociada a la función download_log, nos permite descargar el log que entra por parámetro si la solicitud es del admin.
* Ruta("/api/downloadAllLogs"): asociada a la función download_all_logs, nos permite descargar todos los logs si la solicitud es del admin (descarga carpeta zips). Debajo de download_all_logs tenemos generate_all_logs_zip(), función que genera el zip de todos los logs.
* Ruta("/api/listLogs"): asociada a la función list_logs, devuelve una lista con todos los logs.

Por último en el constructor levantamos el servidor, declaramos un atributo llamado ctx que guarda el contexto de la app del cual podremos realizar un push.
2. run(), emite una señal a on_start y ordenará al servidor manejar todas las solicitudes que lleguen hasta que se haga un shutdown().
3. shutdown(), hace una llamada shutdown() para que el servidor deje de manejar peticiones.
## Mqtt (BrokerWrapper)
Tendremos las siguientes funciones dentro de la misma:
1. _init_ (constructor), declara e inicializa los atributos port, thread, process, on_start y on_stop. En el caso del atributo port este se inicializa a 1883.
2. isRunning(): devuelve el objeto process.
3. _monitor(): recorre el atributo readline de la variable stream que se le pasa por parámetro e imprime por pantalla '[mosquitto]' + la información de la linea 
a la que esté apuntando el iterador. Cuando acaba de iterar imprime '[mosquito]' + y el valor del atributo name de stream. Comprueba que pueda invocar al atributo on_stop y si puede la llama. 
4. start(): Inicializa una variable en la que guardaremos la dirección de la configuración de mosquitto. Crea archivo con la dirección antes especificada y escribe la configuración de mosquitto en él. Guarda en el atributo process un subproceso. Tras la creación de éste subproceso ordena que el subproceso se ejecute en un nuevo hilo para los mensajes de salida y en otro para los mensajes de error. Si el atributo on_start es invocable lo invoca.
5. stop(): Comprueba que el proceso haya terminado y espera a que los hilos acaben para mandar el código de respuesta del atributo process.
# Main 
Se apoya en AppContext.
Está función nos permite lanzar los servidores, antes de lanzarlos llamamos a la función reload_collections() de AppContext.