# Componente BoardView

Es el componente principal de la vista, es nuestro pentágono con la bolita de un usuario y con las de los demás. La bola o círculo tiene un evento asociado cuando la soltamos con el ratón.

# Componente DebugBoardView

Este componente nos servirá para probar el correcto funcionamiento de nuestro anterior componente (BoardView), la parte más interesante es ver el formato en el que se recogen los valores del BoardView.

# Componente Header

Este componente es la cabecera de nuestra página.

# Componente SessionLogin

Es el componente que permite el login. Puntos a resaltar:

1.- Cuando pulsamos el boton login, este está relacionado con el objeto/función joinSession que recoge la información del formulario  y envía una petición post a nuestro controlador api. 
2.- El controlador nos devuelve un mensaje en formato JSON que con el que podremos sacar su status y actuar en consecuencia, en caso de que sea un error mostramos por pantalla que no se ha podido enviar la solicitud de acceso, en caso que no se haya realizado correctamente el proceso mostraremos un mensaje diciendo que no se ha podido entrar a la sesión. Por último tendremos el caso de que el código sea el 200 es decir éxito, en este caso llamaremos a la función onJoinSession.
3.- Cuando llamamos a onJoinSession devolvemos un objeto joinSession que contiene la información del id de session, id y nombre del participante.


