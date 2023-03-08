# Componente BoardView

Nuestro componente BoardView está compuesto por:
1. Un polígono que dependerá del caso el número de preguntas(lados).
2. En los vértices o extremos del polígono irán unos puntos que tendrán una respuesta asignada. Cada punto contará con su posición x e y.
3. Un círculo que marcará el punto en el que se encuentra la media de respuestas.
4. Un rectángulo que expresa la respuesta de otro usuario, va unida a una constante llamada denormalizePosition que nos permite obtener la posición en coordenadas x e y. Habrá tantos rectángulos como respuestas.
5. (Solo visible en el modo debug) Un conjunto de flechas que mediante la posición de su inicio y final podremos saber la distancia que hay entre el punto en el que el usuario se ha posicionado y las posiciones de las respuestas.
6. Punto o bola que expresa la posición en la que ha elegido quedarse el usuario, pudiendo obtener de del elemento las posiciones x e y. Cuando el usuario decide posicionarse, éste debe dejar de arrastrar su bola roja soltando el click izquierdo del ratón. Al soltar el click izquierdo salta un evento a la constante startDrag que...

# Componente DebugBoardView

Este componente nos servirá para probar el correcto funcionamiento de nuestro anterior componente (BoardView), la parte más interesante es ver el formato en el que se recogen los valores del BoardView.

# Componente Header

Este componente es la cabecera de nuestra página.

# Componente SessionLogin

Es el componente que permite el login. Puntos a resaltar:

1. Cuando pulsamos el boton login, este está relacionado con el objeto/función joinSession que recoge la información del formulario  y envía una petición post a nuestro controlador api. 
2. El controlador nos devuelve un mensaje en formato JSON que con el que podremos sacar su status y actuar en consecuencia, en caso de que sea un error mostramos por pantalla que no se ha podido enviar la solicitud de acceso y en caso que no se haya realizado correctamente el proceso mostraremos un mensaje diciendo que no se ha podido entrar a la sesión. Por último tendremos el caso de que el código sea el 200 es decir éxito, en este caso llamaremos a la función onJoinSession.
3. Cuando llamamos a onJoinSession devolvemos un objeto joinSession que contiene la información del id de session y el id y nombre del participante.

# Componente App

Es el componente de la aplicación, es el que react considera principal.


