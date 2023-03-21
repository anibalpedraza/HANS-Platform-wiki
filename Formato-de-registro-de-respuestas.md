Cuando el administrador decide dar comienzo al periodo de responder a la pregunta, se crea una subcarpeta llamada sesion_log que a su vez crea otra subcarpeta cuyo nombre estará asociado a la fecha de creación. Dentro de ella podremos encontrar dos archivos:
1. session.json: contiene información referente a la sesión, como la fecha de puesta en marcha, el id de la sesión, la pregunta que se haya respondido y una lista de participantes cada uno con su id, username y status.
2. log.csv: contiene siete campos,
* Id del participante
* Fecha en la que se ha recogido la respuesta (útil para sacar la respuesta final del usuario, por ahora no funciona)
* 5 distancias desde el medio del polígono a cada una de las respuestas, podemos comprobar el funcionamiento de estas distancias en "/debug" (Debemos pulir el formato de estas distancias, ya que podemos tener preguntas con diferente número de respuestas).
Formato en el que se recogen los datos:
![image](https://user-images.githubusercontent.com/115094288/226596963-205e873a-66c0-43a6-b6fd-c4ce6ba25130.png)
Formato en el que se guardan las respuestas de los usuarios:
![image](https://user-images.githubusercontent.com/115094288/226597245-3d94d330-0178-4181-a799-6326302d68e9.png)
![image](https://user-images.githubusercontent.com/115094288/226597372-5ed7f8d1-12f0-48d3-b74c-88e2910d136a.png)
![image](https://user-images.githubusercontent.com/115094288/226597446-94db64b2-d822-4bb8-9435-9dddd5f8c3d9.png)

Las fotos en las que se muestra cómo se guardan las respuestas de los usuarios no corresponde con la imagen de cómo se recogen los datos.

