# Contexto
## Mqtt_utils
Servirá para controlar el comportamiento de un cliente del servidor MQTT.
Métodos o funciones:
1. _init_(), nos servirá para generar un objeto del tipo MQTTClient cuales atributos serán: host, port, connected,pending_suscriptions, client.
2. start(), utilizamos esta función para conectarnos sin bloqueo.
3. shutdown(), utilizamos esta función para deterner el subproceso en segundo plano que generamos en start().
4. on_connect(), se utiliza cuando se nos responde a nuestra petición de conexión.
5. on_disconnet(), utilizada cuando se pierde la conexión con el servidor.
6. on_subscribe(), utilizada cuando el servidor MQTT responde a una petición de suscripción.
7. susbribe(), la utilizamos para suscribirnos al topic que le pasamos por parámetro.
8. publish_sync(), utilizamos esta función para enviar un mensaje al servidor y éste envíe ese mensaje a todos los clientes que estén subscritos a ese topic.
9. publish(), utilizamos esta función para ejecutar la función publish_sync en otro hilo de ejecución.
## Participant
Dentro de la misma definiremos otra clase llamada status la cual será un atributo de la clase participante que definirá el estado del participante, el estado podrá tomar los valores joined, ready o active.
El participante también tendrá asociado un id y un nombre. Para el manejo de los ids tendremos una variable que guardará el último id asociado a un participante, la manera de asignar el id a un nuevo participante será sumar uno al último id.
Definimos una señal llamada on_status_changed asociada al atributo status de Participant.
Dispondremos de varios métodos:
1. _init_(), nos servirá para generar un objeto participant. Este método asignará el id al usuario, guardará su username y marcará su status a joined.
2. status() (etiqueta property), retorna el valor del estado del usuario.
3. status() (etiqueta status.setter), comprobará que el estado entrante no sea el que ya tiene asignado, en ese caso cambia el estado al pasado por parámetro y emitimos una señal de que el status ha cambiado.
4. as_dict(),  retorna el valor de los atributos del participant (id, username, status).
## Question
Los atributos de un objeto de clase Question son: id, prompt, answers, img_path, img_is_local. La clase Question tiene una variable que guardará el último id asociado a una pregunta.
Métodos o funciones:
1. _init_(), nos ayudará a generar un objeto Question con los valores que le pasamos por parámetro y un id correcto.
2. as_dict(), retornará el valor de los atributos id, prompt y answers.
3. from_folder(), nos servirá para generar un objeto Question a partir de la dirección de una carpeta en la que deberemos tener la información referente a una pregunta. Lo primero que haremos es comprobar que exista tal archivo y abrir un buffer de lectura a éste. Almacenaremos los datos en una variable suponiendo que la información que contiene el buffer se encuentra en formato JSON. Tras esto pasamos a buscar dentro de esta información la dirección de la imagen de la pregunta. Por último retornamos un objeto Question con los valores cargados del archivo y con su id asociado.
## Session
* Objeto SessionCommunicator
Contaremos con un atributo de SessionCommunicator llamado status que podrá tomar los valores disconnected, connected o subscribed.
* Objeto Session
Dispondremos de una variable que guardará el último id que tiene asociado una sesión.
En esta clase haremos uso de las otras clases de contexto: Question, Participant y mqtt_utils.
Contaremos con una subclase de la sesión llamada status que podrá tomar los valores waiting o active.
Los atributos del objeto Session son id, status (subclase de Session), question (Question), duration, participants (ArrayList de Participant),log_file, timer y communicator (SessionCommunicator).
Definiremos varias señales:
** on_status_changed: asociada al status de Session.
** on_connection_status_changed: asociada al status de SessionCommunicator.
** on_question_notified: asociada al evento setup de la pregunta.
** on_participant_joined: asociada a que un nuevo participante entre a la sesión.
** on_participants_ready_changed: asociada al número de participantes listos.
** on_start: asociada al evento start.
** on_stop: asociada al evento stop.
Métodos o funciones:
1. _init_, cómo siempre este método nos servirá para generar un objeto de tipo Session.
2. _eq_, nos permite saber si dos sesiones corresponden a la misma sesión, se basa en el id.
3. status (etiqueta property), retorna el valor del status de la sesión (Status).
4. status (etiqueta status.setter), permite modificar el valor del atributo status de la sesión.
5. active_question (etiqueta property), retorna el atributo question (Question).
6. active_question (active_question.setter), permite activar las respuestas a la pregunta. Para ello lo primero que hacemos es modificar el status de todos los participantes a joined. Después comprobamos que la question se ha credo correctamente. Por último llamamos al método publish de mqtt_utils.
7. ready_participants_count (etiqueta property) retorna la cantidad de participantes con el status en ready.
8. as_dict (etiqueta property), retorna el id, status, id de question y duration de la sesión.
9. add_participant, permite añadir el participante que nos pasan por parámentro a la lista de participantes.
10. participant_ready_handler, permite modificar el status de Participant a ready.
11. start() 
12. stop()
13. participant_update_handler()
# Servicios
## Api
## Mqtt
# Main