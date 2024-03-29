# Vista cliente (Participante)
## Componente App

Es el componente que react considera principal. Este componente inicializará variables de sesión como el id de la sesión, el id del participante y su nombre. También redireccionará en caso de inicio o cierre de sesión.

## Componente Header

Este componente es la cabecera de nuestra página.
En caso de que el usuario no haya hecho el login se mostrará un menu, el nombre de la plataforma, y un botón con el cartel Join a Session que nos mandará al login.
En caso de que el usuario si haya realizado el login el botón Join a Session desaparece y en su lugar tenemos un botón que incluye el nombre de usuario y un icono que al pulsarlo nos da la opción de salir de la sesión. 


## Componente BoardView

Este componente hace referencia al componente en el que podremos ver la disposición de las respuestas en un polígono, dentro de este polígono tendremos elementos como las respuestas de otros participantes o la respuesta del propio.

Nuestro componente BoardView está compuesto por:
1. Un polígono que dependerá de la pregunta su número de vértices y lados(cada vértice referencia a una respuesta).
2. En los vértices o extremos del polígono irán unos puntos que tendrán una respuesta asignada. Cada punto contará con su posición en los ejes x e y.
3. Un círculo que marcará el punto en el que se encuentra la media de respuestas.
4. Un rectángulo que expresa la respuesta de otro usuario, va unida a una constante llamada denormalizePosition que nos permite obtener la posición en coordenadas x e y. Habrá tantos rectángulos como respuestas.
5. Un conjunto de flechas que mediante la posición de su inicio y final podremos saber la distancia que hay entre el punto en el que el usuario se ha posicionado y las posiciones de las respuestas (Solo visible en el modo debug).
6. Punto o bola que expresa la posición en la que ha elegido quedarse el usuario, pudiendo obtener del elemento las posiciones x e y. Cuando el usuario decide posicionarse, éste debe hacer click en su bola roja y sin soltar el ratón posicionarse, esto hace saltar un evento pointerdown que está relacionado con la constante startDrag que dentro de él contiene un evento pointermove que es el encargado de permitir mover el punto rojo por la pantalla. Este evento hace referencia a una función llamada onUserMagnetMove que estará relacionada con el componente SessionView.

Para el funcionamiento correcto del componente tendremos varias funciones que nos ayudaran:
1. distance(): nos ayudará a saber la distancia entre un elemento y otro. Por ejemplo, entre la posición de un vértice o respuesta de la pregunta y la posición en la que se ha posicionado el usuario.
2. getClosestAnswers(): devolverá las respuestas de la preguntas más cercanas a la posición de la respuesta del usuario.
3. normalizePosition(): permite expresar la respuesta del usuario en el formato que deseamos, un ejemplo de este formato es el siguiente: 
0,0.4170471955983865,0.4689014007591018,0,0,0
En este ejemplo podemos ver que tenemos seis respuestas y que las respuestas más próximas son las que ocupan la posición superior e inferior derecha.
4. denormalizePosition(): permite pasar de una posición normalizada cómo la expresada en la función anterior a una posición expresada en coordenadas cartesianas (x e y).
5. startDrag(): es el método que permite registrar el movimiento de la respuesta del usuario.


## Componente DebugBoardView

Este componente nos servirá para probar el correcto funcionamiento de nuestro anterior componente (BoardView). Nos permite visualizar el formato en el que se recogen los valores de la respuesta del usuario, tanto normalizada como sin normalizar. Para acceder a dicho componente tendremos que añadir "/debug" a la url de nuestro navegador. 

## Componente SessionLogin

Es el componente que permite el login. Puntos a resaltar:

1. Cuando pulsamos el boton login, este está relacionado con el objeto/función joinSession que recoge la información del formulario y envía una petición post a nuestro controlador api. 
2. El controlador nos devuelve un mensaje en formato JSON que con el que podremos sacar su status y actuar en consecuencia, en caso de que sea un error mostramos por pantalla que no se ha podido enviar la solicitud de acceso y en caso que no se haya realizado correctamente el proceso mostraremos un mensaje diciendo que no se ha podido entrar a la sesión. Por otro lado tendremos el caso de que el código que nos devuelve la petición a la API sea el 200, es decir éxito, en este caso llamaremos a la función onJoinSession() pasándole el nombre del usuario, el id de dicho usuario y el id de la sesión.
3. La función onJoinSession() está relacionada con el componente App, dentro de este componente cuando se recibe la llamada se fijan los valores a las variables de sesión.
## Componente QuestionDetails

