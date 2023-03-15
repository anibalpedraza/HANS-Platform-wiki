Cuando el administrador decide dar comienzo al periodo de responder a la pregunta, se crea una subcarpeta llamada sesion_log que a su vez crea otra subcarpeta cuyo nombre estará asociado a la fecha de creación. Dentro de ella podremos encontrar dos archivos:
1. session.json: contiene información referente a la sesión, como la fecha de puesta en marcha, el id de la sesión, la pregunta que se haya respondido y una lista de participantes cada uno con su id, username y status.
2. log.csv: contiene siete campos,
* Id del participante
* Fecha en la que se ha recogido la respuesta (útil para sacar la respuesta final del usuario, por ahora no funciona)
* 5 distancias desde el medio del polígono a cada una de las respuestas, podemos comprobar el funcionamiento de estas distancias en "/debug" (Debemos pulir el formato de estas distancias, ya que podemos tener preguntas con diferente número de respuestas).