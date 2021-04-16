# 7.2.- El archivo docker-compose.yml

Como ya hemos comentado en el apartado anterior, el fichero `docker-compose.yml` es un fichero en formato YAML que contiene las instrucciones para crear y configurar los servicios que van a constituir nuestra aplicación o entorno. 

La estructura general del archivo es la siguiente:

!!!info "Estructura general de un docker-compose.yml"
    <figure>
      <img src="../assets/DOCKERCOMPOSE-yml.png">
      <figcaption>Estructura general de un docker-compose.yml</figcaption>
    </figure>

Como vemos, el archivo está formado por una serie de secciones, cada una de las cuales tiene multitud de opciones. Las más importantes son las que se muestran en la imagen: 

* `version`, para indicar la versión de la sintaxis en la que está escrito el archivo `docker-compose.yml` (la versión más reciente es la 3).
* `services`, para declarar los contenedores.
* `volumes`, para declarar los volúmenes. 
* `networks`, para indicar las redes. 

Vamos a estudiar la opciones necesarias para llevara a cabo las acciones más habituales para nuestros casos de usos. Lo primero que tenemos que aprender es cómo **asociar contenedores a servicios**. 

Dentro de la sección `services` definiremos una subsección con el nombre de cada servicio que vaya a ser representado por un contenedor. 

Dentro de cada subsección de un contenedor, usaremos `image` para indicar la imagen que debe usarse y `container_name` para indicar el nombre que debe tomar el contenedor. Por ejemplo:

```yaml
# Versión del formato
version: '3'
  # Sección de Servicios
  services:
    # Declaración del servicio miapache
    miapache:
      # El contenedor usará la imagen httpd de DockerHub
      image: httpd
      # Nombre del contenedor
      container_name: web
```

Si queremos que la imagen que use el servicio (contenedor) sea construida a partir de un archivo *Dockerfile*, en vez de usar `image:` usaremos `build: rutaDockerfile`. La ruta del archivo `Dockerfile` se indica normalmente relativa al directorio en el que se encuentra el fichero `docker-compose.yml`. Por ejemplo:

```yaml
version: '3'
  services:
    miapache:
      # El contenedor usará la imagen resultante de la construcción del Dockerfile
      # dentro de la subcarpeta miapache.
      build: ./miapache
      container_name: web
```

Si la construcción es más compleja, porque queremos especificar el contexto de construcción, el nombre del archivo `Dockerfile` o argumentos para la construcción, existen elementos adicionales. Por ejemplo:

```yaml
version: "3.9"
services:
  webapp:
    build:
      # Contexto de construcción
      context: ./dir
      # Nombre del archivo Dockerfile
      dockerfile: Dockerfile-alternate
      # Argumentos para la construcción
      args:
        # Nombre argumento: valor
        buildno: 1
```

Si necesitamos indicar los valores de determinadas variables de entorno para alguno de nuestros contenedores, dentro de la subsección de `services` correspondiente a dicho contenedor usaremos `environment` para definir una subsección con las variables de entorno, dentro de la que definiremos un par `variable: valor` por cada variable. Por ejemplo:

```yaml
version: '3'
  services:
    datos:
      image: mariadb
      container_name: bd
      # Variables de entorno
      environment:    
        MYSQL_ROOT_PASSWORD: 123456    
        MYSQL_DATABASE: test    
        MYSQL_USER: pepe    
        MYSQL_PASSWORD: pepe
```

Para establecer la redirección de puertos en nuestros contenedores usaremos dentro de la subsección correspondiente al contenedor la orden `ports`, que creará una subsección en la que indicar la redirección de puertos. Tendrá en su interior una línea `- puertoEquipo:puertoContenedor`, por cada puerto que queramos redireccionar. Por ejemplo:

```yaml
version: '3'
  services:
    miapache:
      image: httpd
      container_name: web
      # Redirección de puertos
      ports:
        - 8080:80
```

Para persistir los datos usando *bind mount*, dentro de la subsección correspondiente al servicio usaremos `volumes` para crear una subsección en la que indicar la persistencia de datos del contenedor, tanto si se trata de *bind mounts* como si se trata de volúmenes. Contendrá en su interior una línea `- "carpetaEquipo:carpetaContendor"` por cada *bind mount*. Por ejemplo:

```yaml
version: '3'
  services:
    miapache:
      image: httpd
      container_name: web
      # Persistencia de datos
      volumes:
		    # bind mount de la carpeta src de mi equipo en la carpeta /app del contenedor.
        - "./src:/app"
        # Alternativamente podemos usar la notación siguiente
        - type: bind
          source: "./src"
          target: /app
```

Si queremos usar un volumen en vez de un *bind mount*, entonces contendrá una línea `- "nombreVolumen:carpetaContenedor` por cada volumen que queramos que use el contenedor. Además, deberemos definir una nueva sección `volumes`, al mismo nivel que `services`, que contenga una línea `nombreVolumen: driver` por cada volumen que queramos definir. Por ejemplo:

