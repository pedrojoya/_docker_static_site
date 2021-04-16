# 1.4.- ¿Qué es Docker Hub?

## Intro

En el apartado anterior, cuando ejecutamos `docker run hello-world` dijimos que se la imagen `hello-world` era descargada antes de ejecutarla creándose un contenedor. ¿Pero desde dónde se descarga exactamente esa imagen?

Las imágenes se descargan desde un registro de imágenes, que corresponde a una especie de **almacén en la nube** donde los usuarios pueden, entre otras cosas crear, probar, almacenar y distribuir imágenes. Por defecto cuando instalamos Docker el registro que se usa es [DockerHub (https://hub.docker.com/)](https://hub.docker.com/), que además del registro de imágenes proporciona muchas más funcionalidades.

Aunque sería posible crear nuestro propio registro de imágenes y utilizarlo para crear nuestros contenedores, el uso de DockerHub tiene una serie de ventajas:

- Tiene una gran variedad de imágenes disponibles. La gran mayoría son públicas y gratuitas.
- Permite crear y distribuir imágenes de manera muy sencilla. No olvidemos que es el repositorio por defecto para Docker.
- Permite crear organizaciones para poder crear equipos y añadir posteriormente miembros, con sus respectivos permisos.
- Dispone de un interfaz web de fácil utilización.

Todas estas características son de especial interés para la docencia en los ciclos formativos de informática. Algunas de sus aplicaciones prácticas a la docencia son:

- Creación de una organización para los profesores de cada IES.
- Distribución a los alumnos de las imágenes creadas a medida por el profesorado. De tal manera que el entorno es el mismo tanto para profesor como para el alumno. Esto nos ahorra muchos problemas posteriormente.
- Presentación de aplicaciones por parte de los alumnos. Si los alumnos son capaces de crear y subir sus propias imágenes los profesores podrán utilizarlas de manera inmediata sin tener que esperar al manual de instalación o despliegue.

## Uso de DockerHub

En el siguiente vídeo vamos a ver como:

- Crear una cuenta en DockerHub
- Crear una organización en DockerHub
- Crear un equipo en DockerHub y darle una serie de permisos.
- Hacer Login/Logout en DockerHub desde consola.

!!! info "Uso de DockerHub"
    <iframe width="100%" style="aspect-ratio: 16 / 9;" src="https://www.youtube.com/embed/wePGnp_jeeI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    
    Enlace: [https://www.youtube.com/watch?v=wePGnp_jeeI](https://www.youtube.com/watch?v=wePGnp_jeeI)

## Conexión a DockerHub desde consola

Para hacer login y logout en DockerHub desde consola haríamos:

!!! success "Login y logout en DockerHub"
    ```bash
    # Para hacer login
    > docker login
    # Para hacer logout
    > docker logout
    ```