A este componente le pasamos título de la pregunta y la imagen de la misma, tendremos un useEffect asociada a ésta última que garantizará que el tamaño de la imagen no exceda el tamaño máximo fijado.

## Componente Countdown

Se encargará de la cuenta atrás que empieza al iniciar el periodo de respuesta. Para conseguir la cuenta atrás necesitaremos que el componente reciba la fecha límite para contestar. 
Contaremos con un useEffect de la fecha límite, dentro de éste cada un segundo fijaremos el valor de la cuenta atrás al resultado que nos devuelve la función calculateCountdown. Ésta última función nos devuelve la diferencia de tiempo en minutos y segundos entre la fecha límite y la fecha actual.

El componente mostrará el tiempo restante en el formato '10:00' (10 minutos) o 'No time' en caso de que se haya acabado el tiempo o no se haya fijado todavía fecha límite.

## Componente StatusView

Se trata de un componente con intención informativa para el usuario, una vez realizado el login e introducida la sesión, aparece el componente StatusView que nos muestra la sesión a la que estamos suscritos, el estado de nuestra suscripción (entrando o dentro), un mensaje que nos dice que estamos esperando la pregunta y un botón que nos permite salir de la sesión.

## Componente SessionView

La primera comprobación que realiza este componente es si el status de la sesión es activo, es decir que estemos en el tiempo de contestar una pregunta.
En caso de que no estemos en periodo de contestar mostraremos el componente StatusView al que le pasaremos el id y status de la sesión, el status de la pregunta y el evento onLeaveClick.
En caso de que sí estemos en tiempo de contestar nos mostrará un panel que estará separado en dos:
1. Parte de la pregunta: en la que encontraremos el componente QuestionDetails al que le pasaremos por parámetro el path de la imagen y el título de la pregunta.
2. Parte de la respuesta: en la que se ubicará el componente BoardView al que le pasamos por parámetros las respuestas (en caso de que se haya cargado la pregunta), la posición central en la que se debe ubicar el círculo de la media de las respuestas y un array con las posiciones de las respuestas de otros usuarios. Después una variable asociada al estado de la constante userMagnetPosition en la que se encontrará la posición de la respuesta del usuario, onUserMagnetMove será una función asociada a la función onUserMagnetMove del componente BoardView.

Visto ya lo que contiene nuestro componente SessionView pasemos a ver más a profundidad sus atributos, funciones y eventos.

Primero tenemos una variable llamada sessionRef que usamos para almacenar un valor mutable que no provoca una nueva representación cuando se actualiza y que hace referencia a la sesión del usuario a la que estamos conectados.
Después nos encontramos con constantes las cuales están rastreadas (useState Hook), esto quiere decir que cuentan con un estado y una función de actualización de estado. Cuando algunas de las siguientes constantes actualizan su estado generan una nueva renderización y estarían asociadas a:
1. El status de la sesión (Referencia a la constante de la clase de contexto Sesion.js).
2. El status de la pregunta (Referencia a la constante de la clase de contexto Question.js).
3. La pregunta que ha fijado el administrador de la sesión.
4. La posición de la respuesta del usuario (Referencia al componente BoardView).
5. La posición de las respuestas de otros usuarios (Referencia al componente BoardView).
6. La posición de la media de respuestas (Referencia al componente BoardView).
7. La fecha límite para contestar la pregunta. (Referencia al componenente Countdown).

Tendremos varios useEffects para ejecutar su contenido cuando se modifique el valor de las siguientes variables:

1. Sobre el id de la sesión y el id del participante:
 
* Realizamos una petición a nuestro servidor http para obtener un json con información sobre la sesión, en caso de recibir una respuesta http con status 200, cambiamos el estado de la sesión a waiting y comprobamos si se ha especificado ya alguna pregunta, si se ha especificado pregunta asignamos los nuevos valores a question.

* Seguimos creando una nueva sesión para el usuario para ello lo primero que haremos es crear un nuevo objeto de tipo Session que nos permite obtener un mensaje de control y otro de actualización mediante mediante su constructor. 
Para el mensaje de control actuaremos en función de su tipo:
** Tipo setup: comprueba que el id de la question sea nulo y actualiza el estado de la pregunta a undefined, en caso de que no se nulo ,comprobamos que la colección tampoco sea nula, si todo está bien, actualiza el estado de la pregunta a loading y modifica los valores de question.
** Tipo start: actualiza el estado de la sesión a active y fija la fecha para el componente Countdown
** Tipo started: realiza el mismo código que el tipo start y además recoge las posiciones que recibe, las cuales corresponden a las de los demás usuarios.
** Tipo stop: actualiza el estado de la sesión a waiting, fija la posición del usuario al medio de polígono y vacía el array que contiene las posiciones de los demás participantes

