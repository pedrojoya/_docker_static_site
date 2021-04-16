# 2.7.- Copia de archivos entre el equipo local y el contenedor

## Copia de archivos hacia el contenedor

En muchas ocasiones vamos a querer copiar archivos desde nuestro equipo local hacia el contenedor. 

Para ello usaremos la orden `docker cp pathOrigen contenedor:pathDestino`.

Así en el siguiente ejemplo, copiamos el archivo `prueba.html` del directorio actual de nuestro equipo, como `/usr/local/apache2/htdocs/index.html` del sistema de archvios del contenedor llamado `web`:

!!! success "Copia de archivo hacia un contenedor"
    ```bash
    # Copia prueba.html a /usr/local/apache2/htdocs/index.html del contenedor web
    > docker cp prueba.html web:/usr/local/apache2/htdocs/index.html
    ```

## Copia de archivos desde el contenedor

En otras ocasiones, en nuestro contenedor se ha generado algún archivo que queremos copiar a nuestra máquina local. Para ello podemos usar la misma orden, pero con sintaxis distinta: `docker cp contenedor:pathOrigen pathDestino`.

Así, en el siguiente ejemplo copiamos el archivo `/usr/local/apache2/htdocs/index.html` de nuestro contenedor llamado `web` en nuestra máquina local, en concreto en el directorio *home* del usuario pero con el nombre de archivo `test.html`:

!!! success "Copia de archivo desde contenedor"
    ```bash
    # Copia el fichero /usr/local/apache2/htdocs/index.html del contenedor web como $HOME/test.html de la máquina local
    > docker cp web:/usr/local/apache2/htdocs/index.html $HOME/test.html
    ```

## Imágenes mínimas

Debemos tener en cuenta que las imágenes normalmente solo tienen los elementos imprescindibles. Por ejemplo, en sistemas basados en Debian ni siquiera poseen los repositorios de aplicaciones, por lo que si queremos instalar alguna aplicación adicional, antes tendremos que hacer `apt update` para obtenerlos.