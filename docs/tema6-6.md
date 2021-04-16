# 6.6.- Conectando GitHub a DockerHub

Podemos hacer que una imagen nuestra en DockerHub esté vinculada a un repositorio git por ejemplo en GitHub. El objetivo de establecer esta asociación es automatizar el proceso de generación de la imagen, de manera que cada vez que se haga un `git push` a dicho repositorio Git, automáticamente se realice la reconstrucción de la imagen en los servidores de DockerHub.

Para ello en DockerHub iremos a la configuración de nuestra cuenta, en el menú `Account Settings`, y en `Linked accounts` (cuentas enlazadas) daremos de alta nuestra cuenta de GitHub (o de cualquier otro gestor de repositorios Git online, como Bitbucket).

Para automatizar el proceso de construcción de la imagen del proyecto seguiremos los siguiente pasos:

1. En GitHub creamos el repositorio para nuestro proyecto.
2. En DockerHub nos vamos a `Repositories` y creamos un repositorio. 
3. En la pestaña `Build` de nuestro repositorio en DockerHub habilitamos la construcción automática de la imagen a partir del repositorio GitHub. Para ello seleccionaremos GitHub e introduciremos el nombre de usuario GitHub y el nombre del repositorio Git de nuestro proyecto en GitHub. Además configuraremos la rama del repositorio Git con el que queremos trabajar (rama `main`).
4. Clonamos el repositorio Git en nuestro equipo y hacemos los cambios oportunos en él.
5. Cada vez que hagamos un `git push` a nuestro repositorio en GitHub, en DockerHub de reconstruirá la imagen a partir del repositorio. El proceso de construcción en DockerHub puede tardar, ya que se introduce en una cola de peticiones.

En el siguiente vídeo podemos ver cómo llevar a cabo estas operaciones:

!!! info "Generación automática de imagen en DockerHub a partir de repositorio en GitHub"
    <iframe width="100%" style="aspect-ratio: 16 / 9;" src="https://www.youtube.com/embed/74jPcmEkV9Y" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    
    Enlace: [https://www.youtube.com/watch?v=74jPcmEkV9Y](https://www.youtube.com/watch?v=74jPcmEkV9Y)