Para el mensaje de actualización actualizamos el estado de las posiciones de las respuestas de los demás usuarios.

2. Sobre la pregunta:

Comprobamos que el status de pregunta sea loading, en caso de que sea así realizamos una petición a nuestro servidor http en la que recibiremos información de la pregunta en caso de obtener un código de respuesta 200.
Si no debemos de ignorar los datos, con esta información actualizamos el estado de la pregunta. Después publicamos un mensaje de control en el que incluimos el tipo que será 'ready', el id del participante y el id de la sessión. Por último retornamos "ignore = true"

3. Sobre la respuesta del usuario y las respuestas de los demás usuarios.
La función de este useEffect será la de garantizar que el punto medio de las respuestas se actualice con el movimiento de las respuestas del usuario y del resto participantes.

Por último comentar las funciones onUserMagnetMove y onLeaveSessionClick.
1. onUserMagnetMove: en caso de que el status de la sesión sea active, actualizamos el estado de la posición de la respuesta del usuario y publicamos un mensaje de actualización en el que incluimos la nueva posición y la fecha en la que se ha realizado dicho movimiento.
2. onLeaveSessionClick: hace una petición a la API para avisar de que el usuario abandona la página. Por último, hace una llamada a onLeave(),que está asociada al componente App. 



## Clase para el contexto JS Question

Nos permite tener un objeto con atributos de tipo Symbol (Variables únicas que tienen una descripción) congeladas, es decir que no se podrán modificar. Los atributos son tres todos de tipo Symbol:
1. Undefined: cuando no tenemos pregunta definida.
2. Loading: se ha de elegido pregunta pero no se han cargado los detalles de la misma.
3. Loaded: se han cargado los detalles de la pregunta y éstos están disponibles.

## Clase para el contexto JS Session

Nos permite tener un objeto con atributos de tipo Symbol. Constará al igual que Question de tres atributos:
1. Joining: se está obteniendo la información y suscribiéndose a los topics de MQTT.
2. Waiting: esperando a que la pregunta sea definida y que se se carguen sus detalles.
3. Active: los detalles están cargados y se está respondiendo a la pregunta.

También tendremos una clase Session que tendrá las siguientes funcionalidades:
1. Costructor: recibe un id de sesión, un id del participante, un parámetro al que haremos referencia en caso de estar ante un mensaje de control y otro en caso de que sea un mensaje de actualización. Primero conectamos a nuestro servidor mqtt (ws) que está en el puerto 9001. 
* Después definimos un evento que se ejecutará cuando el cliente se conecte al servidor. El cual suscribirá al usuario a dos topics, al de control y al de actualización. (Los topics variarán si el usuario que realiza la conexión es un pparticipante o el administrador). Adicionalmente, en caso de que seamos un participante enviaremos un mensaje por el topic de control que contendrá el tipo 'join', el id de participante y el id de la sesión.
* Definimos otro evento en caso de que recibamos o enviemos un mensaje a través de alguno de los dos topics. Dentro de este evento una vez se reciben los datos lo primero que hacemos es tratarlos. Debemos comprobar que éstos lleguen en el formato correcto, en caso contrario la ejecución del evento se cortará en ese punto. Debemos comprobar también que los datos que llegan y se envían son referentes a la sesión en la que hemos realizado el login. En caso contrario no se trata esa información y se para la ejecución del evento. Tras las anteriores comprobaciones, debemos saber de qué topic proviene el mensaje, si estamos ante un mensaje de control haremos referencia al parámetro de control pasándole un Json con los datos recibidos en la conexión. En el caso en el que sea un mensaje de actualización lo primero que haremos será saber que usuario a enviado esa información (comprobamos que los datos vengan con un quinto parámetro). Después referenciaremos al parámetro de actualización pasándole como parametros el id del participante y un json con los datos.
2. Una función llamada publishControl que recibirá por parámetro un objeto de js con la información que se quiere enviar por el topic de control. La función como su propio nombre indica se encargará de publicar en el topic de control un json con la información del objeto que hemos recibido por parámetro (información de la pregunta por ejemplo).
3. Otra función llamada publishUpdate que tendrá una función idéntica a la anterior para el topic de actualización. Típicamente el json que se publica hará referencia a la ubicación de la respuesta del participante.
4. Una última función llamada close que cierra la conexión con el servidor mqtt.

