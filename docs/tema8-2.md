# 8.2.- Seguridad en el proceso de build

Cuando estamos construyendo nuestras propias imágenes también debemos tener en consideración ciertos factores a la hora de mejorar la seguridad de las imágenes resultantes.

Como ya vimos en el apartado sobre los *Dockerfile*, la orden `ADD` funciona como una copia recursiva desde el origen hasta la carpeta destino en el contenedor, creando los directorios en caso de que no existan. Esta copia recursiva puede ser desde un contenido local o desde una URL y además si le pasamos un archivo comprimido a la orden `ADD` los descomprime de manera automática.  

En el caso de que este contenido venga de una URL, ¿podemos confiar realmente en los autores?, ¿cómo sabemos que los contenidos no han sido comprometidos a nivel de seguridad en el origen?, ¿nos hemos protegido frente a ataques *man in the middle*?. Evidentemente, todos estos aspectos deben de ser tenidos en cuenta.

Al igual que `ADD`, la orden `COPY` hace una copia recursiva desde el origen hasta el destino pero no permite ni url de origen ni descomprime los ficheros. Si copiamos un archivo .zip este permanecerá como un archivo .zip. 

El problema en la orden `COPY` reside en la recursividad de la copia. Cuando copiamos elementos de forma recursiva incrementamos la posibilidad de, además de aumentar el tamaño de la imagen de manera innecesaria, copiar en el contenedor ficheros sensibles que contengan claves, tokens de APIs, etc., por lo que para evitarlo debemos tener un fichero `.dockerignore` debidamente configurado que excluya explícitamente del proceso de copia recursiva archivos con claves, como *.ENV, *.pem, etc.

Otro aspecto importante corresponde a los permisos de acceso que se tienen sobre los recursos de una imagen. Como norma general los procesos que se ejecutan en un contenedor, al igual que en otros sistemas, no deben ejecutarse como *root*, en especial aquellos que son servicios expuestos al exterior.

Desde el punto de vista de seguridad una buena práctica es que las aplicaciones únicamente tengan acceso a los recursos que necesitan para desempeñar sus funciones (principio del mínimo privilegio). Si no lo  hacemos así y ejecutamos aplicaciones como *root*, esa aplicación podrá realizar cualquier operación en nuestro sistema, como por ejemplo realizar operaciones no permitidas, como borrar ficheros, parar servicios y transferir información, así como acceder a ficheros con contenido sensible que hayamos podido colocar en el contenedor, por ejemplo usando un *bind mount*.

Para afrontar este tipo de problemas, a la hora de crear nuestro *Dockerfile* debemos usar el siguiente flujo de trabajo:

1. Crear un usuario y un grupo para ese usuario, gracias a la orden `RUN`.
2. Ejecutar todas las instrucciones del *Dockerfile* que tengan que ser realizadas como root (`RUN`, `COPY`, `ADD`, `WORKDIR`, etc.).
3. Antes de ejecutar el `ENTRYPOINT` y/o el `CMD`, cambiar el usuario de ejecución de las órdenes al usuario creado previamente, mediante la orden `USER`.
4. Definir el `ENTRYPOINT` y/o `CMD` y que esas órdenes lancen procesos pertenecientes al usuario creado, y no al usuario *root*.

Veamos un ejemplo de esta estrategia:

!!! success "Estrategia para conceder permisos mínimos"
    ```Dockerfile
    FROM ...
    # ...
    # Creación del usuario para arrancar el servicio
    RUN addgroup -S usuario && adduser -S usuario -G usuario.
    # ...
    # Lista de órdenes que serán de ROOT
    # ...
    # Se establece el usuario que ejecutará las siguientes órdenes.
    USER usuario
    # Se define el ENTRYPOINT, que se ejecutará como usuario.
    ENTRYPOINT ...
    ```
