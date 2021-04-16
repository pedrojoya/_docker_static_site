# 8.1.- Seguridad en las imágenes

A lo largo del curso no hemos tenido en cuenta para nada una aspecto tan importante como la segurida. De hecho, hemos realizado operaciones potencialmente peligrosas, como por ejemplo:

* Descargar imágenes de terceros sin mirar quién era el autor.
* Entrar como root en el contenedor sin ningún tipo de problema para instalar y configurar.
* Ejecutar y publicar servicios como root.
* Incluir usuarios y contraseñas en *Dockerfiles* y ficheros *docker-compose*.

Si bien es cierto que este curso está orientado a la docencia y al desarrollo, si en algún momento queremos desplegar nuestro código en forma de contenedor en un orquestador como Kubernetes o Docker Swarm tendremos que tomar ciertas medidas de seguridad respecto las imágenes, el proceso de construcción y el arranque de contenedores. 

A lo largo de todos los capítulos hemos descargado multitud de imágenes desde DockerHub. Las imágenes son el elemento fundamental sobre el que hemos desarrollado todo el curso, pero si no tenemos cuidado pueden representar un posible riesgo de seguridad, ya que normalmente se tratan de imágenes realizadas por terceros y, por lo tanto, no tenemos ningún control sobre ellas.

Este aspecto no es tan importante si las usamos únicamente para  desarrollo, pero adquiere mucha más relevancia si tenemos pensado llevar esos contenedores a producción.

Para intentar minimizar los riesgos en el uso de imágenes hay que seguir una serie de pautas generales.

La primera de ellas es **intentar siempre  usar una imagen de un usuario verificado o una imagen publicada por la propia empresa Docker**. Aunque no es una garantía al 100%, el hecho de descargar este tipo de imágenes ayuda a reducir los posibles riesgos que puedan existir. Para obtener este tipo de imágenes, cuando hacemos una búsqueda debemos activar los filtros adecuados:

!!!info "Búsqueda de imágenes oficiales o verificadas"
    <figure>
      <img src="../assets/oficiales.png">
      <figcaption>Búsqueda de imágenes oficiales o verificadas</figcaption>
    </figure>