```yaml
version: '3'
  services:
    datos:
      image: mariadb
      container_name: bd
      # Persistencia
      volumes:
        # En el volumen datosapp montado en /var/lib/mysql
        - "datosapp:/var/lib/mysql"
        # Alternativamente podemos usar la notación siguiente
        - type: volume
          src: datosapp
          target: "var/lib/mysql"
  # Sección de volúmenes (al mismo nivel que services)
  volumes:
    # Volumen llamada datosapp con driver local
    datosapp: local
```

Para definir el nombre de *host* de nuestro contenedor podemos usar dentro de la subsección correspondiente al servicio `hostname: valor`.

Por defecto al lanzar la aplicación con `docker-compose up`, se creará una red de tipo `bridge` a la que se conectarán todos los contenedores (servicios) de la aplicación, y que han sido declarados en el archivo `docker-compose.yml`. 

Si nuestro servicio debe usar redes concretas definidas por el usuario, entonces dentro de la subsección correspondiente al servicio usaremos `networks` para crear una subsección en la que indicar las redes que debe usar el contenedor. Contendrá en su interior una línea `- nombreRed` por cada red que queramos que use. Además, deberemos definir una nueva sección `networks`, al mismo nivel que `services`, que contenga una subsección con el nombre de cada red que queramos definir. Por ejemplo:

```yaml
version: '3'
  services:
    miapache:
      image: httpd
      container_name: web
      # Nombre de host
      hostname: web
      # Redes personalizadas que usa
      networks:
        # Usa la red llamada mired
        - mired      
  # Sección de redes (al mismo nivel que services)
  networks:
    # Se define la red llamada mired
    mired:
      # Tipo de red
      driver: bridge
      # Gateway y subred
      ipam:
        driver: default
        config:
          - subnet: 172.20.0.0/16
```

Si queremos establecer un orden de arranque de los distintos servicios de la aplicación, dentro de la subsección correspondiente a un servicio usaremos `depend_on` para definir una subsección en la que indicar los nombres de los servicios de los que depende dicho servicio. Contendrá en su interior una línea `- nombreServicio` por cada servicio del que dependa para poder ser arrancado, de manera que antes de arrancar éste se deban arrancar aquellos de los que depende. Por ejemplo:

```yaml
version: '3'
  services:
    miapache:
      image: httpd
      # ...
      # Debe arrancarse después del servicio datos
      depends_on:
        - datos
    datos:
      image: mariadb
      # ...
```

Veamos ahora un ejemplo completo de archivo `docker-compose.yml` que arranca dos contenedores, uno con un WordPress y otro con el gestor de bases de datos MariaDB, que debe arrancarse antes que el de Wordpress:

```yaml
version: '3'
services:

  #-------------------------------------------------------------------------
  # SERVICIO SERVIDOR WEB (php:7.4-apache con mysqli y código WP descargado
  #-------------------------------------------------------------------------
  web:
    # IMAGEN USADA
    image: jperjim398/miwp
    # NOMBRE QUE LE DAMOS AL CONTENEDOR
    container_name: web
    # REDIRECCIÓN DE PUERTOS
    ports:
      - 8181:80     
    # SERVICIOS QUE TIENEN QUE ARRANCAR ANTES DE ARRANCAR ESTE
    depends_on:
     - datos
    # REDES A USAR
    networks:
      - ejemplo
  
  #--------------------------------------------------
  # SERVICIO SERVIDOR DE BASE DE DATOS MARIADB
  #--------------------------------------------------

  # NOMBRE DEL SERVICIO
  datos:
    # IMAGEN USADA
    image: mariadb
    # NOMBRE QUE LE DAMOS AL CONTENEDOR
    container_name: bd
    # LISTA DE VARIABLES DE ENTORNO CON SUS VALORES
    environment:
      MYSQL_ROOT_PASSWORD: 123456
      MYSQL_DATABASE: wordpress
      MYSQL_USER: pepe
      MYSQL_PASSWORD: pepe
    # REDIRECCIÓN DE PUERTOS
    ports:
      - 3316:3306
    # VOLÚMENES A USAR POR EL CONTENEDOR
    volumes:
      # DOCKER VOLUME LLAMADO data
      - data:/var/lib/mysql
    # REDES A USAR
    networks:
      # RED mired
      - mired

# DEFINICIÓN DE VOLÚMENES DOCKER A USAR POR LOS SERVICIOS (OPCIONAL)
volumes:
  data:
    # TIPO DE DRIVER
    driver: local

# DEFINICIÓN DE LAS REDES A USAR POR LOS SERVICIOS (OPCIONAL)
networks:
  # NOMBRE DE LA RED
  mired:
    # DRIVER DE LA RED
    driver: bridge
    ipam:
      driver: default
      config:         
        - subnet: 172.20.0.0/16
```

Podemos encontrar una referencia completa sobre el fichero `docker-compose.yml` en la documentación oficial: [https://docs.docker.com/compose/compose-file/compose-file-v3/](https://docs.docker.com/compose/compose-file/compose-file-v3/)

Existe una extensión para manejar este tipo de ficheros en Visual Studio Code de una manera más cómoda: [https://marketplace.visualstudio.com/items?itemName=redhat.vscode-yaml](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-yaml)