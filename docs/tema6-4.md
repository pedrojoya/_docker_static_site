# 6.4.- Definición de un fichero Dockerfile

En el apartado anterior hemos descrito como construir nuevos contenedores mediante la orden `docker build` y los ficheros `Dockerfile`, pero ¿qué son y qué contienen esos ficheros declarativos para permitir construir imágenes a partir de ellos?.

Si queremos hacer una definición más o menos formal de estos ficheros podríamos decir que un fichero *Dockerfile* es un conjunto de instrucciones que serán ejecutadas de forma secuencial para construir una nueva imagen docker.

En el proceso de construcción de la nueva imagen, **cada una de estas instrucciones crea una nueva capa o imagen intermedia, que será cacheada** por si se debe usar de nuevo más adelante.

Esta estructura en forma de capas para las imágenes ya la estudiamos brevemente cuando vimos la orden `docker pull`.

Las capas son de solo lectura y por tanto inmutables, de manera que si modificamos alguna instrucción de nuestro archivo `Dockerfile` deberemos volver a reconstruir la imagen. 

Si modificamos una instrucción de nuestro `Dockerfile` y volvemos a construir la imagen, veremos que si no ha habido cambios en una determinada capa, ésta no será recreada, sino que se usará la versión cacheada, ahorrándonos tiempo de construcción. Sin embargo, la capa correspondiente a la intrucción que ha cambiado y todas las capas de las instrucciones posteriores sí que deberán ser reconstruídas, dado que se han producido cambios. 

Por otra parte, si construimos varias imágenes que usen una misma capa, para dicha imagen intermedia se podrá reusar la existente en caché, en vez de volver a generarla, ahorrándonos tiempo y espacio en disco.

Cuando lanzamos un contenedor basado en una imagen, se crea un capa adicional mutable, la llamada capa del contenedor, en la que se escribirán los archivos generados o modificados por el contenedor, como por ejemplos los archivos de *log*, archivos temporales, o archivos creados por el usuario en el contenedor. 

Sin embargo, debemos tener en cuenta que el tiempo de vida de dicha capa adicional mutable o capa del contenedor está asociado al tiempo de vida del propio contenedor, de manera que la capa será eliminada en cuanto el contenedor termine su ejecución.

Todos los contenedores que lancemos que estén basados en la misma imagen compartirán todas las imágenes intermedias de dicha imagen.

Dado que las capas de una imagen son inmutables, si incluimos en una imagen ficheros fuente, cuando los modifiquemos y queramos incorporar los cambios a la imagen, tendremos que reconstruirla. Sin embargo, gracias a la caché de imágenes, dicho proceso será optimizado, porque solo se recrearán las capas correspondientes a las instrucciones del `Dockerfile` en la que se cargan los ficheros fuente, y a las instrucciones posteriores. De ahí que la instrucción de copia de ficheros fuente se intente colocar lo más abajo posible dentro del `Dockerfile`. 

La estructura general de un fichero *Dockerfile* es la siguiente:

!!!info "Estructura de un fichero Dockerfile"
    <figure>
      <img src="../assets/DOCKERFILE.png" style="max-width: 600px">
      <figcaption>Estructura de un fichero Dockerfile</figcaption>
    </figure>

La idea general es que pondremos más arriba en el ficheor `Dockerfile` las órdenes correspondientes a los elementos que menos cambien, y más abajo lo que más cambie. . El objetivo en que en sucesivas construcciones se pueda reutilizar la caché lo máximo posible, teniendo en cuenta que cuando cambia una capa se debe recrear esa capa y todas las correspondientes a las ordenes siguientes.

Veamos poco a poco cuáles son las órdenes más comunes que nos vamos a encontrar en un archivo `Dockerfile`.

## Imagen base

La primera de ellas es la orden `FROM imagen[:version]`, que sirve para especificar la imagen base sobre la que se va a construir nuestra imagen personalizada. Si no indicamos la versión se usará `latest`. En el siguiente ejemplo indicamos que la imagen base sea la imagen `php` en su versión `7.4-apache`:

!!! success "Especificar imagen base"
    ```dockerfile
    FROM php:7.4-apache
    ```

Normalmente la orden `FROM` se usa como primera orden de nuestro `Dockerfile`, y debe corresponder a una imagen de un sistema operativo o una imagen creada sobre una imagen de un sistema operativo. 

El sistema de ficheros de nuestra imagen se inicializa al sistema de ficheros de esta imagen base.

## Ejecución de órdenes

Otra orden muy utilizada es `RUN orden param1, param2, ...`, que ejecuta la orden especificada sobre el sistema de ficheros de la capa anterior, y como resultado de ello se crea una nueva capa o imagen intermedia. 

