# Interfaz de control (Server/gui)
## init
## Participant (ParticipantWidget)
Hereda de la clase QWidget y nos apoyaremos en la clase de contexto Participant.
Tendremos dos funciones:
1. init() (constructor): llama al constructor de Qwidget e inicializa el participante al pasado por parámetro. Enlaza la señal on_status_changed con la función on_status_changed. Inicializamos una organización de widgets en horizontal a la que añadimos:
* Un widget con el id del participante
* Un widget con el nombre del participante
* Un widget con el valor de status del participante.
2. on_status_changed(): actualizará el valor status del participante en la interfaz en caso de que éste cambie.
## Session
Se apoya en las clases de contexto AppContext, Participant, Session y SessionCommunicator. También hará uso de ParticipantWidget.
### SessionListItem
Hereda de QListWidgetItem, únicamente cuenta con su método constructor que a su vez llama al constructor de QListWidgetItem, inicializa el atributo sesión y fija el texto 'Session ' + el id de la sesión.
### SessionPanelWidget
Hereda de QWidget. Métodos:
1. init() (Constructor): llama al constructor de QWidget, inicializa la session a none, un QTimer y enlaza la señal  de timeout de éste último al método on_duration_timer_timeout. Llama a la función setupUI() y  en caso de que la session no sea none llama a la función set_session().
2. set_connection_status(), fijará el texto de connection_txt en función del status de SessionCommunicator.
3. on_connection_status_changed(), se disparará en caso de que el valor de la variable status de SessionCommunicator se vea modificada.
4. set_status(), nos permite fijar fijar el texto de status_txt en función del valor que tenga el status de Session.
5. on_status_changed(), se disparará cuando se modifique el valor de status de Session. Llamará a set status para actualizar el estado de la sesión. Después habilitará duration_txt y question_cbbox si el nuevo status es waiting, en caso de que no sea waiting los deshabilitará.
6. on_duration_timer_timeout(), comprueba que exista un objeto session en cuyo caso calcula el tiempo restante en miliseguntos y fija el texto de duration_timer_lbl con el formato adecuado 'hh:mm:ss'. Si el tiempo restante es 0 deshabilita start_btn y llama a la función stop de Session.
7. on_question_changed(), primero deshabilita question_cbbox, En caso de que el id de la pregunta que se pasa por parámetro a la función sea none se modifica el valor de active_question a none, en caso contrario modificamos el valor de active_question al id de la pregunta que se nos pasa por parámetro en caso de que éste sea un dato de tipo entero.
8. on_question_notified(), habilita question_cbbox.
9. add_participant_widget(), inicializa un objeto de tipo ParticipantWidget y otro de tipo QListWidgetItem (lista de participantes), a éste último se le fija un tamaño recomendado. Después añade a participants_list el objeto de tipo QListWidgetItem y fija en éste al objeto ParticipantWidget.
10. on_participant_joined(), en caso de que estemos en la sesión correcta, llamamos a la función add_participant_joined() a la que le pasamos el participante que nos entra por parámetro a la función. Después deshabilitamos start_btn y fijamos el texto de participants_ready_txt con el valor de ready_participants_count + '/' + valor de participants de
Session.
11. on_participants_ready_changed(), comprobamos primero que el valor status de la sesión sea waiting, después habilitamos o deshabilitamos start_btn si coinciden dos parámetros de la función. Por último fijamos el texto de participants_ready_txt también con estos dos parámetros.
12. on_start_btn_clicked(), deshabilita start_btn, comprueba que status de Session sea waiting, si cumple con esta condición llama al método start de session. En caso de que no cumpla la condición comprobamos si el status de Session es active, en cuyo caso llamaremos al método stop de Session.
13. on_start(), fija el texto de start_btn a 'Stop' y lo habilita, cambia el índice actual a 1 y llama al método start de duration_timer pasándole 10.
14. on_stop(), fija el texto de start_btn a 'Start' y lo habilita, cambia el índice actual a 0 y llama al método stop de duration_timer.
15. set_session(),
16. setupUI(), 
--Acabar los dos métodos que quedan y poner herencias, imports de todas las clases y poner al lado del titulo el nombre por el que se le reconoce en imports--

