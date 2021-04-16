# 2.6.- Ejecución de órdenes en los contenedores

## Intro

Una vez que tengamos un contenedor en ejecución (en estado *Up*), será muy habituales que queramos ejecutar órdenes sobre él, para realizar operaciones tales como instalar paquetes, modificar o ver el contenido de ciertos ficheros, habilitar ciertos módulos de servicios, etc. 

Esto lo podemos hacer de dos maneras: 

* Ejecutando un terminal en el contenedor y desde él ejecutar las órdenes deseadas.
* Ejecutando directamente la orden deseada sobre el contenedor. 

En ambos casos tendremos que usar la orden `docker exec`. Para que dicha orden tenga efecto es necesario que el contenedor esté en ejecución, es decir, en estado *Up*.

La sintaxis de esta orden es bastante sencilla: `docker exec [opciones] nombre_contenedor orden [argumentos]`

## Ejecución de un terminal en un contenedor

La opción (*flag*) más importante de esta orden es `-it`, que va a permitir tener interactividad con el contenedor ejecutando un shell, `/bin/bash` normamente. Una vez tenemos el terminal ya podremos trabajar desde dentro del propio sistema. 

Así, el siguiente ejemplo:

!!! success "Ejecución de un terminal en un servicio"
    ```bash
    # Ejecución de un terminal en un contenedor Apache llamado web
    > docker exec -it web /bin/bash
    ```

abre un terminal en el contenedor con la siguiente salida por pantalla:

!!! quote "Salida por pantalla"
    ```bash
    # Salida por consola
    root@5d96ce1f7374:/usr/local/apache2#
    ```

De esta manera, podemos ejecutar sobre dicho terminal del contenedor las órdenes que estimemos oportunas.

## Ejecución de una orden concreta en el contenedor

También podemos ejecutar una orden concreta en un contenedor sin crear un terminal en él. Si queremos que la orden se ejecute en un directorio concreto, podemos usar el *flag* `-w` o `--workdir` de la orden `docker exec` para indicar el directorio de trabajo.

Así, en el siguiente ejemplo listamos un directorio concreto establecido como directorio de trabajo en el contenedor web:

!!! success "Ejecución de una orden concreta sin terminal"
    ```bash
    # Mostrar el contenido de la carpeta /usr/local/apache2/htdocs del contenedor web. Como no hace falta interactividad no es necesario -it
    > docker exec -w /usr/local/apache2/htdocs web ls 
    ```

Si la orden debe ser ejecuta como si fuera un usuario distinto del de *root*, debermos usar el *flag* `-u` o `--user` para indicar el usuario con el que se debe ejecutar.

## Creación de ficheros en el contenedor por redirección

En algunas ocasiones querremos crear ficheros en el contenedor, cuyo contenido sea la salida de una determinada orden que queramos ejecutar. En ese caso, podemos ejecutar una orden de *shell* y redireccionar la salida a un fichero.

Así, en el siguiente ejemplo se crea en el contenedor llamado `web` el fichero `/usr/local/apache2/htdocs/index.html` con el contenido `HOLA MUNDO`:

!!! success "Creación de fichero en contenedor por redirección"
    ```bash
    # Creación de fichero por redirección
    > docker exec -it web sh -c "echo 'HOLA MUNDO' > /usr/local/apache2/htdocs/index.html"
    ```