Dado que la orden indicada se va a ejecutar durante el proceso de construcción de la imagen, deberemos hacer que dicha orden no trate de interactuar con el usuario. Por ejemplo, si queremos ejecutar la orden `apt install` se recomienda usar `-y` para indicar que no se pregunte al usuario.

Como ya hemos comentado en alguna ocasiones, las imágenes base de sistemas operativos suelen venir con el contenido mínimo, por lo que será muy habitual que al construir nuestra imagen personalizada tengamos que que ejectuar `apt update` para obtener los repositorios:

!!! success "Ejecución de órdenes"
    ```dockerfile
    FROM php:7.4-apache
    RUN apt update 
    ```

En un fichero `Dockerfile` podemos encontrarnos varias órdenes `RUN`. Así, en el siguiente ejemplo se installa git después de obtener los repositorios:

!!! success "Ejecución de órdenes"
    ```dockerfile
    FROM php:7.4-apache
    RUN apt update
    RUN apt install -y git  
    ```

En el ejemplo anterior cabría preguntarse si no es más óptimo usar una única orden `RUN` que haga varias cosas. Por ejemplo:

!!! success "Ejecución de órdenes"
    ```dockerfile
    FROM php:7.4-apache
    RUN apt update && apt install -y git  
    ```

La respuesta es que depende de la etapa en la que nos encontremos
en nuestro proyecto. Si probando a instalar y desinstalar varias librerías, es más óptimo tener varias órden `RUN`, una para cada librería. El motivo es que al ser independientes, cada una de ellas generará una capa que será cacheada, de manera que si eliminamos la orden `RUN` correspondiente a una librería que ya no vamos a usar, sólo se deberán volver a generar las imágenes de las instrucciones posteriores. Sin embargo, si optamos por usar una única orden `RUN`, se generará y cacheará una única capa para dicha orden, de manera que al eliminar una librería se deberán ejecutar de nuevo todos los comandos indicados en el `RUN`, dado que la versión en la caché ya no será válida.

Sin embargo, si el conjunto de librerías que debemos cargar es estable y ya no se va a ver modificado, entonces es más óptimo tener una única orden `RUN` que cargue todas estas librerías, y que genere una única capa o imagen intermedia que sea cacheada, ahorrándonos espacio en disco.

## Argumentos de construcción

Si queremos que las órdenes de nuestro fichero `Dockerfile` hagan uso de algún argumento que podamos especificar en el proceso de construcción, para que dicho proceso sea configurable desde fuera, debemos usar la orden `ARG nombreArgumento[=valorInicial]`, donde la asignación del valor inicial es opcional.

Por ejemplo, si queremos definir un argumento que corresponda al puerto que debe usar la aplicación, en nuestro archivo `Dockerfile` definiremos el siguiente argumento con el valor `8000` por defecto:

!!! success "Definir argumento de construcción con valor inicial"
    ```dockerfile
    FROM python:3.8.7
    ARG APP_PORT=8000
    ```

Durante el proceso de construcción, en la orden `docker build` usaremos el *flag* `--build-arg` para indicar el valor que queremos asociar a un determinado argumento. Así, para indicar un valor para el argumento `APP_PORT` definido en el `Dockerfile` del ejemplo anterior haríamos:

!!! success "Construcción de una imagen proporcionando valor para un argumento"
    ```bash
    # Construcción de una imagen proporcionando un valor para un argumento
    > docker build --build-arg APP_PORT=8002 -t usuarioDockerHub/nombre_imagen:1.0 .
    ```

El argumento definido en nuestro archivo `Dockerfile` podremos usarlo en las órdenes de éste con la sintaxis `$nombreArgumento` o `${nombreArgumento}`. Así, el argumento `APP_PORT` podemos usarlo en una orden `RUN` de la siguiente manera:

!!! success "Uso de un argumento de construcción"
    ```dockerfile
    FROM python:3.8.7
    ARG APP_PORT=8000
    # ...
    RUN sed -i "s/\$1/$APP_PORT/" startapp.sh
    ```

Un aspecto importante es que los argumentos **no se pueden usar en las órdenes `ENTRYPOINT` ni `CMD`**, que vamos a estudiar dentro de poco.

## Establecer directorio de trabajo en la imagen

En algunas ocasiones merece la pena establecer el directorio de trabajo en el sistema de archivos de la imagen, para que las operaciones posteriores que realicemos sobre ella se realicen sobre dicho directorio.

