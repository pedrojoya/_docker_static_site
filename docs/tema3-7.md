# 3.7.- Tareas del tema

!!! question "Descarga, inspección y eliminación de imágenes"
    === "Enunciado"
        1. Descarga la imagen `ubuntu:20.04`.
        2. Lista las imágenes para confirmar que se ha descargado. Captura la pantalla.
        3. Obtén toda la información de esa imagen y vuélcala en el fichero info.txt.
        4. Crea un contenedor para esa imagen, llamado `modulo3`.
        5. Lista los contenedores disponibles, para desmostrar que el contenedor se ha creado (en ejecución o no). Captura la pantalla.
        6. Intenta borrar la imagen `ubuntu:20.04`. Captura el resultado del intento. ¿Has podido?. ¿Por qué?.
        7. Realiza las operaciones necesarias para poder borrar la imagen. Captura un pantallazo de cada una de ellas.

    === "Solución"
        ```bash
        # Descargar la imagen
        > docker pull ubuntu:20.04
        # Obtener información básica sobre la imagen
        > docker images ubuntu:20.04
        # Volcar toda la información sobre la imagen en el archivo info.txt
        > docker image inspect ubuntu:20.04 > info.txt
        # Crear contenedor
        > docker run --name modulo3 ubuntu:20.04
        # Listar contenedores activos o parados
        > docker ps -a
        # Tratar de borrar la imagen
        > docker rmi ubuntu:20.04
        # No se puede borrar la imagen porque está en uso por el contenedor
        # Para poder eliminar la imagen borramos antes el contenedor
        > docker rm modulo3
        > docker rmi ubuntu:20.04
        ```
