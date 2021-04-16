# 8.5.- Tareas del tema

!!! question "Habilitar protección para imágenes sin firmar"
    === "Enunciado"
        1. Descarga la imagen https://hub.docker.com/r/testinf/holamundohtml.
        2. Elimina dicha imagen.
        3. Habilita correctamente la variable de entorno DOCKER_CONTENT_TRUST para descargar sólo imágenes firmadas (*trusted*).
        4. Intenta descargar de nuevo la imagen y comprueba que nos da un mensaje de error.
        5. Fuerza la descarga de esa imagen de manera forzosa
    === "Solución"
        ```bash
        # Descargar la imagen
        > docker pull testinf/holamundohtml
        # Eliminar la imagen
        > docker image rm testinf/holamundohtml
        # Hablitar solo imágenes firmadas
        > echo "export DOCKER_CONTENT_TRUST=1" >> ~/.zshrc
        # Intentar descargar la imagen
        > docker pull testinf/holamundohtml
        # Falla porque la imagen no está firmada
        # Forzar la descarga aunque no esté firmada
        > docker pull --disable-content-trust testinf/holamundohtml
        ```

!!! question "Firma de imágenes con docker trust"
    Firma la imagen generada en la tarea "Creación de imágenes a partir de ficheros Dockerfile" del tema 6.