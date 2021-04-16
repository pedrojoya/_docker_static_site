# 7.3.- La orden docker-compose

Una vez hemos creado el archivo `docker-compose.yml`, cuando queramos lanzar nuestra aplicación tendremos que ejecutar la herramienta `docker-compose` desde el directorio en el que se encuente el fichero `docker-compose.yml`.

Esta herramienta tiene varias subórdenes y cada una de ellas varias opciones. Nosotros nos centraremos en las combinaciones más comunes y que pueden resultar más interesantes.

La primera de ella es conocer la versión instalada de la herramienta:

!!! success "Conocer la versión de docker-compose"
    ```bash
    # Obtener la versión de docker-compose.
    > docker-compose --version
    ```

Si en nuestro `docker-compose.yml` hemos indicado que alguno de los servicios debe lanzarse a partir de una imagen que debe ser construida a partir de un fichero `Dockerfile`, entonces podremos lanzar dicho proceso de construcción mediante la orden `build`

!!! success "Construcción de las imágenes de los servicios"
    ```bash
    # Obtener la versión de docker-compose.
    > docker-compose build
    ```

Para arrancar nuestra aplicación ejecutaremos la siguiente orden:

!!! success "Lanzar la aplicación"
    ```bash
    # Lanza los contenedores descritos en el archivo docker-compose.yml.
    > docker-compose up
    ```

Si queremos lanzar los servicios de la apliación en modo *detach* de manera que no se muestren los mensajes de *log* y se ejecute en segundo plano, usaremos el *flag* `-d`:

!!! success "Conocer la versión de docker-compose"
    ```bash
    # Lanza en segundo plano los contenedores descritos en el archivo docker-compose.yml.
    > docker-compose up -d
    ```

Si hemos lanzado los contenedores en segundo plano, es posible que queramos ver los *logs* que está produciendo un determinado contenedor de la aplicación. Para ello ejecutaremos la orden `logs nombreServicio` de la siguiente manera:

!!! success "Operaciones de cambio de estado con los contenedores de la aplicación"
    ```bash
    # Muestra los logs del servicio servicio1
    > docker-compose logs servicio1
    ```

También tenemos una serie de opciones para pausar, reactivar, parar y reiniciar los contenedores de la aplicación:

!!! success "Operaciones de cambio de estado con los contenedores de la aplicación"
    ```bash
    # Pausa los contenedores que previamente se han lanzado con docker-compose up.
    > docker-compose pause
    # Reanuda los contenedores que previamente se han pausado.
    > docker-compose unpause
    # Detiene los contenedores que previamente se han lanzado con docker-compose up.
    > docker-compose stop
    # Inicia los contenedores descritos en el docker-compose.yml que estén parados.
    > docker-compose run
    # Reinicia los contenedores. Orden ideal para reiniciar servicios con nuevas configuraciones.
    > docker-compose restart
    ```

Si queremos ejecutar una determinada orden en uno de los contenedores de la aplicación, usaremos la orden `exec nombreServicio orden` de la siguiente forma:

!!! success "Ejecutar una orden en un servicio de la aplicación"
    ```bash
    # Ejecuta la orden /bin/bash en el servicio servicio1
    > docker-compose exec servicio1 /bin/bash
    ```

Cuando queramos terminar definitivamente la ejecución de nuestra aplicación usaremos la orden `down`, que para y eliminar los contenedores de la aplicación, así como las redes creadas con `docker-compose up`. Si además queremos eliminar los volúmenes que hayan sido creados por la aplicación deberemos usar el *flag* `-v`:

!!! success "Finalización de la ejecución de la aplicación"
    ```bash
    # Para y elimina los contenedores y las redes creadas para la aplicación.
    > docker-compose down
    # Para y elimina los contenedores, las redes y los volúmenes creados para la aplicación
    > docker-compose down -v
    ```

Finalmente, mencionar que con el subcomando `docker-compose top` se muestran los procesos que están ejecutándose en cada uno de servicios (contenedores).