# Vista cliente (Administrador)

Este apartado estará enfocado en la vista de administración, para acceder a ella bastará con añadir a la url '/admin'. Que nos redigirá al componente AdminView.

## Componente AdminView
Se encarga de mostrar el componente AdminLogin o AdminInterface dependiendo si se ha realizado correctamente el login. Para ello utilizaremos una constante status, que tendrá asociada un useEffect.
El componente AdminLogin modificará el status, cuando esto ocurre el código del usesEffect asociado a status realiza lo siguiente:

1. Una petición a la API que devuelve un array con las sesiones activas.
2. Otra petición a la API, esta vez nos devolverá un objeto de tipo set, una lista de keys (nombre de colecciones) y values asociados a una key (preguntas de la colección). Después de recibir las colecciones llamamos nos apoyaremos en dos métodos para ordenar correctamente las colecciones:
* customSortCollections: nos ayudará a ordenar las colecciones.
* customSortQuestions: nos ayudará a ordenar las preguntas de una colección.

## Componente AdminLogin

Cómo su propio nombre indica será el encargado de realizar el login del administrador. Para ello el usuario cuenta con dos cajas de texto, una para el usuario y otra para la contraseña. También contaremos con el botón submit, cuando lo presiona el usuario se realiza una petición de tipo POST que comprueba que las credenciales son correctas, en caso de éxito se llama a la función onJoinSession que está enlazada con el componente AdminView.

## Componente AdminInterface

Es el componente principal para la administración de la sesión, en la interfaz de éste se diferencian tres columnas. 
En la columna izquierda tendremos:
* Select con las sesiones activas.
* Botón para generar una nueva sesión.
* El id asociado a la sesión en la que nos encontramos.
* La duración marcada en la sesión actual (este valor se puede modificar).
* Select con las colecciones de las que disponemos.
* Select con las preguntas de las que disponemos en la colección seleccionada.
* TextArea en la que se mostrarán los participantes de la sesión.

En la columna central:
* Componente QuestionDetails
* Componente Countdown
* Texto que marca la cantidad de logs que se han generado
* Select con los logs generados.
* Botón para cargar los logs.
* Botón para descargar el log seleccionado en el select.
* Botón para descargar el log con fecha más reciente.
* Botón para descargar todos los logs.
* Botón para descargar todas las trayectorias.
* Botón para borrar todas las trayectorias y los logs asociados a estas trayectorias. Pedirá confirmación
* Botón para borrar todos los logs generados. Pedirá doble confirmación.
* Texto que marca el modo en el que está la aplicación. Tenemos dos modos (Normal mode y Trajectories mode), el primero sería el uso normal de la aplicación (modo que se debe de utilizar en sesión con expertos), el segundo sería para conseguir trayectorias (si tenemos este modo seleccionado se generan las trayectorias).
* Botón check que permite variar el modo en el que se encuentra la aplicación. Seleccionado -> Trajectories mode y No seleccionado -> Normal mode.
En la columna derecha:
* Componente BoardView.

Dentro de la lógica que necesitamos para administrar correctamente la sesión necesitaremos lo siguiente:

1. useEffects:
* sessions: se ejecutará cuando se cargue la aplicación y cuando generemos una nueva sesión. En caso de tener alguna sesión, fija la colección en la primera del listado de colecciones y fija también la primera pregunta de la lista de ésta.

* collections: primero comprobamos que la lista de colecciones no sea nula o esté vacía, sólo entonces haremos lo siguiente:
1. Fijar la colección y la primera pregunta de ésta a la sesión actual.
2. Hacemos una llamada a la función fetchQuestion(), con la respuesta de ésta llamada podremos fijar los datos de la pregunta activa. También enviaremos un mensaje de control de tipo setup con el id de la colección y el id de la pregunta. 

* selectedSession.id, getParticipantsBySession: primero comprobamos que el id de la sesión actual sea distinto a 0. Después haremos una llamada a getParticipantsBySession(), tras esto fijaremos la sesión actual con un nuevo Objeto Session de la carpeta context. Dentro de la declaración podremos fijar los comportamientos en caso de recibir un mensaje de control o de actualización.
Casos para un mensaje de control:

1. Mensaje de tipo join: se actualiza la lista de participantes.
2. Mensaje de tipo ready: en caso de que el status de la sesión sea activa, se le pasa un mensaje de tipo started con la duración y las posiciones de los demás usuarios. Después se actualiza la lista de participantes.
3. Mensaje de tipo leave: se actualiza la lista de participantes.
Si el mensaje es de actualización lo que haremos será modificar los valores asociados a la posición del participante que envía el mensaje de actualización.

