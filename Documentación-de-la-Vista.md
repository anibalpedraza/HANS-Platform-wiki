# Componente App

Es el componente de la aplicación, es el que react considera principal. Este componente contendrá información como el id de la sesión, el id y el nombre del usuario. También redireccionará en caso de inicio o cierre de sesión.

# Componente Header

Este componente es la cabecera de nuestra página.
En caso de que el usuario no haya hecho el login se mostrará un menu, el nombre de la plataforma, y un botón con el cartel Join a Session que nos mandará al login.
En caso de que el usuario si haya realizado el login el botón Join a Session desaparece y en su lugar tenemos un botón que incluye el nombre de usuario y un icono que al pulsarlo nos da la opción de salir de la sesión. 


# Componente BoardView

Nuestro componente BoardView está compuesto por:
1. Un polígono que dependerá de la pregunta su número de vértices y lados(cada vértice referencia a una respuesta).
2. En los vértices o extremos del polígono irán unos puntos que tendrán una respuesta asignada. Cada punto contará con su posición en los ejes x e y.
3. Un círculo que marcará el punto en el que se encuentra la media de respuestas.
4. Un rectángulo que expresa la respuesta de otro usuario, va unida a una constante llamada denormalizePosition que nos permite obtener la posición en coordenadas x e y. Habrá tantos rectángulos como respuestas.
5. Un conjunto de flechas que mediante la posición de su inicio y final podremos saber la distancia que hay entre el punto en el que el usuario se ha posicionado y las posiciones de las respuestas (Solo visible en el modo debug).
6. Punto o bola que expresa la posición en la que ha elegido quedarse el usuario, pudiendo obtener del elemento las posiciones x e y. Cuando el usuario decide posicionarse, éste debe hacer click en su bola roja y sin soltar el ratón posicionarse, esto hace saltar un evento mousedown que está relacionado con la constante startDrag que dentro de él contiene un evento mousemove que es el encargado de permitir mover el punto rojo por la pantalla. Este evento hace referencia a una constante llamada onUserMagnetMove del componente SessionView.

# Componente DebugBoardView

Este componente nos servirá para probar el correcto funcionamiento de nuestro anterior componente (BoardView). Nos permite visualizar el formato en el que se recogen los valores del BoardView.

# Componente SessionLogin

Es el componente que permite el login. Puntos a resaltar:

1. Cuando pulsamos el boton login, este está relacionado con el objeto/función joinSession que recoge la información del formulario y envía una petición post a nuestro controlador api. 
2. El controlador nos devuelve un mensaje en formato JSON que con el que podremos sacar su status y actuar en consecuencia, en caso de que sea un error mostramos por pantalla que no se ha podido enviar la solicitud de acceso y en caso que no se haya realizado correctamente el proceso mostraremos un mensaje diciendo que no se ha podido entrar a la sesión. Por último tendremos el caso de que el código sea el 200 es decir éxito, en este caso llamaremos a la función onJoinSession.
3. Cuando llamamos a onJoinSession devolvemos un objeto joinSession que contiene la información del id de session y el id y nombre del participante.


# Componente SessionView

La primera comprobación que realiza este componente es si el status de la sesión es activo, es decir que estemos en el tiempo de contestar una pregunta.
En caso de que no estemos en periodo de contestar mostraremos el componente StatusView al que le pasaremos el id y status de la sesión, el status de la pregunta y el evento onLeaveClick.
En caso de que sí estemos en tiempo de contestar nos mostrará un panel que estará separado en dos:
1. Parte de la pregunta: en la que encontraremos el componente QuestionDetails al que le pasaremos por parámetro el path de la imagen.
2. Parte de la respuesta: en la que se ubicará el componente BoardView al que le pasamos por parámetros las respuestas (en caso de que se haya cargado la pregunta), la posición central en la que se debe ubicar el círculo de la media de las respuestas, un array con las posiciones de las respuestas de otros usuarios. Después una variable asociada al estado de la constante userMagnetPosition en la que se encontrará la posición de la respuesta del usuario, onUserMagnetMove será la función encargada de actualizar el estado de la constante función especificada en el componente BoardView. 

Visto ya lo que retornaría nuestro componenete SessionView pasemos a ver más a profundidad sus atributos, funciones y eventos.

Primero tenemos una variable llamada sessionRef quee usamos para almacenar un valor mutable que no provoca una nueva representación cuando se actualiza y que hace referencia a la sesión del usuario.
Después nos encontramos con constantes las cuales están rastreadas (useState Hook), esto quiere decir que cuentan con un estado y una función de actualización de estado. Cuando algunas de las siguientes constantes actualizan su estado generan una nueva renderización y estarían asociadas a:
1. El status de la sesión (Referencia a la constante de la clase de contexto Sesion.js).
2. El status de la pregunta (Referencia a la constante de la clase de contexto Question.js).
3. La posición de la respuesta del usuario (Referencia al componente BoardView).
4. La posición de las respuestas de otros usuarios (Referencia al componente BoardView).
5. La posición de la media de respuestas (Referencia al componente BoardView).

Tras esto realizaremos efectos secundarios sobre otros componentes.

1. Sobre el id de la sesión y el id del participante:
 
