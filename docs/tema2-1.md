# 2.1.- Ejecución de contenedores

## Intro

Tras instalar docker y hacer la comprobación de que todo está correcto lo que nos pide el cuerpo es empezar a ejecutar contenedores de todo tipo. Querremos probar todo tipo de sistemas operativos y todo tipo de servicios. Para ello necesitaremos las imágenes de todo aquello que queremos probar. 

Eso lo podemos conseguir con la orden `docker pull nombreimagen:version`, que descargará desde el repositorio una imagen con la versión indicada o la última versión (*latest*) si no indicamos versión.

La orden fundamental para ejecutar contenedores que es `docker run nombreimagen`, cuya función principal es poner en ejecución contenedores en base a una imagen de referencia que le indicaremos. Si no tenemos descargada la imagen indicado, se descargará automáticamente antes de ejecutar el contenedor.

Para buscar las imágenes concretas se recomienda usar el buscador de [Docker Hub (https://hub.docker.com/)](https://hub.docker.com/).

Esta orden `docker run` tiene una sintaxis sencilla pero multitud de opciones, de las que solo explicaremos algunas. Su estructura general es la siguiente:

!!!info "Estructura general docker run"
    <figure>
      <img src="../assets/estructuradockerrun.png" style="max-width: 600px">
      <figcaption>Estructura general docker run</figcaption>
    </figure>

## Flags principales de docker run

En este curso no vamos a poder ver todas las opciones (*flags*) que tiene `docker run` ya que son alrededor de 100, pero iremos introduciendo apartado a apartado las que yo considero que son más importantes para nuestro trabajo diario:

- `-d` o `--detach` para ejecutar un contenedor (normalmente porque tenga un servicio) en background.
- `-e` o `--env` para establecer variables de entorno en la ejecución del contenedor.
- `-h` o `--hostname` para establecer el nombre de red parar el contenedor.
- `--help` para obtener ayuda de las opciones de docker.
- `-`o `--interactive` para mantener la STDIN abierta en el contenedor.
- `--ip` si quiero darle una ip concreta al contenedor.
- `--name` para darle nombre al contenedor.
- `--net` o `--network` para conectar el contenedor a una red determinada.
- `-p` o `--publish` para conectar puertos del contenedor con los de nuestro host.
- `--restart` que permite reiniciar un contenedor si este se "cae" por cualquier motivo.
- `--rm` que destruye el contenedor al pararlo.
- `-t`o `--tty` para que el contenedor que vamos a ejecutar nos permita un acceso a un terminal para poder ejecutar órdenes en él.
- `-u`o `--user` para establecer el usuario con el que vamos a ejecutar el contenedor.
- `-v`o `--volume` para montar un bind mount o un volumen en nuestro contenedor.
- `-w`o `--workdir` para establecer el directorio de trabajo en un contenedor.

!!! tip "Contenedores de servicios"
    Si queremos arrancar un contenedor de servicios se recomienda usar el *flag* `-d`, para que se ejecute en segundo plano y no se nos quede la consola dentro del contenedor.

A continuación vamos a ver algunos ejemplos básicos. 

## Ejecución interactiva de Ubuntu con acceso root

El primero de ellos consiste en ejecutar un contenedor de Ubuntu 18.04 de forma interactiva de manera que se nos muestre un shell en el que ejecutar comandos como root:

!!! success "Ubuntu 18.04 interactivo en shell con root"
    ```bash
    # Descargar una imagen de manera previa
    > docker pull ubuntu:18.04
    # Crear un contenedor de ubuntu:18.04 y tener acceso a un shell en él. 
    > docker run -it ubuntu:18.04 /bin/bashroot@ef2bea1d6cb1:/#
    ```

!!! tip "Arranque interactivo"
    Cuando arranquemos una imagen de un sistema operativo y queramos tener interactividad con ella se recomienda usar los *flags* `-i`y `-t`, o de forma resumida `-it`.

Al crear el contenedor se nos da un acceso a un *shell* del mismo. Es importante destacar que estamos accediendo como *root* que tiene unas implicaciones de seguridad que trataremos más adelante en el curso. 

Si estando en el terminar ejecutamos `exit`, saldremos de él, y el contenedor será parado, aunque al haberlo ejecutado de forma interactiva, podremos volver más adelante a iniciarlo y acceder a él.

## Ejecutar una orden al arrancar el sistema operativo

En el siguiente ejemplo arrancamos un contenedor del sistema operativo CentOS en su versión 18.04 y ejecutamos directamente en él la orden necesario para listar el contenido del directorio `/bin`:

!!! success "CentOS 18:04 sin shell ejecutando orden concreta"
    ```bash
    # Crear un contenedor de centOs:18.04 y listar el contendido de la carpeta /bin 
    > docker run centOs:18.04 ls /bin 
    ```

que produce la siguiente salida por pantalla

!!! quote "Salida por consola"
    ```bash
    # Salida por consola
    etc lib lost+found mnt proc run srv tmp var dev home lib64 media opt root sbin sys usr
    ```

Al crear el contenedor se ejecuta la orden `ls /bin` y posteriormente el contenedor pasa a estar parado. Y ya no podremos acceder a él, ya que no lo hemos arrancado interactivamente.

Por defecto la orden ejecutada al arrancar el contenedor es mostrar el shell, pero dado que hemos indicado que se haga `ls /bin`, no se muestra el shell sino que tan solo se ejecuta dicha orden.

## Ejecutar un servidor Apache en primer plano

En el siguiente ejemplo ejecutamos un servidor Apacha (*httpd*) en primer plano, por lo que nos muestran los *logs* del mismo.

!!! success "Servidor Apache en foreground"
    ```bash
    # Crear un contenedor de httpd (Servidor Apache) 
    > docker run httpd
    ```

que produce la siguiente salida por consola

!!! quote "Salida por consola"
    ```bash
    # Salida por consola
    AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.6. Set the 'ServerName' directive globally to suppress this message AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.6. Set the 'ServerName' directive globally to suppress this message [Mon Dec 07 10:01:52.670809 2020] [mpm_event:notice] [pid 1:tid 140412541457536] AH00489: Apache/2.4.46 (Unix) configured -- resuming normal operations [Mon Dec 07 10:01:52.670973 2020] [core:notice] [pid 1:tid 140412541457536] AH00094: Command line: 'httpd -D FOREGROUND' 
    ```

Se crea un servidor Web Apache 2.4 en la ip mostrada y se nos muestra por pantalla el *log* de dicho servicio. Si quisiéramos arrancarlo y que se ejecute en segundo plano, deberemos añadir el *flag* `-d` a `docker run`.

## Ejecutar un sistema operativo estableciendo el directorio de trabajo

En el siguiente ejemplo ejecutamos un sistema operativo Debian 9 de forma interactiva, establecemos el directorio de trabajo al directorio `/etc` y ejecutamos directamente la orden `ls`.

!!! success "Debian 9 con directorio de trabajo y orden inicial"
    ```bash
    # Crear un contenedor de debian 9 y mostrar el contenido de una carpeta establecida con el parámetro -w 
    > docker run -it -w /etc debian:9 ls
    ```

Al crear el contenedor se ejecuta la orden `ls` desde el directorio `/etc`, ya que éste es el directorio de trabajo. Posteriormente el contenedor pasa a estar parado, y ya no podremos acceder a él, debido a que, aunque hemos indicado que se arranque de forma interactiva, al especificar la orden `ls` ya no estamos ejecutando la orden por defecto que es mostrar el *shell*.