* peerMagnetPositions: este useEffect nos ayuda a mantener la posición central actualizada en función a las posiciones de respuesta de los participantes.

* shouldPublishCentralPosition, currentSession: su función será esperar a que el tiempo de contestar finalice para enviar la posición media de las respuestas de los usuarios. También envía el mensaje de stop, junto con el modo que está seleccionado.

2. Funciones:
* La función getParticipantsBySession() que utiliza useCallback, ésta función nos sirve para realizar una petición a la api que nos devuelve los participantes con sus estados de una sesión concreta.

* fetchQuestion(collectionId, questionId): devuelve la respuesta a la llamada a la api que nos devuelve los datos de una pregunta de una colección en concreto. Con los datos que nos devuelve la llamada fijamos la pregunta.

* handlequestionChange(), asociado al evento onChange del select de las preguntas. Primero vacía la lista de las respuestas de la anterior pregunta, después fija el id de la pregunta a la sesión actual. Por último realizamos una llamada a fetchquestion().

* handleCollectionChange(), asociado al evento onChange del select de las colecciones. Primero vacía la lista de las respuestas de la anterior pregunta, después fija el id de la colección al valor del elemento que ha generado el evento. También fija la pregunta a la primera pregunta de la colección seleccionada. Por último realizamos una llamada a fetchquestion(), con la respuesta de la misma fijaremos al pregunta activa y enviaremos un mensaje de control de tipo setup con el id de la colección y el id de la pregunta.

* handleSessionChange(), asociado al evento onChange del select de las sesiones. Fija la sesión actual a la sesión asociada al valor del evento. 

* startSession(), asociado al evento onClick del botón de start/stop. Si el periodo de contestar no ha comenzado, se vacía la lista de las posiciones de las respuestas de los participantes, se pone la posición media de las respuestas en el medio, enviamos un mensaje de control de tipo start con la duración. También se actualiza el estado de la sesión a activa y se fija la fecha para la cuenta atrás. Por último, hacemos una llamada a waitOrCloseSession().

* waitOrCloseSession(), se actuará en función de si se está esperando a la cuenta atrás.
En el caso de que sea la primera llamada a la función:

1. Fijamos que estamos esperando que acabe la cuenta atrás.

2. Fijamos un tiempo determinado antes de ejecutar los siguientes puntos.

3. Permitimos que se pueda publicar el punto central.

4. Marcamos que no estamos esperando a la cuenta atrás.

5. Cambiamos el estado de la sesión a waiting.

En el caso de que no sea la primera llamada a la función, nos saltamos los dos primeros puntos antes mencionados.

* createSession(), asociado al evento onClick del botón de New session. Hacemos una petición a la API para generar una nueva sesión. Si respuesta es exitosa llamaremos a la función onSessionCreated() que está relacionada con el componente AdminView.

* compareDates(), tal como indica su nombre compara fechas.

* fetchlogs(), realiza una petición a la API que devolverá el nombre de los logs que tengamos en el backend.

* handleLogSelect(), asociado al evento onChange del select de logs.

* downloadFolder(), asociado al evento onClick del botón de Download selected log. Realiza una petición a la API para instalar un log en concreto y descarga el .zip asociado a dicho log.

* downloadLastFolder(), asociado al evento onClick del botón de Download last log. Realiza una petición a la API para instalar el último log de la lista de logs y descarga el .zip asociado a dicho log.

* downloadAllLogs(), asociado al evento onClick del botón de Download all logs. Realiza una petición a la API para instalar todos los logs y descarga el .zip que contiene todos los logs.

* downloadAllTrajectories(), asociado al evento onClick del botón Download all trajectories. Realiza una petición a la API que nos permite descargar todas las trayectorias que hayamos generado.

* deleteTrajectories(), asociado al evento onClick del botón Delete all trajectories. Realiza una petición a la API que borra todas las trayectorias generadas y sus logs asociados. Solicita una confirmación por parte del usuario. En caso de que todo haya ido bien se mostrará un mensaje de confirmación.

* deleteLogs(), asociado al evento onClick del botón Delete all logs. Realiza una petición a la API que borra todos los logs generados. Solicita doble confirmación. Si todo va bien debería mostrar un mensaje de confirmación.

* handleCheckboxChange(), asociado al evento handleCheckboxChange del input Save Trajectories. La función permite cambiar el modo en el que está la aplicación (Normal o Trajectories).