Para establecer el directorio de trabajo de la imagen usaremos en nuestro fichero `Dockerfile` la orden `WORKDIR directorioImagen`. 

En el siguiente ejemplo establecemos el directorio de trabajo de nuestra imagen apache a `WORKDIR /usr/local/apache/htdocs`:

!!! success "Establecer directorio de trabajo"
    ```dockerfile
    FROM php:7.4-apache
    # ...
    WORKDIR /var/www/html
    # ...
    ```

Las órdenes posteriores al establecimiento del directorio de trabajo se ejecutarán desde dichos directorios. Así, en el siguiente ejemplo el archivo `startapp.sh` se busca en el directorio `/app` del sistema de archivos de la imagen, a la hora de darle el permiso de ejecución. 

!!! success "Ejecución de órdenes desde el directorio de trabajo"
    ```dockerfile
    FROM python:3.8.7
    # ...
    WORKDIR /app
    RUN chmod +x startapp.sh
    ```

Un fichero `Dockerfile` puede contener más de una orden `WORKDIR`, porque nos interese cambiar de directorio de trabajo para ejecutar las órdenes posteriores. 

Así en el siguiente ejemplo, primero establecemos el directorio de trabajo como uno temporal para realizar una serie de operaciones auxiliares y más adelante volvemos a establecer el directorio de trabajo pero ahora al que queramos que finalmente tengan nuestros contenedores:

!!! success "Cambio de directorio de trabajo"
    ```dockerfile
    FROM python:3.9.2-alpine3.13
    # Directorio de trabajo para generación
    WORKDIR /tmp
    # Se realiza la generación
    RUN ...
    # Directorio de trabajo real para los contenedores
    WORKDIR /docs
    # ...
    ```

## Copiar archivos desde el host a la imagen

Cuando construyamos una imagen que corresponda a una aplicación será necesario que copiemos los archivos ejecutables de la aplicación desde  nuestro equipo anfitrión *host* hacia el sistema de archivos de la imagen, para que cuando creamos un contenedor a partir de ella se pueda ejecutar la aplicación.

Para llevar a cabo este proceso de copia tenemos disponible la orden `COPY [--chown=<usuario>:<grupo>] src dest`. Los archivos de origen deben encontrarse en el directorio de nuestro *host* correspondiente al contexto de construcción, del que ya hablamos anteriormente, ya sea un directorio o un repositorio Git, por ejemplo disponible en GitHub. La carpeta de destino es creada si no exite.

Veamos un ejemplo:

!!! success "Copia de archivos desde el host a la imagen"
    ```dockerfile
    FROM python:3.8.7
    COPY djangoapp /app
    COPY ./startapp.sh /app/
    WORKDIR /app
    RUN chmod +x startapp.sh
    # ...
    ```

En el ejemplo anterior primero se copia el subdirectorio `djangoapp` del directorio del *host* correspondiente al contexto de construcción, en el sistema de archivos de la imagen, con la ruta `/app`. De esta manera el contenido de `djangoapp` de nuestro equipo pasará a estar disponible en el directorio `/app` de la imagen, y por tanto de los contenedores que creamos a partir de ella. 

Posteriormente, se copia el archivo `startapp.sh` del *host* en esa misma carpeta. Se establece esa misma carpeta como diretorio de trabajo y finalmente se le concede al archivo `startapp.sh` el permiso de ejecución.

!!! success "Uso de archivos copiados"
    ```dockerfile
    FROM python:3.8.7
    COPY djangoapp /app
    COPY ./startapp.sh /app/
    WORKDIR /app
    RUN chmod +x startapp.sh
    # ...
    ```

Un aspecto muy importante de la orden `COPY` es que si ha habido algún cambio en alguno de los archivos que se copian en la imagen respecto a la último construcción, la capa correspondiente en caché será invalidada, lo que implicará que si volvemos a construir la imagen tenga que ser regenerada y también todas las capas correspondientes a las órdenes posteriores.

Otra orden muy parecida a `COPY` que podemos usar en nuestros archivos `Dockerfile` es la orden `ADD`, que funciona de manera similar pero proporciona funcionalidades adicionales.

Así, en la orden `ADD` podremos especificar un archivo comprimido, que será descomprimido a la hora de ser agregado al destino. Por otra parte, `ADD` nos permite que le pasemos la URL desde la que queremos que se obtenga el archivo que se desea copiar al destino.

## Establecer variables de entorno

Para establecer el valor de una determinada variable de entorno en los contenedores que usen la imagen que vamos a construir podemos usar la orden `ENV variableEntorno=valor`. Por ejemplo:

