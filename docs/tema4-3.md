# 4.3.- Asociando almacenamiento a los contenedores

## El flag --mount

Para asociar almacenamiento a nuestro contenedor, cuando lo lancemos con la orden `docker run` deberemos usar el *flag* `--mount`.

Esta operación se conoce como montaje, y se puede realizar tanto si el almacenamiento que queremos asociar corresponde a un volumen o a un *mount bind*, esto es, a un directorio de nuestro host gestionado por nosotros.

El *flag* `--mount` tiene una sintaxis un tanto especial, ya que recibe como argumento una serie de pares propiedad=valor separados por coma. Las propiedades con las que trabajemos serán:

- `type`: Tipo de almacenamiento.
- `src`: Origen del almacenamiento en el host.
- `dst`: Directorio del contenedor con el que se quiere asociar, cuyo contenido será sobrescrito por la asociación.
- `readonly`: Para que el contenedor solo tenga acceso de lectura al origen del almacenamiento.

A la hora de realizar el montaje debemos tener en cuenta que el contenido de la carpeta de destino del contenedor será sobrescrita por el origen. Por tanto deberemos tener especial cuidado, sobre todo cuando estamos trabajando con carpetas que pueden contener datos y configuraciones varias.

Por este motivo, si usamos imágenes de DockerHub, debemos leer la información que cada imagen nos proporciona en su página sobre cómo persistir los datos de esa imagen, ya sea con volúmenes o *bind mounts*, y cuáles son las carpetas importantes en caso de ser imágenes que contengan ciertos servicios, como por ejemplos servidores web, servidores de base de datos, etc.

Una vez realizado el montaje, queda reflejado en la salida de la orden `docker inspect` sobre dicho contenedor, que mostrará en el valor `{{ .Mounts }}` los montajes realizados.

Existen opciones adicionales, como por ejemplo hacer que el montaje sea de sólo lectura. Para saber más visite [https://docs.docker.com/storage/bind-mounts/](https://docs.docker.com/storage/bind-mounts/) y [https://docs.docker.com/storage/volumes/](https://docs.docker.com/storage/volumes/).

## Montaje de volúmenes

Si queremos **montar un volumen** en nuestro contenedor, entonces la propiedad `type` deberá tomar el valor `volume`, y la propiedad `src` el nombre de un volumen existente. Veamos un ejemplo:

!!! success "Montar el volumen data como directorio htdocs del servidor Apache"
    ```bash
    # Montar el volumen data como directorio htdocs del servidor Apache
    > docker run --name apache -p 80:80 --mount type=volume,src=data,dst=/usr/local/apache2/htdocs httpd
    ```

Si no se especifica la propiedad `src` en el montaje de un volumne, se creará un volumen cuyo nombre corresponderá al mismo del id del mismo. Por ejemplo.

!!! success "Crear un volumnen automáticamente y montarlo como directorio htdocs del servidor Apache"
    ```bash
    # Crear un volumen automáticamente y montarlo como directorio htdocs del servidor Apache 
    > docker run --name apache -p 80:80 --mount type=volume,dst=/usr/local/apache2/htdocs httpd
    ```

Una vez montado el volumen en el contenedor podemos copiar en él los archivos que queramos usando la orden `docker cp`. Por ejemplo:

!!! success "Copiar el directorio actual del host en el volumen data montado como directorio htdocs del servidor Apache"
    ```bash
    # Copiar el directorio actual del host en el volumen data montado como directorio htdocs del servidor Apache
    > docker cp . data:/usr/local/apache2/htdocs
    ```

Como vemos, si estamos en una fase de desarrollo en la que los archivos de nuestra carpeta de desarrollo va cambiando constantemente este proceso de copia de archivos al volumen no es lo más óptimo. De ahí que durante la etapa de desarrollo el mecanismo recomendado sea el *bind mount*.

Sin embargo, para producción el uso de volúmenes tiene mucho más sentido, mejorando además el rendimiento. De ahi que sea la opción recomendada para ese caso.

## Bind mounts

Si queremos realizar un *bind mount* para que un directorio de nuestro *host*, controlado por nosotros, se monte en el sistema de archivos de un contenedor en un directorio concreto, entonces en el montaje deberemos usar para la propiedad `type` es valor `bind` y para la propiedad `src` la ruta del directorio de nuestro *host*. Veamos un ejemplo:

!!! success "Montar el directorio web del usuario como directorio htdocs del servidor Apache"
    ```bash
    # Montar el directorio web del usuario como directorio htdocs del servidor Apache.
    > docker run --name apache -p 80:80 --mount type=bind,src=/home/usuario/web,dst=/usr/local/apache2/htdocs httpd 
    ```

## El flag -v

Docker nos ofrece otra posibilidad a la hora de hacer montajes, que consiste en usar el *flag* `-v` o `--volume` en la orden `docker run`. Este *flag* es más antiguo que `--mount`, y tiene menos opciones y comportamientos, por lo que no se recomienda su uso. 

Utiliza una sintaxis distinta, más compacta pero menos expresiva. Así, si queremos montar un volumen indicaremos como valor del *flag* `nombreVolumen:dirDestinoContenedor[:opciones]`. Por ejemplo:

!!! success "Montar el volumen data como directorio htdocs del servidor Apache"
    ```bash
    # Montar el volumen data como directorio htdocs del servidor Apache
    > docker run --name apache -p 80:80 -v data:/usr/local/apache2/htdocs httpd
    ```

Si queremos realizar un *bind mount*, entonces la sintaxis será `dirOrigen:dirDestino[:opciones]`. Por ejemplo:

!!! success "Montar el volumen data como directorio htdocs del servidor Apache"
    ```bash
    # Montar el directorio web del usuario como directorio htdocs del servidor Apache.
    > docker run --name apache -p 80:80 -v /home/usuario/web:/usr/local/apache2/htdocs httpd 
    ```
  
  En la sintaxis anterior la parte de `:opciones` es opcional, y su valor más frecuente es `ro` para indicar que el contenedor solo tenga acceso de lectura (*read only*) al almacenamiento que se haya montado, ya sea un volumen o un *bind mount*.
  