* Realizamos una petición a nuestro servidor http para obtener un json con información sobre la sesión, en caso de recibir una respuesta http con status 200, cambiamos el estado de la sesión a waiting y comprobamos si se ha especificado ya alguna pregunta, si se ha especificado pregunta cambiamos su estado a loading y guardamos el id de ésta.

* Seguimos creando una nueva sesión para el usuario para ello lo primero que haremos es crear un nuevo objeto de tipo Session que nos permite obtener un mensaje de control y otro de actualización mediante mediante su constructor. 
Para la mensaje de control actuaremos en función de su tipo:
** Tipo setup: comprueba que el id de la question sea nulo y actualiza el estado de la pregunta a undefined, en caso de que no se nulo actualiza el estado de la pregunta a loading y asigna el id de la pregunta que vendría en el mensaje de control.
** Tipo start: actualiza el estado de la sesión a active
** Tipo stop: actualiza el estado de la sesión a waiting

Para el mensaje de actualización actualizamos el estado de las posiciones de las respuestas de los demás usuarios.

2. Sobre la pregunta:

Comprobamos que el status de pregunta sea loading, en caso de que sea así realizamos una petición a nuestro servidor http en la que recibiremos información de la pregunta en caso de obtener un código de respuesta 200.
Si no debemos de ignorar los datos, con esta información actualizamos el estado de la pregunta. Después publicamos un mensaje de control en el que está la información "type: ready". Por último retornamos "ignore = true"

3. Sobre la respuesta del usuario y las respuestas de los demás usuarios.
Creamos una constante en a que podremos guardar los puntos de los demás usuarios. Después actualizaremos el estado de la media de respuestas de todos los usuarios, para ello haremos uso de la constante anteriormente nombrada y la posición de la respuesta del usuario. 

Por último comentar las constantes onUserMagnetMove y onLeaveSessionClick.
1. onUserMagnetMove: en caso de que el status de la sesión sea active, actualizamos el estado de la posición de la respuesta del usuario y publicamos un mensaje de actualización en el que incluimos la nueva posición.
2. onLeaveSessionClick: que hará referencia al parámetro/función onleave() del componente.


Seguir con la parte de constantes/funciones.....

# Componente QuestionDetails

Es el componente que muestra información de la pregunta como la imagen, el número de pregunta y el tiempo restante para contestar.

# Componente StatusView

Una vez realizado el login e introducida la sesión, aparece una pequeña ventana que nos muestra la sesión a la que estamos suscritos, el estado de nuestra suscripción (entrando o dentro), un mensaje que nos dice que estamos esperando la pregunta y un botón que nos permite salir de la sesión.

# Clase para el contexto JS Question

Nos permite tener un objeto con atributos de tipo Symbol (Variables únicas que tienen una descripción) congeladas, es decir que no se podrán modificar. Los atributos son tres todos de tipo Symbol:
1. Undefined: cuando no tenemos pregunta definida.
2. Loading: se ha de elegido pregunta pero no se han cargado los detalles de la misma.
3. Loaded: se han cargado los detalles de la pregunta y éstos están disponibles.

# Clase para el contexto JS Session

Nos permite tener un objeto con atributos de tipo Symbol. Constará al igual que Question de tres atributos:
1. Joining: se está obteniendo la información y suscribiéndose a los topics de MQTT.
2. Waiting: esperando a que la pregunta sea definida y que se se carguen sus detalles.
3. Active: los detalles están cargados y se está respondiendo a la pregunta.

También tendremos una clase Session que tendrá las siguientes funcionalidades:
1. Costructor: recibe un id de sesión, un id del participante, un parámetro al que haremos referencia en caso de estar ante un mensaje de control y otro en caso de que sea un mensaje de actualización. Primero conectamos a nuestro servidor mqtt (ws) que está en el puerto 9001. 
* Después definimos un evento que se ejecutará cuando el cliente se conecte al servidor. El cual suscribirá al usuario a dos topics, al de control y al de actualización. 
* Definimos otro evento en caso de que recibamos o enviemos un mensaje a través de alguno de los dos topics. Dentro de este evento una vez se reciben los datos lo primero que hacemos es tratarlos. Debemos comprobar que éstos lleguen en el formato correcto, en caso contrario la ejecución del evento se cortará en ese punto. Debemos comprobar también que los datos que llegan y se envían son referentes a la sesión en la que hemos realizado el login. En caso contrario no se trata esa información y se para la ejecución del evento. Tras las anteriores comprobaciones, debemos saber de qué topic proviene el mensaje, si estamos ante un mensaje de control haremos referencia al parámetro de control pasándole un Json con los datos recibidos en la conexión. En el caso en el que sea un mensaje de actualización lo primero que haremos será saber que usuario a enviado esa información (comprobamos que los datos vengan con un quinto parámetro). Después referenciaremos al parámetro de actualización pasándole como parametros el id del participante y un json con los datos.
2. Una función llamada publishControl que recibirá por parámetro un objeto de js con la información que se quiere enviar por el topic de control. La función como su propio nombre indica se encargará de publicar en el topic de control un json con la información del objeto que hemos recibido por parámetro (información de la pregunta por ejemplo).
3. Otra función llamada publishUpdate que tendrá una función idéntica a la anterior para el topic de actualización. Típicamente el json que se publica hará referencia a la ubicación de la respuesta del participante.
4. Una última función llamada close que cierra la conexión con el servidor mqtt.

