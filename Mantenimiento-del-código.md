## Mantenimiento del backend

(Para subir los cambios que hayamos hecho en local y que hemos subido al repositorio Hans-Platform-Backend)
* cd Hans-Platform-Backend
* git pull

Con esto ya tendríamos el código actualizado

## Mantenimiento del frontend

En este caso no podriamos hacer directamente el pull, ya que la carpeta node-modules es distinta entre linux y windows.
Si los cambios no son muy grandes recomiendo hacer los cambios con herramientas como nano. Dentro de nano con Alt+a podemos seleccionar todo el contenido de la clase y sustituirlo por el nuevo.

Si son muchos cambios, deberíamos hacer un clone del repositorio y sobreescribir la carpeta de src.
