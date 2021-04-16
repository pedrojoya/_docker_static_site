# 1.3.- Nuestro primer contenedor

## Obtener información sobre la instalación de docker

Para obtener información sobre la versión de docker instalada tenemos la orden `docker version`.

Si queremos información algo más detallada deberemos ejecutar la orden `docker info`.

## Imagen hello-world

Que un programa muestre `Hola mundo` es una convención habitual en el mundo del desarrollo. Muchos cursos de programación acaban su introducción con un primer programa que nos muestra `Hola mundo` al ser ejecutado, ya sea en pantalla, en un navegador web etc...

Normalmente, si conseguimos que ese mensaje se muestre significa que todo está correctamente instalado y que todo funciona como debería  (al menos de momento ;) ).

Docker sigue esta convención y para obtenerlo deberemos ejecutar esta orden:

!!! success "Ejecución de un contenedor hello-world"
    ```bash
    # Ejecutar un contenedor a partir de la imagen hello-world
    > docker run hello-world
    ```

Obtendremos en nuestro terminal una salida similar a la siguiente:

!!! quote "Salida por pantalla de la ejecución del contenedor hello-world"
    ```bash
    Unable to find image 'hello-world:latest' locally
    latest: Pulling from library/hello-world
    b8dfde127a29: Pull complete 
    Digest: sha256:308866a43596e83578c7dfa15e27a73011bdd402185a84c5cd7f32a88b501a24
    Status: Downloaded newer image for hello-world:latest

    Hello from Docker!
    This message shows that your installation appears to be working correctly.

    To generate this message, Docker took the following steps:
    1. The Docker client contacted the Docker daemon.
    2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
        (amd64)
    3. The Docker daemon created a new container from that image which runs the
        executable that produces the output you are currently reading.
    4. The Docker daemon streamed that output to the Docker client, which sent it
        to your terminal.

    To try something more ambitious, you can run an Ubuntu container with:
    $ docker run -it ubuntu bash

    Share images, automate workflows, and more with a free Docker ID:
    https://hub.docker.com/

    For more examples and ideas, visit:
    https://docs.docker.com/get-started/
    ```

Pero, ¿qué es lo que está sucediendo al ejecutar esa orden?. Al ser la primera vez que se ejecuta un contenedor basado en la imagen `hello-word`, dicha imagen es descargada desde el repositorio que se encuentra en el registro que vayamos a utilizar, en nuestro caso DockerHub.

Después crea un contenedor en base a dicha imagen y comienza su ejecución, por lo que se muestra por consola el mensaje de bienvenida.

Debemos tener en cuenta que, tal y como se ha arrancado el contenedor, no podremos volver a arrancarlo ni comunicarlos con él. Más adelante estudiaremos por qué y cómo solucionarlo.

## Imagen docer/whalesay

Además de esta imagen `hello-world`, la comunidad de usuarios de Docker ha elaborado otras imágenes con el mismo objetivo, comprobar que todo está correcto, pero que muestran una apariencia más visualmente atractiva. 

Una de las más curiosas es la imagen `docer/whalesay`:

!!! success "Ejecución de un contenedor de la imagen docker/whalesay"
    ```bash
    # Ejecutar un contenedor a partir de la imagen docker/whalesay
    > docker run docker/whalesay cowsay Hello World
    ```

que genera en nuestro terminal una salida similar a la siguiente:

!!! quote "Salida por pantalla de la ejecución de un contenedor docker/whalesay"
    ```bash
    Unable to find image 'docker/whalesay:latest' locally
    latest: Pulling from docker/whalesay
    Image docker.io/docker/whalesay:latest uses outdated schema1 manifest format. Please upgrade to a schema2 image for better future compatibility. More information at https://docs.docker.com/registry/spec/deprecated-schema-v1/
    e190868d63f8: Pull complete 
    909cd34c6fd7: Pull complete 
    0b9bfabab7c1: Pull complete 
    a3ed95caeb02: Pull complete 
    00bf65475aba: Pull complete 
    c57b6bcc83e3: Pull complete 
    8978f6879e2f: Pull complete 
    8eed3712d2cf: Pull complete 
    Digest: sha256:178598e51a26abbc958b8a2e48825c90bc22e641de3d31e18aaf55f3258ba93b
    Status: Downloaded newer image for docker/whalesay:latest
    _____________ 
    < Hello World >
    ------------- 
        \
        \
          \     
                        ##        .            
                  ## ## ##       ==            
              ## ## ## ##      ===            
          /""""""""""""""""___/ ===        
      ~~~ {~~ ~~~~ ~~~ ~~~~ ~~ ~ /  ===- ~~~   
          \______ o          __/            
            \    \        __/             
              \____\______/   

    ```

## Imagen danielkraic/asciiquarium

Otra imagen muy divertida es `danielkraic/asciiquarium`, que muestra una serie de peces en movimiento dibujados con caracteres ASCII, y que podremos detener usando la combinación de teclas `Ctrl-C`:

!!! success "Ejecución de un contenedor danielkraic/asciiquarium"
    ```bash
    # Ejecutar un contenedor a partir de la imagen danielkraic/asciiquarium
    > docker run -it --rm danielkraic/asciiquarium
    ```



