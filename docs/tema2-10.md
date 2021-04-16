# 2.10.- Parada, inicio tras parada, reinicio y eliminación de un contenedor

Con el paso del tiempo iremos ejecutando muchos contenedores y llegará un momento en que tengamos la necesidad de realizar operaciones como las siguientes:

- Parar un contenedor que no estamos necesitando o que esté ejecutando un servicio que ocupe un puerto que queremos ocupar con otro servicio o contenedor.
- Volver a iniciar un contenedor que estaba parado pero que vamos a volver a necesitar.
- Reiniciar un contenedor para que nuevas opciones de configuración sean aplicadas.
- Eliminar un contenedor que instalamos y que ya no necesitamos.

## Parada de un contenedor

Como ya hemos comentado, cuando lanzamos un contenedor de un sistema operativo, si no lo hacemos de forma interactiva, el sistema será parado automáticamente.

Si, por el contrario, lo lanzamos de forma interactiva, y no especificamos ninguna orden para que sea ejecutada inicialmente, por defecto el contenedor mostrará un *shell*. Si estando en dicho *shell* ejecutamos `exit`, el contenedor será automáticamente parado.

Por otro lado, cuando lanzamos un contenedor correspondiente a un servicio y usamos el *flag* `-d` para que se ejecute en segundo plano, el servicio seguirá en ejecución, aunque no estemos dentro de él.

Si queremos parar el servicio temporalmente, podemos ejecutar la orden `docker stop contenedor`. Si el contenedor ya estaba parado, la orden simplemente no hará nada.

Así, en el siguiente ejemplo, se para un contenedor llamado `servidorWeb`:

!!! success "Parada del contenedor llamado servidorWeb"
    ```bash
    # Para el contenedor llamado servidorWeb
    > docker stop servidorWeb
    ```

Si queremos que se espere un determinado número de segundos antes de parar el contenedor, podemos usar el *flag* `-t` o `--time`. Por ejemplo:

!!! success "Parada del contenedor llamado servidorWeb tras 10 segundos"
    ```bash
    # Para el contenedor llamado servidorWeb tras 10 segundos
    > docker stop -t 10 servidorWeb
    ```

## Reinicio de un contenedor parado

Si tenemos un contenedor parado, esto es, en estado *Exited*, ya sea porque se haya parado automáticamente o porque explícitamente lo hayamos parado nosotros, podemos volver a inicialo usando la orden `docker start contenedor`. Si el contenedor en cuestión ya estaba en ejecución, la orden simplemente no hará nada.

Así, en el siguiente ejemplo volvemos a iniciar el contenedor llamado `servidorWeb` que habíamos parado anteriormente:

!!! success "Volver a iniciar el contenedor llamado servidorWeb"
    ```bash
    # Volver a iniciar el contenedor llamado servidorWeb
    > docker start servidorWeb
    ```

Si queremos volver a iniciar el contenedor con acceso interactivo deberemos usar el flag `-i` de `docker start`.

Así, si queremos acceder al contenedor llamado `servidorWeb` de forma interactiva haríamos:

!!! success "Volver a iniciar el contenedor llamado servidorWeb de forma interactiva"
    ```bash
    # Volver a iniciar el contenedor llamado servidorWeb de forma interactiva
    > docker start -i servidorWeb
    ```

!!! warning "Aviso"
    Si se trata de un contenedor de un sistema operativo, y no lo hemos arrancado inicialmente de forma interactiva, será parado automáticamente, pero además, no se podrá iniciar de nuevo de forma interactiva aunque usemos `docker start -i`.

## Reinicio de un contenedor con nueva configuración

En algunas ocasiones queremos parar un contenedor en ejecución y volver a iniciarlo pero con una configuración distinta a la anterior. Para esos casos Docker nos proporciona la orden `docker restart contenedor`, que hace las dos cosas anteriores, es decir, parar el contenedor y después iniciarlo con la nueva configuración. Si el contenedor ya estuviera parado, simplmente se iniciará.

Así, en el ejemplo anterior, vamos a parar el contenedor llamado `servidorWeb` y lo vamos a volver a iniciar, todo de una sola vez:

!!! success
    ```bash
    # Parar y volver a iniciar el contenedor llamado servidorWeb
    > docker restart servidorWeb
    ```

Si queremos que se espere un determinado número de segundos antes de parar el contenedor, podemos usar el *flag* `-t` o `--time`. Por ejemplo:

!!! success "Parada del contenedor llamado servidorWeb tras 10 segundos y volver a iniciarlo"
    ```bash
    # Reinicia el contenedor llamado servidorWeb tras 10 segundos
    > docker restart -t 10 servidorWeb
    ```

## Eliminación de un contenedor

En muchas ocasiones ya no estamos interesados en seguir usando un determinado contenedor, y para que no ocupe espacio en disco queremos eliminarlo. Para ello Docker nos proporciona la orden `docker rm contenedor`.

Así, si queremos eliminar el contenedor llamado `servidorWeb` ejecutaríamos:

!!! success "Eliminación de un contenedor parado"
    ```bash
    # Borrar el contenedor llamado servidorWeb, que debe estar parado
    > docker rm servidorWeb
    ```

Por defecto, si el contenedor que queremos eliminar está en ejecución, la orden anterior fallará y lanzará un mensaje de error informando de que antes es necesario parar el contenedor.

Si queremos forzar la eliminación del contenedor, aunque esté en ejecución, podemos usar el *flag* `-f` o `--force`. Por ejemplo:

!!! success "Fuerza la eliminación del contenedor servidorWeb, aunque esté en ejecución"
    ```bash
    # Fuerza la eliminación del contenedor servidorWeb, aunque esté en ejecución
    > docker rm -f servidorWeb
    ```

La orden `docker rm` nos proporciona otro *flag* muy interesante, `-v` o `--volumes`, que hace que se eliminen además del contenedor todos los volúmnes que éste estuviera usando y no estuvieran siendo usados por otros contenedores.

Así, si queremos forzar la eliminación no solo del contenedor sino también de los volúmenes que use en solitario haríamos:

!!! success "Fuerza la eliminación del contenedor servidorWeb, aunque esté en ejecución, y también de los volúmnes que use en solitario."
    ```bash
    # Fuerza la eliminación del contenedor servidorWeb, aunque esté en ejecución, y también de los volúmnes que use en solitario.
    > docker rm -fv servidorWeb
    ```

Como ya comentamos antes, si queremos forzar la eliminación de todos los contenedores, podemos hacerlo en una única orden, haciendo uso de la orden `docker ps -aq` para obtener solo los ids de los contenedores:

!!! success "Fuerza la eliminación de todos los contenedores y de sus volúmenes"
    ```bash
    # Fuerza la eliminación de todos los contenedores y de sus volúmenes
    > docker rm -fv `docker ps -aq`
    ```
