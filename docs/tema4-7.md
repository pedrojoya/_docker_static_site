# 4.7.- Tareas del tema

!!! question "Creación y uso de volúmenes"
    === "Enunciado"
        1. Crea dos volúmenes, unos llamado `volumen_datos` y otro `volumen_web`
        2. Listar los volúmenes disponibles, para demostrar que los anteriores han sido creados.
        3. Arranca un contenedor llamado `c1` sobre la imagen `php:7.4-apache` que monte el volumen `volumen_web` en la ruta `/var/www/html``
        4. Arranca un contenedor llamado `c2` sobre la imagen `mariadb` que monte el volumen `volumen_datos` en la ruta `/var/lib/mysql` y cuya contraseña de *root* sea `admin`.
        5. Lista los contenedores en ejecución, para comprobar que los dos anteriores han sido lanzados.
        6. Para y borra el contenedor `c2`
        7. Lista los contenedores en ejecución, para comprobar que ya solo queda uno.
        8. Muestra información sobre el contenedor `c1` para demostrar que uso el volumen `volumen_web`.
        9. Borra el volumen `volumen_datos`
        10. Lista los volúmenes para demostrar que el volumen `volumen_datos` ha sido eliminado.

    === "Solución"
        ```bash
        # Creación de los volúmenes
        > docker volume create volumen_datos
        > docker volume create volumen_web
        # Listar volúmenes
        > docker volume ls
        # Creación del contenedor c1
        > docker run -d --name c1 --mount type=volume,scr=volumen_web,dst=/var/www/html php:7.4-apache
        # Creación del contenedor c2
        > docker run -d --name c2 --mount type=volume,scr=volumen_datos,dst=/var/lib/mysql mariadb
        # Listar contenedores en ejecución
        > docker ps
        # Parar y borrar el contenedor c2
        > docker stop c2
        > docker rm c2
        # Listar contenedores en ejecución
        > docker ps
        # Mostrar información sobre el contenedor c1 para demostrar que usa el volumen volumen_web
        > docker inspect c1 --format '{{ .Mounts }}'
        # Borrar el volumen volumen_datos
        > docker volume rm volumen_datos
        # Listar volúmenes para demostrar que se ha eliminado el anterior.
        > docker volume ls
        ```

!!! question "Bind mount para compartir datos"
    === "Enunciado"
        1. Crea una carpeta llamada `saludo` y dentro de ella crea un fichero llamado `index.html` con el contenido `<h1>HOLA SOY XXXXXX</h1>`, sustituyendo `XXXXX` por tu nombre.
        2. Arranca un contenedor llamado `c1` basado en la imagen `php:7.4-apache` que haga un *bind mount* de la carpeta `saludo` en la carpeta `/var/www/html` del contenedor, y que redirecciones su puerto `80` al puerto `8181` del *host*.
        3. Arranca un contenedor llamado `c2` basado en la imagen `php:7.4-apache` que haga un *bind mount* de la carpeta `saludo` en la carpeta `/var/www/html` del contenedor, y que redirecciones su puerto `80` al puerto `8282` del *host*.
        4. Muestra en el navegador el archivo `index.html` del contenedor c1.
        5. Muestra en el navegador el archivo `index.html` del contenedor c2.

    === "Solución"
        ```bash
        # Arrancar contenedor c1
        > docker run -d --name c1 -p 8181:80 -v /Users/pedro/saludo:/var/www/html php:7.4-apache
        # Arrancar contenedor c2
        > docker run -d --name c2 -p 8282:80 -v /Users/pedro/saludo:/var/www/html php:7.4-apache
        ```

        Para mostrar en el navegador del archivo `index.html` del contenedor c1 visitaremos [http://localhost:8181/index.html](http://localhost:8181/index.html).

        Para mostrar en el navegador del archivo `index.html` del contenedor c2 visitaremos [http://localhost:8282/index.html](http://localhost:8282/index.html).


!!! question "Depuración con Bind Mounts"
    1. Configura Visual Studio Code para poder depurar cualquier ejemplo que se te ocurra en PHP.
    2. Crear una carpeta llamada `debug` que tenga en su interior un archivo PHP. 
    3. Arranca un contenedor basado en `php:7.4-apache`, llamado `debugPHP`, que deberá mostrar los contenidos en el puerto `8585`, y monta en él la carpeta `debug` en la carpeta `/var/www/html` del contenedor.
    4. En Visual Studio Code establece un *breakpoint* en cualquier línea del archivo PHP que creaste en la carpeta `debugPHP`.
    5. Ejecuta con depuración el archivo PHP y comprueba que la ejecución se para en el *breakpoint*
      