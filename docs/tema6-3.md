# 6.3.- Crear una imagen a partir de un fichero Dockerfile

En el apartado anterior hemos visto cómo crear y distribuir nuestras imágenes personalizadas partiendo de un contenedor. Esta forma suele ser la preferida cuando empezamos, porque es fácil si tenemos ciertos conocimientos de sistemas y porque no hace falta muchos conocimientos sobre docker y su entorno. 

Sin embargo, este tipo de flujo de trabajo aunque fácil, tiene unos inconvenientes importantes. En primer lugar, no se puede reproducir la imagen de manera automática. Si perdemos la imagen generada tendremos que recordar toda la secuencia de órdenes que ejecutamos sobre la imagen base cuando la creamos, antes de hacer `docker commit`.

Otra inconveniente es que una vez creada la imagen no podemos cambiar la imagen de base. Así, si ha habido alguna actualización en la imagen base, por ejemplo para soluciona problemas de seguridad o bugs, tendremos que volver a crear un nuevo contenedor basado en esta nueva versión de la imagen base, y ejecutar de nuevo toda la secuencia de órdenes de personalización que realizamos la vez anterior.

Frente a estos inconvenientes, el enfoque recomendado para la creación de imágenes personalizadas es utilizar los conocidos como ficheros *Dockerfile*, que nos permiten definir una imagen personalizada de forma declarativa, y usar dicho declaración para la construcción de nuevas imágenes.

!!!info "Creación de imágenes con Dockerfile"
    <figure>
      <img src="../assets/BuildDockerfile.png" style="max-width: 600px">
      <figcaption>Creación de imágenes con Dockerfile</figcaption>
    </figure>

Así, para construir una imagen usando esta técnica necesitaremos dos cosas: 

* Un fichero `Dockerfile` en el que incluyamos la declaración de los pasos que queremos que se sigan en la construcción de la imagen.
* Un contexto de construcción, que normalmente corresponderá al directorio en el que se encuentre el fichero `Dockerfile` y desde el que se ejecutarán las órdenes indicadas dentro del mismo. Por este motivo, normalmente crearemos un directorio para nuestro proyecto e incluiremos en él el fichero `Dockerfile` y los ficheros que queramos que se copien en la imagen.

En el siguiente apartado estudiaremos las principales órdenes que podemos especificar dentro del fichero `Dockerfile` y para qué sirve cada una de ellas.

Para construir la imagen a partir de dicho archivo `Dockerfile` usaremos la orden `docker build pathContexto`, que recibe el *path* del contexto de construcción, en el que se encontrará el fichero `Dockerfile` con el que debe trabajar. 

La forma más sencilla de usar esta orden es ejecutarla desde el directorio que va actuar como contexto de ejecución, y en en el que se encontrará el archivo `Dockerfile`. Por ejemplo:

!!! success "Construcción de una imagen con Dockerfile en el directorio actual"
    ```bash
    # Construye una imagen sin nombre ni versión con Dockerfile en el directorio actual
    > docker build .
    ```

En el ejemplo anterior se crea una imagen si nombre ni versión. Sin embargo, lo habitual será que queramos especificar un nombre para nuestra imagen, e incluso una versión, para lo que debemos usar el *flag* `-t` o `--tag`. No debemos olvidar que si queremos que la imagen construida sea distribuida mediante DockerHub debemos poner como prefijo de la imagen nuestro nombre de usuario de DockerHub seguido de `/`. Por ejemplo: 

!!! success "Construcción de una imagen con nombre y versión en el directorio actual"
    ```bash
    # Construcción de una imagen con nombre y versión en el directorio actual
    > docker build -t  usuarioDockerHub/nombre_imagen:1.0 .
    ```

En realidad, la orden `docker build` permite que el fichero `Dockerfile` se encuentre en un repositorio externo, por ejemplo en GitHub, ya podemos pasarle una url en vez de un *path*.

!!!info "Estructura de la orden docker build"
    <figure>
      <img src="../assets/EstructuraDockerBuild.png" style="max-width: 600px">
      <figcaption>Estructura de la orden docker build</figcaption>
    </figure>

Veamos un ejemplo, en el que el contexto de construcción se encuentra en una rama concreta de un repositorio Git en GitHub:

!!! success "Construcción de una imagen especificando como contexto de construcción un repositorio Git en GitHub"
    ```bash
    # Construcción de una imagen especificando como contexto de construcción un repositorio en GitHub
    > docker build -t usuarioDockerHub/nombre_imagen:1.1 https://github.com/usariogithub/nombre_repo.git
    ```

Si el fichero `Dockerfile` no se encuentra en el contexto de construcción, sino en algún otro *path*, por ejemplo porque se llame de otra manera o esté en otro directorio distinto, deberemos usar el *flag* `-f` para indicar dónde se encuentra. Por ejemplo:

!!! success "Construcción de una imagen especificando como contexto de construcción un repositorio Git en GitHub"
    ```bash
    # Construcción de una imagen con fichero Dockerfile en path distinto al contexto de construcción
    > docker build -t  usuarioDockerHub/nombre_imagen:1.0 -f /home/usuario/DockerProject/Dockerfile .
    ```

Un aspecto muy importante del proceso de construcción de una imagen, es que todas las imágenes o capas intermedias que sean creadas durante la construcción serán cacheadas, de manera que si posteriormente volvemos a reconstruir la imagen, solo de deberán construir las capas intermedias a partir de la primera que se haya visto modificada. De esta manera se consigue que el proceso de construcción sea mucho más rápido.

Si queremos forzar a que se reconstruyan todas las imágenes o capas intermedias y no se emplee la caché, podemos usar el *flag* `--no-cache`. Por ejemplo:

!!! success "Construcción de una imagen sin usar las capas cacheadas de construcciones anteriores"
    ```bash
    # Construcción de una imagen sin usar las capas cacheadas de construcciones anteriores
    > docker build --no-cache -t  usuarioDockerHub/nombre_imagen:1.0 .
    ```

Si, una vez construida la imagen, queremos distribuirla a través de DockerHub, deberemos conectarnos a DockerHub usando `docker login`, y después subir nuestra imagen con la orden `docker push`, de forma similar a como vimos en el apartado anterior.

No debemos olvidar que si queremos que la imagen construida sea distribuida mediante DockerHub debemos poner como prefijo de la imagen nuestro nombre de usuario de DockerHub seguido de `/`.

La siguiente imagen muestra el flujo de trabajo habitual para la creación y distribución de imágenes a partir de fichero `Dockerfile`:

!!!info "Proceso de creación y distribución usando docker build"
    <figure>
      <img src="../assets/ProcesoDockerBuild.png" style="max-width: 600px">
      <figcaption>Proceso de creación y distribución usando docker build</figcaption>
    </figure>

Aunque el proceso de construcción del *Dockerfile* es costoso al principio, tiene una serie de ventajas claras. Por un lado, podremos reproducir la imagen fácilmente, ya que en el fichero *Dockerfile* tenemos todas y cada una de las órdenes necesarias para la construcción de la imagen. 

Si además ese fichero `Dockfile` está guardado en un sistema de control de versiones como Git, podremos no sólo reproducir la imagen, sino asociar los cambios en el fichero `Dockerfile` a los cambios en las versiones de las imágenes creadas.

Por otra parte, si queremos cambiar la imagen de base, tan solo tendremos que modificar la imagen base declarada en el fichero `Dockerfile`, tal y como explicaremos en el siguiente apartado.
