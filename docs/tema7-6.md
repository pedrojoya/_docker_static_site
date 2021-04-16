# 7.6.- Tareas del tema

!!! question "docker-compose para desarrollo"
    === "Enunciado"
        1. Descarga el fichero `docker-compose.yml` de Bitnami que puedes encontrar en [https://hub.docker.com/r/bitnami/rails/](https://hub.docker.com/r/bitnami/rails/).
        2. Modifica el puerto de la aplicación al que nos podremos conectar desde nuestro host para que sea el puerto `8001`. 
        3. Inicia la aplicación multicapa.
        4. En un navegador visita la página `http://localhost:8001` para demostrar que la aplicación se ha iniciado correctamente.
    === "Solución"
        Creamos una carpeta para la tarea y dentro de ella creamos el fichero `docker-compose.yml`, que modificado queda de la siguiente manera:
        ```yaml
        version: '2'
        services:
          mariadb:
            image: 'docker.io/bitnami/mariadb:10.3-debian-10'
            environment:
              - ALLOW_EMPTY_PASSWORD=yes
          myapp:
            tty: true # Enables debugging capabilities when attached to this container.
            image: docker.io/bitnami/rails:6-debian-10
            environment:
              - DATABASE_HOST=mariadb
              - DATABASE_NAME=my_app_development
            depends_on:
              - mariadb
            ports:
              - 8001:3000
            volumes:
              - .:/app
        ```
        Arrancamos la aplicación con la siguiente orden:
        ```bash
        > docker compose up
        ```

!!! question "Prueba de aplicaciones con docker-compose"
    === "Enunciado"
        1. Descarga el fichero docker-compose.yml de Bitnami que podemos encontrar en la siguiente URL [https://hub.docker.com/r/bitnami/prestashop/](https://hub.docker.com/r/bitnami/prestashop/)
        2. El usuario de prestashop para conectarse a la base de datos deberá ser `pepe` y su contraseña `pepe`. Investiga en la página de Dockerhub cuál es el nombre de las variables de entorno que debes modificar y/o añadir.
        3. Modifica el nombre de la base de datos de prestashop para que se llame `mitienda`. Investiga en la página de Dockerhub cuál es el nombre de las variables de entorno que debes modificar.
        4. Ejecuta la aplicación.
    === "Solución"
        Creamos una carpeta para la tarea y dentro de ella creamos el fichero `docker-compose.yml`, que modificado queda de la siguiente manera:
        ```yaml
        version: '2'
        services:
          mariadb:
            image: 'docker.io/bitnami/mariadb:10.3-debian-10'
            environment:
              - ALLOW_EMPTY_PASSWORD=yes
              - MARIADB_USER=pepe
              - MARIADB_PASSWORD=pepe
              - MARIADB_DATABASE=mitienda
            volumes:
              - 'mariadb_data:/bitnami/mariadb'
          prestashop:
            image: 'docker.io/bitnami/prestashop:1.7-debian-10'
            ports:
              - '80:8080'
              - '8443:8443'
            environment:
              - PRESTASHOP_HOST=localhost
              - PRESTASHOP_DATABASE_HOST=mariadb
              - PRESTASHOP_DATABASE_PORT_NUMBER=3306
              - PRESTASHOP_DATABASE_USER=pepe
              - PRESTASHOP_DATABASE_PASSWORD=pepe
              - PRESTASHOP_DATABASE_NAME=mitienda
            volumes:
              - 'prestashop_data:/bitnami/prestashop'
            depends_on:
              - mariadb
        volumes:
          mariadb_data:
            driver: local
          prestashop_data:
            driver: local   
        ```
        Arrancamos la aplicación con la siguiente orden:
        ```bash
        > docker compose up
        ```

