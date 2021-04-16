# 3.1.- Descarga de imágenes

Como ya sabemos si hacemos `docker run imagen[:version]` indicando una imagen que no hayamos descargado previamente, ésta será descargada, antes de crear y ejecutar el contenedor. Por ejemplo:

!!! success "Descarga por ejecución la imagen de mysql en su versión 8.0.22"
    ```bash
    # Supongamos que es la primera vez que usamos la imgaen mysql en su versión 8.0.22
    > docker run -it -d --name mysql8 -p 3306:3306 mysql:8.0.22
    ```
    
Sin embargo, esa no es la opción recomendada, ya que no nos propociona control sobre el proceso de descarga. Docker proporciona la orden `docker pull nombreImagen[:version]`, especializada en la descarga de imágenes, en la que indicaremos el nombre de la imagen y la versión de la misma (*tag*). Si no indicamos la versión, se descargará la última versión (*latest*) disponible de la imagen. Por ejemplo:

!!! success "Descarga de la imagen de mysql en su versión 8.0.22"
    ```bash
    # Descarga de mysql en su versión 8.0.22
    > docker pull mysql:8.0.22
    ```

En cualquiera de los dos casos, durante el proceso de descarga se nos mostrarán una serie de líneas informativas. Cada una de estas líneas corresponde a una de las capas que conforman la imagen docker. 

Así, una imagen está construida a partir de una imagen base, a la que se agrega una capa adicional (imagen intermedia) por cada uno de los elementos adicionales que queramos en nuestra imagen final. 

Estas imágenes intermedias pueden ser reutilizadas en la construcción de otras imágenes. De hecho, si una capa intermedia es usada por más de una imagen que estemos usando, no tendrá que ser descargada varias veces, sino que será reutilizada, ya que se encontrará en la caché de imágenes. Más adelante hablaremos con más detenimiento de ello.

Si no queremos que se nos muestre toda la información sobre las capas intermedias al descargar una imagen podemos usar el *flag* `-q` o `--quiet`. Por ejemplo:

!!! success "Descarga de la imagen httpd sin mostrar las capas intermedias"
    ```bash
    # Descarga de la imagen httpd sin mostrar las capas intermedias
    > docker pull -q httpd  o docker pull --quiet
    ```

Aunque no se recomienda, podemos bajar todas las versiones disponibles de una imagen de una sola vez, mediante el flag `-a` o `--all`. Debemos ser cautelosos con esta orden, porque una imagen puede tener muchas versiones, por lo que el espacio en disco y el tiempo de descarga podrá ser muy grande. Por ejemplo:

!!! success "Descarga de todas las versiones de la imagen php. ¡CUIDADO!"
    ```bash
    # Descarga todas las versiones de la imagen php. ¡CUIDADO!
    > docker pull -a php o docker pull --all-tags php
    ```
