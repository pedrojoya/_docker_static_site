# 6.2.- Crear una imagen a partir de un contenedor existente

## Creación de una imagen

La primera forma de personalizar las imágenes y distribuirlas es a partir de un contenedor existente. Así, lanzaremos un contenedor con la imagen base, y ejecutaremos en él las acciones de instalación y configuración necesarias para personalizarla.

Una vez finalizada la personalización, podremos salir del contenedor y generar nuestra propia imagen mediante la orden `docker commit nombreContenedor nombreNuevaImagen`.

A la hora de indicar el nombre de la nueva imagen, debemos tener en cuenta que si vamos a querer subir nuestra imagen a DockerHub debemos agregar al nombre de la imagen un prefijo con nuestro nombre de usuario en DockerHub y una `/`, es decir, que el nombre de la imagen debe ser algo parecido `usuarioDockerHub/nombreImagen`. Por ejemplo:

!!! success "Creación de una nueva imagen a partir de un contenedor"
    ```bash
    # Crea una nueva imagen usuarioDockerHub/ubuntu20netutils con la versión latest, a partir del contenedor ejemplo.
    > docker commit ejemplo usuarioDockerHub/ubuntu20netutils
    ```

Además del nombre de la imagen, podremos indicar un *tag* que haga referencia a la versión. Veamos un ejemplo:

!!! success "Creación de una nueva imagen a partir de un contenedor indicando versión"
    ```bash
    # Crea la versión 1.0 de la imagen usuarioDockerHub/ubuntu20netutils, a partir del contenedor ejemplo
    > docker commit ejemplo usuarioDockerHub/ubuntu20netutils:1.0
    ```

Si queremos agregar un mensaje indicativo de los cambios introducido respecto a la imagen original, podemos usar el *flag* `-m` o `--message`. También podemos agregar el *flag* `-a` o `--author` para nombrar al autor de la imagen. Finalmente, si queremos pausar el contenedor para generar la imagen porque esté en ejecución, podemos usar el *flag* `-p` o `--pause`. Veamos un ejemplo completo:

!!! success "Creación de una nueva imagen a partir de un contenedor indicando mensaje, autor y pausando el contenedor"
    ```bash
    # Crear una nueva imagen a partir de un contenedor indicando mensaje, autor y pausando el contenedor
    > docker commit -a "Baldomero Llegate Ligero" -m "Versión con Nmap" -p ejemplo usuarioDockerHub/ubuntu20netutils:1.1
    ```

## Distribución de una imagen en fichero .tar

Una vez creada la nueva imagen debemos pensar en cómo la vamos a distribuir, y ahí tenemos dos opciones: 

* Distribuir la imagen como un archivo que podamos enviar al destinatario, por ejemplo por correo o mediante un *pendrive*. 
* Subir la imagen a DockerHub, para que cualquiera pueda descargarla.

Veamos en primer lugar el flujo de trabajo necesario para distribuir una imagen personalizada a través de un archivo:

!!!info "Distribución de una imagen mediante fichero .tar"
    <figure>
      <img src="../assets/DockerSave_DockerLoad.png" style="max-width: 600px">
      <figcaption>Distribución de una imagen mediante fichero .tar</figcaption>
    </figure>

Como vemos, para generar el archivo de la imagen distribuirle deberemos usar la orden `docker save imagen`, que producirá un archivo `.tar` con la imagen. Por ejemplo:

!!! success "Generar el archivo de una imagen"
    ```bash
    # Guardar la imagen ubuntu20netutils:1.1 en el fichero u20v1.1.tar
    > docker save usuarioDockerHub/ubuntu20netutils:1.1 > u20v1.1.tar
    ```

También podemos indicar el fichero usando el *flag* `-o` o `--output`, indicando el nombre del archivo `.tar`. Por ejemplo:

!!! success "Generar el archivo de una imagen"
    ```bash
    # Guardar la imagen ubuntu20netutils:1.1 en el fichero u20v1.1.tar
    > docker save --output u20v1.1.tar usuarioDockerHub/ubuntu20netutils:1.1
    ```

Una vez recibido el archivo, el destinatario podrá cargarlo en su propia instalación de docker, para que lo que usará la orden `docker load`. Para indicar el fichero desde el que cargar la imagen usaremos el *flag* `-i` o `--input`. Por ejemplo:

!!! success "Generar el archivo de una imagen"
    ```bash
    # Carga la imagen con nombre imagen.tar
    > docker load --input imagen.tar
    ```

## Distribución de una imagen en DockerHub

Si vamos a distribuir nuestra imagen a través de DockerHub el flujo de trabajo es distinto:

!!!info "Distribución de una imagen en DockerHub"
    <figure>
      <img src="../assets/DockerCommitDockerPush.png" style="max-width: 600px">
      <figcaption>Distribución de una imagen en DockerHub</figcaption>
    </figure>

Para poder subir nuestra imagen a DockerHub deberemos estar conectados a DockerHub. Para conectarnos usaremos la orden `docker login`, que nos solicitará nombre de usuario y contraseña en DockerHub, si es que no estamos conectados ya. 

!!! success "Conectarnos a DockerHub"
    ```bash
    # Autentificación en DockerHub
    > docker login
    ```

Una vez conectados, para realizar la subida usaremos la orden `docker push nombreImagen[:version]`. Debemos tener en cuenta que el nombre de la imagen que queramos subir debe tener el formato `nombreUsuarioDockerHub/nombreImagen`. La versión o *tag* es opcional, y si no se indica se usará la versión `latest`.

!!! success "Conectarnos a DockerHub"
    ```bash
    # Subir una imagen ubuntu20netutils:1.1 a DockerHub
    > docker push usuarioDockerHub/ubuntu20netutils:1.1
    ```

Si no queremos que se muestre información sobre el proceso de subida podemos usar el *flag* `-q` o `--quiet`. Por ejemplo:

!!! success "Conectarnos a DockerHub"
    ```bash
    # Subir una imagen ubuntu20netutils:1.1 a DockerHub suprimiendo la información del proceso de subida
    > docker push -q usuarioDockerHub/ubuntu20netutils:1.1
    ```

También tenemos disponible el *flag* `-a` o `--all` para indicar que se suban todas la versiones de una imagen. Por ejemplo:

!!! success "Conectarnos a DockerHub"
    ```bash
    # Subir a DockerHub todas las versiones (tags) de la imagen ubuntu20netutils (--all-tags / -a)
    > docker push -a usuarioDockerHub/ubuntu20netutils
    ```

Cuando queramos desconectarnos de DockerHub ejecutaremos la orden `docker logout`. Por ejemplo:

!!! success "Conectarnos a DockerHub"
    ```bash
    # Desconectarse de DockerHub
    > docker logout
    ```

!!! info "Instalación de Docker en Windows"
    <iframe width="100%" style="aspect-ratio: 16 / 9;" src="https://www.youtube.com/embed/eWkqN9U5yJU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    
    Enlace: [https://www.youtube.com/watch?v=eWkqN9U5yJU](https://www.youtube.com/watch?v=eWkqN9U5yJU)

## Exportación e importación

Existe una tercera opción de distribución, consistente en utilizar la orden `docker export` para exportar la imagen y después usar la orden `docker import`en el destinatario para importarla. 

Sin embargo, no se recomienda usar esta opción, ya que se limita a copiar el sistema de ficheros sin tener en cuenta la información de las imágenes de las que deriva el contenedor (capas, imagen de origen, autor etc..) y además obviará los volúmenes o *bind mounts* que hayamos montado.