Un segundo paso es **verificar la integridad de la imagen** que nos descargamos. Docker utiliza un mecanismo de firma digital llamado DCT (*Docker's Content Trust*) para verificar la integridad y la autoría de las imágenes que nos descargamos desde DockerHub. Por defecto el mecanismo DCT está deshabilitado  y cuando hacemos `docker pull` podemos descargar imágenes sin verificar ni la integridad ni la autoría.

Si habilitamos DCT  solo podremos hacer `docker pull`, `docker run` o `docker build` con imágenes *trusted* (en las que podemos confiar), salvo que explícitamente hagamos alguna excepción añadiendo el flag `---disable-content-trust` a la ejecución de dichas órdenes.

Si queremos habilitar dicho mecanismo tendremos que establecer la variable de entorno `DOCKER_CONTENT_TRUST=1`. A partir de entonces no podremos descargarme imágenes no firmadas, incluso aunque sean nuestros.

En Linux esto se realiza de la siguiente manera:

!!! success "Habilitar mecanismo de content trust en Linux"
    ```bash
    # En el archivo .bashrc añadios la variable de entorno
    export DOCKER_CONTENT_TRUST=1
    # Recargamos el .bashrc
    > source /home/miusuario/.bashrc
    ```

El procedimiento en Windows y Mac para establecer variables de entorno es diferente.

Si no queremos crear la variable de entorno podemos añadir DOCKER_CONTENT_TRUST=1 delante de la orden `docker` para conseguir el mismo efecto.

Si queremos firmar digitalmente nuestras imágenes tendremos que seguir los siguientes pasos:

1. Generar la parejas de claves público/privada. La privada se coloca en `~/.docker/trust/private`
2. Compartir nuestra clave pública con el servidor Notary asociado a DockerHub. Notary es una herramienta que me permite publicar contenidos confiables asegurando la integridad de dicho contenido y la autoría del mismo. Si se utilizara otro registro diferente a DockerHub el hecho de tener un servicio Notary es requisito para poder firmar las imágenes en ese registro. Tenemos que generar una clave pública para cada repositorio (colección de versiones de la misma imagen).
3. Firmar la imagen. Este proceso firma y a la vez hace un *push*.

Veámoslo en código:

!!! success "Firma de una imagen"
    ```bash
    # 1. Generar la pareja de claves
    >  docker trust key generate miclave.pub
    # 2. Compartir la clave pública con el servidor Notary en el repositorio para las imágenes del respositorio usuario/nombreimagen. El nombre del firmante es miclave y miclave.pub es la clave pública generada en el proceso anterior. Recordad que un mismo repositorio puede contener varias versiones (TAGS) de una misma imagen.
    > docker trust signer add --key miclave.pub miclave usuario/nombrerepositorio
    # 3. Firmar la imagen
    > docker trust sign usuario/nombreimagen[:tag]
    ```

Si queremos ver las firmas almacenadas para un determinado repositorio deberemos ejecutar la siguiente orden `docker trust inspect`:

!!! success "Consulta de firmas de un repositorio"
    ```bash
    # Obtener las firmantes y las claves de un repositorio
    >  docker trust inspect --pretty nombreusuario/nombrerepositorio
    ```

que produce una salida similar a la siguiente:

!!!info "Salida por consola de docker trust inspect"
    <figure>
      <img src="../assets/salida_inspect.png">
      <figcaption>Salida por consola de docker trust inspect</figcaption>
    </figure>

Para conocer más opciones de la orden `docker trust` visite el siguiente enlace a la referencia: [https://docs.docker.com/engine/reference/commandline/trust/](https://docs.docker.com/engine/reference/commandline/trust/)

Otro buen consejo de seguridad es **intentar usar imágenes que tengan lo mínimo**. ¿Qué sentido tiene usar un sistema operativo entero si solo queremos instalar una aplicación o un servicio?. Cada aplicación nueva que instalamos añade un potencial punto de inseguridad y nos obliga a estar atentos para actualizar a una nueva versión cuando surja una vulnerabilidad.

En este sentido, si estamos construyendo nuestra propia imagen deberemos usar una imagen de base que sea de tamaño mínimo. Un ejemplo es bitnami/minideb. 

También es cierto que a lo largo del curso hemos utilizado imágenes de terceros con un único servicio ya instalado. Normalmente estas imágenes ya están pulidas y no tienen mas que lo necesario.

Otra buena práctica relacionada con el tamaño de la imagen es usar el fichero `.dockerignore` en nuestro proceso de construcción de la imagen. Así evitamos que tenga más tamaño del necesario.

Otra buena medida de seguridad es **restringir los privilegios de los datos a los que pueden acceder los contenedores**. Cuando hablamos de restricción de privilegios nos referimos a los permisos que el contenedor va a tener en el acceso a los volúmenes montados, *bind mounts* o a ciertos directorios del propio contenedor.

Si vamos a usar un volumen o un *bind mount* en el que no queremos que se pueda escribir desde el contenedor podemos hacerlo añadiendo la opción `readonly` al *flag* `--mount`. Por ejemplo:

!!! success "Hacer un bind mount de solo lectura"
    ```bash
    # Realizar bind mount de solo lectura
    >  docker run -it --mount type=bind,src=/home/pekechis/pruebaPHP,dst=/var/www/html,readonly -p 8686:80 php:7.4-apache
    ```

Si queremos evitar que desde el contenedor se pueda escribir en su propio sistema de archivos, tendremos que usar el *flag* `--read-only` de la orden `docker run`. Por ejemplo:

!!! success "Lanzar contenedor con sistema de archivos de solo lectura"
    ```bash
    # Lanzar un contenedor en modo solo lectura.
    >  docker run -it --read-only ubuntu:20.04 /bin/bash
    ```

Una última medida de seguridad a la hora de crear nuestras propias imágenes es **pagar por el servicio de DockerHub *Vulnerability Scanner*** o utilizar herramientas que sirvan para ese mismo propósito, disponible para cuentas de pago DockerHub.

Este servicio escanea las imágenes para encontrar vulnerabilidades, tanto a nivel de sistema operativo como a nivel de las posibles aplicaciones que tengamos instaladas en ellas. 

Para activar este servicio tenemos que cambiar de plan y aplicarlo a cada una de las imágenes que queramos escanear desde la sección de administración del repositorio de la imagen.

!!!info "Habilitar el escaneo de vulnerabilidades"
    <figure>
      <img src="../assets/vulnerability.png">
      <figcaption>Habilitar el escaneo de vulnerabilidades</figcaption>
    </figure>

