# 4.5.- Usos principales de volúmenes y bind mounts

Como hemos visto en los apartados anteriores, con el uso de volúmenes y *bind mounts* conseguimos que los datos de los contenedores persistan y que sobrevivan incluso si los contenedores desaparecen. 

En este apartado vamos a concretar y desarrollar un poco más los casos de uso más habituales de estos mecanismos.

El primero de ellos es la **realización de copias de seguridad de contenidos, tanto para código para datos**. 

En el siguiente ejemplo vamos a usar un volumen para preservar los datos de un servidor de bases de datos MariaDB, y comprobaremos que aunque eliminemos el contenedor dichos datos son preservados en el volumen, y pueden ser usados posteriormente por otro contenedor MariaDB que lancemos posteriormente.

!!! success "Conservar datos de un servidor MariaDB"
    ```bash
    # Creamos un volumen llamado DATA
    > docker create volume DATA
    # Lanzamos un contenedor de MariaDB que use el volumen para almacenar los datos de las bases de datos.
    > docker run -d --name bd1 -p 3306:3306 --mount type=volume,src=DATA,dst=/var/lib/mysql -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=test mariadb
    # Realizamos operaciones sobre la base de datos
    # ...
    # Forzamos la parada y eliminación del contenedor. 
    # Los datos siguen estando en el volumen.
    > docker rm -f bd1
    # Se arranca un nuevo contenedor y se le monta el mismo volumen.
    # No indicamos variables de entorno, la base de datos ya está creada.
    > docker run -d --name bd2 -p 3306:3306 --mount type=volume,src=DATA,dst=/var/lib/mysql mariadb
    # La base de datos, tablas y datos sigue existiendo.
    # Realizamos operaciones sobre ella
    # ...
    ```

Ahora vamos a ver cómo podemos usar un *bind mount* para hacer una copia de seguridad de los datos de un servidor Apache

!!! success "Conservar archivos fuente en servidor Apache"
    ```bash
    # Lanzamos un servidor Apache y le montamos como carpeta raíz una carpeta de nuestro equipo llamada web
    > docker run -d -p 8787:80 --name apache -v /home/usuario/web:/usr/local/apache2/htdocs httpd
    # Si haremos forzamos la eliminación del contenedor, la carpeta web seguirá estando disponible
    > docker rm -f apache
    # Podemos montarla en un nuevo contenedor
    > docker run -d -p 8787:80 --name apache2 -v /home/usuario/web:/usr/local/apache2/htdocs httpd
    # Y seguirá teniendo sus archivos
    ```

En el siguiente vídeo puede ver de forma resumida el uso de volúmenes y *bind mounts* para este fin:

!!! info "Copias de seguridad de datos y código con volúmenes y bind mounts"
    <iframe width="100%" style="aspect-ratio: 16 / 9;" src="https://www.youtube.com/embed/rV9mEsPQJW0" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    
    Enlace: [https://www.youtube.com/watch?v=rV9mEsPQJW0](https://www.youtube.com/watch?v=rV9mEsPQJW0)

Otro de los casos de uso más habituales de volúmenes y *bind mounts* es la posiblidad de compartir contenidos entre contenedores. 

A modo de ejemplo vamos a hacer que dos servicios php con apache compartan una carpeta `web` que en ambos vamos a montar en ambos mediante un *bind mount*:

!!! success "Compartir carpetas en contenedores mediante bind mount"
    ```bash
    # Lanzamos un servidor php Apache y le montamos como carpeta raíz una carpeta de nuestro equipo llamada web
    > docker run -d -p 8181:80 --name web1 --mount type=bind,src=/home/usuario/web:/var/www/html php:7.4-apache
    # Lanzamos otro servidor php Apache y le montamos como carpeta raíz una carpeta de nuestro equipo llamada web
    > docker run -d -p 8282:80 --name web2 --mount type=bind,src=/home/usuario/web:/var/www/html php:7.4-apache
    # Cualquier cambio en la carpeta que se produzca desde fuera o desde cualquiera de los dos contenedores será visible en ambos.
    ```

En el siguiente vídeo puede ver de forma resumida su uso con dicho objetivo:

!!! info "Comparticion de datos entre contenedores con volúmenes y bind mounts"
    <iframe width="100%" style="aspect-ratio: 16 / 9;" src="https://www.youtube.com/embed/jIYQZIbSeng" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    
    Enlace: [https://www.youtube.com/watch?v=jIYQZIbSeng](https://www.youtube.com/watch?v=jIYQZIbSeng)

En algunas ocasiones queremos probar nuestra aplicación con nuevas versiones de las librerías que usamos para saber si todo sigue funcionando correctamente. Para ello podemos usar un contenedor con las nuevas versiones de las librerías y montar un volumen o un *bind mount* con nuestra aplicación, y comprobar si sigue funcionando correctamente. El siguiente vídeo muestra como podemos usar los volúmenes o los *bind mount* para comprobar la compatibilidad entre versiones:

!!! info "Comprobación de compatibilidad con nuevas versiones con volúmenes y bind mounts"
    <iframe width="100%" style="aspect-ratio: 16 / 9;" src="https://www.youtube.com/embed/qdURCnir3dY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    
    Enlace: [https://www.youtube.com/watch?v=qdURCnir3dY](https://www.youtube.com/watch?v=qdURCnir3dY)
