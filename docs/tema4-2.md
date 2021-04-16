# 4.2.- Gestión de volúmenes

En este apartado nos centraremos en los volúmenes y en las operaciones básicas que podemos hacer con ellos mediante la orden `docker volume`, que corresponden a la creación, obtención de información y eliminación de volúmenes.

## Creación de volúmenes

Para la creación de volúmenes vamos a usar la orden `docker volume create [options] [nombreVolumen]`. Por ejemplo:

!!! success "Creación de un volumen llamado data"
    ```bash
    # Creación de un volumen llamado data
    > docker volume create data
    ```

Cada volumen está gestionado por un controlador o *driver*. Los distintos tipos de controladores de volumen permiten almacenar volúmenes en hosts locales, remotos o proveedores en la nube, cifrar el contenido, etc.

Para indicar el *driver* debemos usar la opción `-d` o `--driver`.

!!! success "Creación de un volumen llamado data con driver local"
    ```bash
    # Creación de un volumen llamado data especificando el driver local
    > docker volume create -d local data
    ```

Si al crear un volumen no especificamos el *driver* que queremos que use, se utilizará el driver `local`, que es el más interesante durante el desarrollo. 

Podríamos indicar que los volúmenes estén en otros dispositivos, en montajes *nfs* e incluso en servicios de almacenamiento en nube públicas o privadas.

En el caso de Linux el *driver* `local` es `overlay2`, pero existen otras posibilidades como `aufs`, `btrfs`, `zfs`, `devicemapper` o `vfs`. 

Si estamos interesados en conocer al detalle cada uno de ellos [aquí](https://docs.docker.com/storage/storagedriver/) tenemos más información.

También tenemos disponibles otras *flags* en la orden `docker volume create`:

- `--label`: para especificar los metadatos del volumen mediante parejas clave-valor.
- `-o` o `--opt`: para especificar opciones relativas al *driver* elegido. Si son opciones relativas al sistema de ficheros puedo usar una sintaxis similar a las opciones de la orden `mount`.

Por ejemplo:

!!! success "Creación de un volumen llamado con varios metados"
    ```bash
    # Creación de un volumen llamado web añadiendo varios metadatos
    > docker volume create --label servicio=http --label server=apache web
    ```

## Obtener información de los volúmenes

Si queremos obtener información sobre los volúmenes que hemos creado, lo primero que podemos hacer es listar los volúmenes disponibles.

Para ello ejecutaremos la orden `docker volume ls`, que nos mostrará por consola una lista con los volúmenes creados junto con el *driver* que usa cada uno de ellos. Veamos un ejemplo:

!!! success "Listar los volúmenes creados en el sistema"
    ```bash
    # Listar los volúmenes creados en el sistema
    > docker volume ls
    ```

Si queremos información más detallada sobre un volumen en concreto, tenemos la orden `docker volume inspect`, al que le podemos pasar el nombre del volumen o su id, que retorna un objeto JSON con toda la información sobre el volumen en cuestión. Por ejemplo:

!!! success "Listar los volúmenes creados en el sistema"
    ```bash
    # Información detallada del volumen llamado data
    > docker volume inspect data 
    ```

Entre la información retornada encontraremos la fecha de creación del volumen, el tipo del driver, etiquetas asociadas, el punto de montaje, el nombre del volumen, las opciones asociadas al *driver* y el ámbito del volumen.

## Eliminación de volúmenes

Si queremos eliminar un volúmen creado anteriormente, podemos usar la orden `docker volume rm`, al que le pasaremos el nombre del volumen o su id. Por ejemplo:

!!! success "Eliminación del volumen llamado data"
    ```bash
    # Eliminar el volumen llamado data
    > docker volume rm data 
    ```

Evidentemente si el volumen está en uso por algún contenedor, no podrá ser borrado, a no ser que usemos el *flag* `-f` o `--force` para forzar la eliminación. Por ejemplo:

!!! success "Forzar la eliminación del volumen llamado data"
    ```bash
    # Forzar la eliminación del volumen llamado data
    > docker volume rm -f data 
    ```

Si queremos eliminar de una vez todos los volúmenes que no estén siendo usados por ningún contenedor, podemos usar la orden `docker volume prune`. Por ejemplo:

!!! success "Eliminar todos los volúmenes que no estén en uso"
    ```bash
    # Eliminar todos los volúmenes que no tengan contenedores asociados
    > docker volume prune
    ```

Si no queremos que se nos solicite confirmación para borrar cada uno de los volúmenes, podemos usar el flag `-f` o `--force`. Por ejemplo:

!!! success "Eliminar todos los volúmenes que no estén en uso"
    ```bash
    # Eliminar todos los volúmenes que no tengan contenedores asociados sin pedir confirmación
    > docker volume prune -f
    ```

También podemos filtrar para que solo se eliminen los que tenga un determinado metadato, para lo que usaremos el *flag* `--filter`. Por ejemplo:

!!! success "Eliminar todos los volúmenes que no estén en uso"
    ```bash
    # Eliminar todos los volúmenes sin usar que tengan el valor http en la etiqueta servicio
    > docker volume prune --filter servicio=http
    ```
