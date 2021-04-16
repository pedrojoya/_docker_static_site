# 3.4.- Borrado de imágenes

## Borrado individual

Conforme vamos avanzando en el uso de Docker iremos acumulando imágenes en nuestro sistema. Estas imágenes, bien es cierto, no ocupan tanto espacio como una máquina virtual, pero si hemos descargado varias decenas o centenas de las mismas al final nos encontraremos con que podemos llegar a ocupar una cantidad considerable de espacio en disco, si no tenemos cierto control sobre las mismas.

En este caso, se hace necesario eliminar imágenes de nuestro sistema. Para ello podemos usar la orden `docker rmi nombreImagen[:version]`, o su equivalente `docker image rm nombre nombreImagen[:version]` que recibe el nombre de la imagen y opcionalmente la versión. También podemos pasarles el id de la imagen. Veamos unos ejemplos:

!!! success "Ejemplos de eliminación de imágenes" 
    ```bash
    # Eliminación de la imagen mysql:8.0.22
    > docker rmi mysql:8.0.22
    # Eliminación de la imagen con el id dd7265748b5d
    > docker rmi dd7265748b5d
    # Eliminación de la imagen mysq:8.0.22
    > docker image rm mysq:8.0.22
    # Eliminación de la imagen con el id dd7265748b5d
    > docker image rm dd7265748b5d
    # Eliminación de varias imágenes a la vez
    > docker rmi mysql:8.0.22 mysql:5.7
    ```

Sin embargo, debemos tener en cuenta que **para borrar una imagen evidentemente no podemos tener creado un contenedor que esté usándola**. Si lo intentamos, se nos mostrará un mensaje de error.

Podemos forzar el borrado usando el *flag* `-f`, aunque evidentemente eso afectará a los contenedores que la estuvieran usando, por lo que no se recomienda su uso. Por ejemplo:

!!! success "Forzado de eliminación de una imagen que puede estar en uso"
    ```bash
    # Elimina la imagen httpd latest aunque hubiera contenedores que estuvieran usando esa imagen.
    > docker rmi -f httpd
    ```

## Borrado masivo

Este proceso de borrado individual de imágenes puede resultar tedioso si tenemos muchas, por lo que Docker nos proporciona la orden `docker image prune`, que nos va a permitir eliminar varias imágenes de una vez. 

Así, si usamos el *flag* `-a`, se borrarán todas las imágenes que no están siendo usadas por contenedores. Por ejemplo:

!!! success "Eliminación de todas las imágenes que no estén en uso"
```bash
# Eliminación de todas las imágenes que no están en uso
> docker image prune -a
```

Podemos usar el *flag* `--filter` para indicar que solo se borren las imágenes que cumplan las condiciones indicadas en el filtro. Por ejemplo:

!!! success "Eliminación de todas las imágenes creadas hace más de 1o días"
    ```bash
    # Elimina la imágenes creadas hace más de 10 días
    > docker image prune --filter until="240h"
    ```

Si no queremos que se nos solcite confirmación para la eliminación de las imágenes, podemos usar el *flag* `-f`, aunque debemos tener cuidado con él, porque puede que estemos borrando imágenes que no queríamos realmente eliminar.
