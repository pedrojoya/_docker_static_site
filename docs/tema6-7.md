# 6.7.- Tareas del tema

!!! question "Creación de imágenes a partir de contenedores en ejecución"
    === "Enunciado"
        1. Arrancar un contenedor sobre la imagen `ubuntu:20.04`.
        2. Instala en el contenedor el editor nano (`apt install nano`).
        3. Instala en el contenedor el editor vim (`apt install vim`).
        4. Instala en el contenedor las herramientas de red (`apt install inetutils-tools`).
        5. Instala en el contenedor las herramientas dns (`apt install dnsutils`).
        6. Crea en el contenedor un usuario llamado `usuario` con contraseña `usuario` (`adduser usuario`).
        7. Crea una imagen docker llamada `tuUsuarioDockerHub/a61`.
        8. Lista las imágenes disponibles para comprobar que se ha creado
        9. Sube la imagen creada a DockerHub.
    === "Solución"
        ```bash
        # Crear contenedor u1
        > docker run -it --name u1 ubuntu:20.04
        # Instalación de aplicaciones en el contenedor
        root@...:/# apt update
        root@...:/# apt install nano
        root@...:/# apt install vim
        root@...:/# apt install inetutils-tools
        root@...:/# apt install dnsutils
        root@...:/# adduser usuario
        root@...:/# exit
        # Crear imagen pedrojoya/a61
        > docker commit -a "Pedro Joya" -m "Versión con utilidades" u1 pedrojoya/a61
        # Listar imágenes disponibles
        > docker images
        # Subir imagen a DockerHub
        > docker login
        > docker push pedrojoya/a61
        ```

!!! question "Creación de imágenes a partir de ficheros Dockerfile"
    === "Enunciado"
        1. Crea un fichero `Dockerfile` que realice lo siguiente:

            * Parta de la imagen `php:7.4-apache`.
            * Instale `nano` (`apt install -y nano`)
            * Instale `git` (`apt install -y git`)
            * Coloque en el directorio raíz del servidor apache (/var/www/html) dos ficheros:
                * `index.html`, que contenga `HOLA SOY XXXXXX`, sustituyendo `XXXXX` por tu nombre
                * `info.php`, que contenga `<?php phpinfo(); ?>`
        2. Construye una imagen `nombreUsarioDockerHub/a62` a partir del archivo `Dockerfile`.
        3. Lista las imágenes disponibles para comprobar que ha sido creada.
        4. Sube la imagen creada a DockerHub.
    === "Solución"
        En un carpeta a62 creamos los ficheros `index.html`, `index.php` y `Dockerfile`. Este último con el siguiente contenido:
        ```dockerfile
        FROM php:7.4-apache
        # Installing basic tools
        RUN apt update
        RUN apt install -y nano 
        RUN apt install -y git
        # Copy app
        COPY --chown=www-data:www-data index.html /var/www/html
        COPY --chown=www-data:www-data info.php /var/www/html
        # Set working directory
        WORKDIR /var/www/html
        ```
        Estando en dicho directorio jecutamos las siguiente órdenes:
        ```bash
        # Construcción de la imagen
        > docker build -t pedrojoya/a62:1.0 .
        # Listar imágenes
        > docker images
        # Subir la imagen a DockerHub
        > docker login
        > docker push pedrojoya/a62:1.0
        ```

!!! question "Sincronización gitHub/DockerHub"
    === "Enunciado"
        1. Crea un repositorio en GitHub y sincronizarlo con tu cuenta de DockerHub para la creación de una imagen que use el Dockerfile de la actividad anterior.
        2. Una vez sincronizados, haz al menos 3 builds. Valdrá con que cambies el nombre que se muestra en el HTML.