!!! success "Establecer variables de entorno"
    ```dockerfile
    FROM python:3.9.2-alpine3.13
    ENV PACKAGES=/usr/local/lib/python3.9/site-packages
    ENV PYTHONDONTWRITEBYTECODE=1
    # ...
    ```

Posteriormente podemos usar el valor de una variable de entorno en una orden posterior, mediante la sintaxis `${variableEntorno}`. Así, el siguiente ejemplo se usa la variable de entorno `PACKAGES` en una oden `RUN` posterior:

!!! success "Uso de variables de entorno"
    ```dockerfile
    FROM python:3.9.2-alpine3.13
    ENV PACKAGES=/usr/local/lib/python3.9/site-packages
    # ...
    RUN \
      apk upgrade --update-cache -a && \
        for theme in mkdocs readthedocs; do \
          rm -rf ${PACKAGES}/mkdocs/themes/$theme; \
          ln -s \
            ${PACKAGES}/material \
            ${PACKAGES}/mkdocs/themes/$theme; \
        done 
    ```

Debemos tener en cuenta que **no podemos usar las variables de entorno en la orden `ENTRYPOINT` ni `CMD`**.

## Establecer el ejecutable de arranque de un contenedor basado en la imagen

En muchas ocasiones es necesario que indiquemos para la imagen cuál es el ejecutable que debe ejecutarse inicialmente cuando se lance un contenedor basado en dicha imagen. Para ello debemos usar en nuestro archivo `Dockerfile` la orden `ENTRYPOINT <command>` o  `ENTRYPOINT ["executable","param1","param2"]`. Normalmente dicho punto de entrada corresponderá a algún fichero que habremos copiado anteriormente en la imagen. 

Así, en el siguiente ejemplo, se establece el ejecutable `startapp.sh` como programa que debe arrancarse automáticamente al lanzar un contenedor basado en esta imagen:

!!! success "Ejecutable a ejecutar al arrancar contenedor"
    ```dockerfile
    FROM python:3.8.7
    # ...
    COPY djangoapp /app
    COPY ./startapp.sh /app/
    WORKDIR /app
    RUN chmod +x startapp.sh
    # ...
    ENTRYPOINT ["/app/startapp.sh"]
    ```

En algunas ocasiones queremos proporcionar parámetros adicionales al ejecutable que debe ejecutarse al arrancar un contenedor basado en nuestra imagen. Para esos casos podemos usar la orden `CMD ["param1","param2", ...]`.

En el siguiente ejemplo, además de establecer el *entry point*, se indica los parámetros que éste debe recibir:

!!! success "Ejecutable a ejecutar al arrancar contenedor"
    ```dockerfile
    FROM python:3.9.2-alpine3.13
    # ...
    ENTRYPOINT ["mkdocs"]
    CMD ["serve", "--dev-addr=0.0.0.0:8000"]
    ```

En realidad podríamos haber definido la ejecución inicial que se debe realizar en un contenedor que use nuestra imagen usando solo la orden `ENTRYPOINT`:

!!! success "Ejecutable a ejecutar al arrancar contenedor"
    ```dockerfile
    FROM python:3.9.2-alpine3.13
    # ...
    ENTRYPOINT ["mkdocs", "serve", "--dev-addr=0.0.0.0:8000"]
    ```

 o usando solo la orden `CMD`:

!!! success "Ejecutable a ejecutar al arrancar contenedor"
    ```dockerfile
    FROM python:3.9.2-alpine3.13
    # ...
    CMD ["mkdocs", "serve", "--dev-addr=0.0.0.0:8000"]
    ``` 

aunque la opción recomendada es usar `ENTRYPOINT` para indicar el ejecutable y `CMD` para indicar los parámetros de éste.

Recordemos que **no podemos usar ni variables de entorno ni argumentos en el valor de `ENTRYPOINT` ni en de `CMD`**.

Debemos tener en cuenta que aunque nosotros hayamos establecido el *entry point* o el *cmd* en la construcción de la imagen, cuando se lanza un contenedor basado en dicha imagen se puede especificar explícitamente el *entry point* deseado sobrescribiendo el establecido en la construcción de la imagen. Para ello en la orden `docker run` se puede usar el *flag* `--entrypoint cadena`.

Si el `Dockerfile` no especifica un `CMD`o un `ENTRYPOINT` y tampoco se indica el *flag* `--entrypoint` al lanzar el contenedor, la imagen generada hereda dichas órdenes de la imagen base, lo que es de especial relevancia cuando usamos contenedores correspondientes a servicios.

## Otras órdenes

A continuación se describen otras órdenes que podemos incluir en nuestro archivo `Dockerfile`:

