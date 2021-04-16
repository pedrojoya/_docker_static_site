# 3.3.- Obtener información de las imágenes

Una vez tenemos ya las imágenes descargadas es muy interesante conocerlas al máximo para poder utilizarlas. Para ello tenemos dos fuentes principales. 

Por un lado la página de la imagen en DockerHub, que suele recoger sobre todo información relativa a aspectos como una descripción de la aplicación o servicio que contiene la imagen, una lista de versiones *tags* disponibles, variables de entorno interesantes y cómo ejecutar la imagen. 

Por otro lado, podemos usar la salida de las órdenes `docker image inspect` y `docker inspect`, que nos muestran información más detallada sobre las características de la imagen. Por ejemplo:

!!! success "Obtener información detallada sobre la imagen mysql en su versión 8.0.22"
    ```bash
    # Dos formas de obtener información de la imagen mysql:8.0.22
    > docker image inspect mysql:8.0.22
    > docker inspect mysql:8.0.22
    ```

Esta orden nos retorna un objeto JSON en el que podemos encontrar información detallada sobre la imagen, como el id y el checksum, los puertos abiertos, la arquitectura y el sistema operativo de la imagen, su tamaño, los volúmenes, el *entry point* (que es lo que se ejecuta al hacer `docker run`), las capas, etc.

Adicionalmente, podemos formatear la salida usando [Go Templates](https://golang.org/pkg/text/template/) y el flag `--format` o `-f`. Por ejemplo:

!!! success "Muestra la arquitectura y el sistema operativo de la imagen msql en su versión 8.0.22"
    ```bash
    # Mostrar la arquitectura y el sistema
    > docker inspect --format '{{.Architecture}} es la arquitectura y el SO es {{.Os}}' mysql:8.0.22
    ```

que muestra la siguiente salida por consola:

!!! quote "Salida por consola"
    ```bash
    # Salida por consola
    amd64 es la arquitectura y el SO es linux
    ```

En el siguiente ejemplo se muestra la lista de puertos expuestos por el servicio:

!!! success "Lista de puertos expuestos por la imagen mysql en su versión 8.0.22"
    ```bash
    # Mostrar la lista de puertos expuestos
    > docker inspect --format '{{.Config.ExposedPorts}}' mysql:8.0.22
    ```

que produce la sigiente salida por consola

!!! quote "Salida por consola"
    ```bash
    # Salida por consola
    map[3306/tcp:{} 33060/tcp:{}]
    ```

Evidentemente, para poder realizar el formateo deseado será necesario conocer en profundidad la estructura del objeto JSON retornado por la orden.
