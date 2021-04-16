# 3.2.- Listado de imágenes

Para mostrar por pantalla la lista de imágenes que tenemos descargadas podemos usar la orden `docker images`, o su equivalente `docker image ls`:

!!! success "Listado de las imágenes descargadas"
    ```bash
    # Listado de imágenes descargadas
    > docker images
    ```

Como resultado se nos mostrará una tabla, como la siguiente:

!!! quote "Salida por consola"
    ```bash
    REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
    ubuntu                  20.04               8e428cff54c8        6 days ago          72.9MB
    mysql                   latest              2fb283157d3c        12 days ago         449MB
    php                     7.3-apache          4c91f3690297        2 weeks ago         411MB
    ```

en la que para cada imagen se nos indicará el nombre de la imagen en el repositorio (*repository*), la versión de la imagen (*tag*), donde *latest* significa la última disponible, el identificador de la imagen (*image id*), cuánto hace que se creó la imagen (*created*) y su tamaño (*size*).