- `LABEL`: Para añadir metadatos a la imagen mediante clave=valor. Ejemplo: `LABEL company=iesalixar`.
- `USER`: Para especificar (por nombre o por por UID/GID) el usuario de trabajo para todas las órdenes `RUN`, `CMD` y `ENTRYPOINT` posteriores. Por ejemplo: `USER jenkins` o `USER 1001:10001`. Es útil cuando queremos ejecutar determinadas órdenes con los permisos de un determinado usuario. Podemos tener varias órdenes `USER`, de manera que podamos cambiar de nuevo a otro usuario para ejecutar las órdenes posteriores.
- `EXPOSE`: Nos da información acerca de qué puertos tendrá abiertos el contenedor cuando se cree uno en base a la imagen que estamos creando. Es meramente informativo. Por ejemplo: `EXPOSE 80`.

Podemos encontrar una referencia completa sobre los ficheros *dockerfile* visite el siguiente enlace: [https://docs.docker.com/engine/reference/builder/](https://docs.docker.com/engine/reference/builder/)

## Ejemplos de ficheros Dockerfile 

Veamos un ejemplo de `Dockerfile` de un proyecto Tomcat:

!!! success "Dockerfile para un proyecto Tomcat"
    ```dockerfile
    FROM tomcat:9.0.39-jdk11
    # Installing basic tools
    RUN apt update && apt install -y nano && apt install -y  vim 
    # Enable manager app, host manager app and docs apss
    RUN mv /usr/local/tomcat/webapps.dist/* /usr/local/tomcat/webapps
    RUN rm -rf /usr/local/tomcat/webapps.dist
    # Copying my tomcat-users.xml to the container
    # TO-BE-DONE user password as a docker build arg
    COPY mytomcat-users.xml /usr/local/tomcat/conf/tomcat-users.xml
    # Context Modifying from all default apps
    COPY mycontext.xml /usr/local/tomcat/webapps/host-manager/META-INF/context.xml
    COPY mycontext.xml /usr/local/tomcat/webapps/manager/META-INF/context.xml
    # COPY THE APP
    COPY webapp.war /usr/local/tomcat/webapps/webapp.war
    ```

Y ahora un `Dockerfile` para un servidor Apache + PHP + WordPress:

!!! success "Dockerfile para un servidor Apache + PHP + WordPress"
    ```dockerfile
    FROM php:7.4-apache
    # Installing basic tools
    RUN apt update && apt install -y git 
    # Set working directory
    WORKDIR /var/www/html
    # Clone WordPress repository
    RUN git clone https://github.com/WordPress/WordPress.git .
    # Change owner of the working directory
    RUN chown -R www-data:www-data /var/www/html
    # Install mysqli
    RUN docker-php-ext-install mysqli
    ```

Veamos un fichero `Dockerfile` para un proyecto Django en Python:

!!! success "Dockerfile para un proyecto Django en Python"
    ```dockerfile
    FROM python:3.8.7
    # Set arguments
    ARG DEBUG_PORT=5687
    ARG APP_PORT=8000
    # Copy app
    COPY djangoapp /app
    COPY ./startapp.sh /app/
    # Set working directory
    WORKDIR /app
    # Run app installing code
    RUN chmod +x startapp.sh
    RUN ls /app
    RUN pip install -r requirements.txt
    RUN sed -i "s/\$1/$APP_PORT/" startapp.sh
    RUN sed -i "s/\$2/$DEBUG_PORT/" startapp.sh
    RUN more startapp.sh
    # Set entrypoint
    ENTRYPOINT ["/app/startapp.sh"]
    ```

## Buenas prácticas para ficheros Dockerfile

Los Dockerfiles que hemos usado anteriormente son Dockerfiles de ejemplo. Nos hemos centrado en ellos desde el punto de vista del desarrollo y de la docencia y por lo tanto no hemos tenido en cuenta dos factores importantes en relación a las imágenes que estamos construyendo: ni el tamaño de la imagen resultante, ni el tiempo empleado en el proceso de construcción (*build*).

Estos dos factores son vitales cuando se requiere desplegar lo más rápido posible las **últimas versiones de nuestro código por motivos de seguridad, comerciales etc...**

Construir imágenes optimizadas **no es algo fácil**, requiere una experiencia que el autor no posee y queda un poco fuera del alcance de este curso. No obstante considero que en el siguiente enlace podéis encontrar una series de pautas y ejemplos que para aquellos que estáis interesados os puede resultar de utilidad: [https://github.com/juan131/dockerfile-best-practices](https://github.com/juan131/dockerfile-best-practices)
