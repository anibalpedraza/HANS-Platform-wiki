## Script de sincronización

Cuando la instancia arranca se debería de sincronizar el código que tengamos en github, de esta manera nuestra aplicación siempre correrá la última versión que tengamos. En HansTest tendremos el código de la rama Dev y en HansBack el código de la rama main. Si en el lanzamiento fallase la sincronización tenemos la opción de correr el script de sincronización manualmente. Para correr el script debemos meternos en la instancia y lanzar el script llamado collectionsSync.sh.

## Mantenimiento del backend

(Para subir los cambios que hayamos hecho en local y que hemos subido al repositorio Hans-Platform-Backend)
* cd Hans-Platform-Backend
* git pull

Con esto ya tendríamos el código actualizado

## Mantenimiento del frontend

En este caso no podriamos hacer directamente el pull, ya que la carpeta node-modules es distinta entre linux y windows.
Si los cambios no son muy grandes recomiendo hacer los cambios con herramientas como nano. Dentro de nano con Alt+a podemos seleccionar todo el contenido de la clase y sustituirlo por el nuevo.

Si son muchos cambios, deberíamos hacer un clone del repositorio y sobreescribir la carpeta de src.
