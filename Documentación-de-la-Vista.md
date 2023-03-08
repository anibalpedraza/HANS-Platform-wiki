# Componente App

Es el componente de la aplicación, es el que react considera principal. Este componente contendrá información como el id de la sesión, el id y el nombre del usuario. También redireccionará en caso de inicio o cierre de sesión.

# Componente Header

Este componente es la cabecera de nuestra página.
En caso de que el usuario no haya hecho el login se mostrará un menu, el nombre de la plataforma, y un botón con el cartel Join a Session que nos mandará al login.
En caso de que el usuario si haya realizado el login el botón Join a Session desaparece y en su lugar tenemos un botón que incluye el nombre de usuario y un icono que al pulsarlo nos da la opción de salir de la sesión. 


# Componente BoardView

Nuestro componente BoardView está compuesto por:
1. Un polígono que dependerá del caso el número de preguntas(lados).
2. En los vértices o extremos del polígono irán unos puntos que tendrán una respuesta asignada. Cada punto contará con su posición x e y.
3. Un círculo que marcará el punto en el que se encuentra la media de respuestas.
4. Un rectángulo que expresa la respuesta de otro usuario, va unida a una constante llamada denormalizePosition que nos permite obtener la posición en coordenadas x e y. Habrá tantos rectángulos como respuestas.
5. (Solo visible en el modo debug) Un conjunto de flechas que mediante la posición de su inicio y final podremos saber la distancia que hay entre el punto en el que el usuario se ha posicionado y las posiciones de las respuestas.
6. Punto o bola que expresa la posición en la que ha elegido quedarse el usuario, pudiendo obtener de del elemento las posiciones x e y. Cuando el usuario decide posicionarse, éste debe hacer click en su bola roja y sin soltar el ratón posicionarse, esto hace saltar un evento mousedown que está relacionado con la constante startDrag que dentro de él contiene un evento mousemove que es el encargado de permitir mover el punto rojo por la pantalla. Este evento hace referencia a una constante llamada onUserMagnetMove del componente 
 SessionView.

# Componente DebugBoardView

Este componente nos servirá para probar el correcto funcionamiento de nuestro anterior componente (BoardView), la parte más interesante es ver el formato en el que se recogen los valores del BoardView.

# Componente SessionLogin

Es el componente que permite el login. Puntos a resaltar:

1. Cuando pulsamos el boton login, este está relacionado con el objeto/función joinSession que recoge la información del formulario  y envía una petición post a nuestro controlador api. 
2. El controlador nos devuelve un mensaje en formato JSON que con el que podremos sacar su status y actuar en consecuencia, en caso de que sea un error mostramos por pantalla que no se ha podido enviar la solicitud de acceso y en caso que no se haya realizado correctamente el proceso mostraremos un mensaje diciendo que no se ha podido entrar a la sesión. Por último tendremos el caso de que el código sea el 200 es decir éxito, en este caso llamaremos a la función onJoinSession.
3. Cuando llamamos a onJoinSession devolvemos un objeto joinSession que contiene la información del id de session y el id y nombre del participante.


# Componente SessionView

La primera comprobación que realiza este componente es si el status de la sesión es activo, es decir que estemos en el tiempo de contestar una pregunta.
En caso de que no estemos en periodo de contestar mostraremos el componente StatusView.
En caso de que si estemos en tiempo de contestar nos mostrará un panel que estará separado en dos:
1. Parte de la pregunta: en la que encontraremos el componente QuestionDetails.
2. Parte de la respuesta: en la que se ubicará el componente BoardView.

Seguir con la parte de constantes/funciones.....

# Componente QuestionDetails

Es el componente que muestra información de la pregunta como la imagen, el número de pregunta y el tiempo restante para contestar.

# Componente StatusView

Una vez realizado el login e introducida la sesión, aparece una pequeña ventana que nos muestra la sesión a la que estamos suscritos, el estado de nuestra suscripción (entrando o dentro), un mensaje que nos dice que estamos esperando la pregunta y un botón que nos permite salir de la sesión.

# Clase para el contexto JS Question

# Clase para el contexto JS Session
