# 2.5.- Ejecución de servicios. Puertos y variables de entorno

Una de las cosas más atrayentes de usar docker no es tan solo el hecho de que podemos probar todas versiones de los distintos sistemas que van apareciendo, sino que para probar y usar cualquier servicio  y cualquier aplicación no tenemos que instalar nada en nuestro sistema. Sea cual sea el servicio o la aplicación que se nos ocurra, estará disponible en DockerHub. Solo teneos que buscarla, averiguar cuál es la versión que queremos y lanzar el contenedor o contenedores necesarios. Y si por casualidad no está en DockerHub, siempre podremos construir una imagen para ello, como veremos más adelante.

De esta manera evitamos la instalación de aplicaciones, con un ahorro considerable en cantidad de ficheros, algunos de ellos incluso que no son eliminados adecuadamente si desinstalamos la aplicación.

El uso de contenedores de este tipo impacta de manera muy favorable la mi práctica docente en al menos dos aspectos:

- Permite utilizar servicios y aplicaciones que a veces, dadas la limitada potencia de los equipos de aula que tenemos, es difícil que podamos usar con soltura. Recordad que una de las características de los contenedores es que eran entornos de ejecución aislados (no dejan basura ninguna si los elimino) y que la sobrecarga sobre el sistema es mínima si lo comparábamos con una máquina virtual, por lo arrancan de una manera mucho más rápida.
- Se elimina de una vez el típico problema de "en mi equipo no funciona". En cualquier equipo donde se haya instalado Docker, el contenedor que quiero que los demás usen va a funcionar con total seguridad.

De momento en este apartado nos vamos a centrar en servicios de un solo contenedor, como por ejemplo servidores de bases de datos, servidores web, servidores de aplicaciones etc., servicios que por otra parte son de uso casi diario en nuestras aulas. En módulos posteriores veremos aplicaciones que requieren de la interacción de más de un contenedor.

Para la ejecución de contenedores de este tipo vamos a tener que en cuenta varias cosas.

En primer lugar, debemos usar el flag `-d` o `--dettach`, para que el servicio se ejecute en modo background o *dettach* (desenganchado). Si no lo hacemos se bloqueará el terminal mostrando el log del servicio (en ciertas ocasiones puede interesarnos) y tendremos que salir del mismo con ++ctrl+c++. Al pulsar esta combinación de teclas el contenedor es parado, aunque podremos arrancarlo de nuevo posteriormente.

Además, si queremos que el servicio que vamos a lanzar sea accesible desde el exterior tendremos que añadir el flag `-p PuertoEnHost:PuertoEnContenedor`, que normalmente corresponderá al puerto por defecto del servicio correspondiente. Como vemos, esta opción establece una redirección de puertos. Podemos tener varias reglas `-p` al arrancar (en algunos servicios será necesario) y es muy importante recordar que no podemos tener dos servicios escuchando en el mismo puerto. Si lo intentamos se nos mostrará un mensaje de error.

Finalmente, deberemos comprobar y definir si es necesario las variables de entorno que puede tener el contenedor. Las variables de entorno importantes se describen en la página en DockerHub para la imagen correspondiente, y para usarlas tenemos que usar el flag `-e NombreVariable=Valor` para cada variable.

Para ilustrar lo anterior vamos a ver una serie de ejemplos. En el primero de ellos vamos a ejecutar el servidor Apache en primer plano sin redirección de puertos:

!!! success "Servidor Apache en primer plano"
    ```bash
    # Ejecuta un servidor Apache sin el flag -d ni redirección de puertos.
    > docker run httpd
    ```

que produce la siguiente salida por consola:

!!! quote "Salida por consola"
    ```bash
    # Salida por cosola
    AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.22. Set the 'ServerName' directive globally to suppress this message
    AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.22. Set the 'ServerName' directive globally to suppress this message
    [Mon Dec 07 18:27:28.561909 2020] [mpm_event:notice] [pid 1:tid 140253864719488] AH00489: Apache/2.4.46 (Unix) configured -- resuming normal operations
    [Mon Dec 07 18:27:28.562072 2020] [core:notice] [pid 1:tid 140253864719488] AH00094: Command line: 'httpd -D FOREGROUND'
    ```

En este caso el terminal es bloqueado mostrando los *logs* del servicio. Para salir de él tendremos que usar ++ctrl+c++.

En el siguiente ejemplo ejecutamos el servidor Apache en segundo plano (*background*) y redireccionamos los puertos de manera que el puerto `8888` de nuestro equipo anfitrión se haga corresponder al puerto `80` del contenedor.

!!! success "Servidor Apache en background con redirección de puertos"
    ```bash
    # Ejecuta un servidor Apache en background y accediendo desde el exterior a través del puerto 8888 de nuestra máquina.
    > docker run -d -p 8888:80 httpd
    ```

En este caso no vemos los *logs* del servidor Apache en consola, ya que ésta regresa inmediatamente, aunque el contenedor sigue en ejecución.

Si queremos engancharnos de nuevo con el servidor podemos ejecutar la orden `docker attach`, al que le pasaremos el id o el nombre del contenedor.

Finalmente vamos a ver cómo arrancar un servidor de bases de datos MariaDB de forma interactiva en segundo plano, de manera que podamos en nuestro equipo acceder a él a través del puerto `3306,` estableciendo la contraseña de root del servidor a través de una variable de entorno.

!!! success "MariaDB en background con redirección de puerto y variable de entorno"
    ```bash
    # Creación de un servidor de base de datos mariadb accediendo desde el exterior a través del puerto 3306 y estableciendo una contraseña de root mediante una variable de entorno
    > docker run -it -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root mariadb
    ```

En este caso el servidor queda en ejecución en segundo plano y la consola retorna inmediatamente.

Como estudiaremos más adelante, si queremos abrir una consola en el servidor, deberemos ejecutar en él el shell de forma interactiva, de la siguiente forma:

!!! success "Ejecutar consola en el servidor de base de datos"
    ```bash
    docker exec -it a1e4a567e9ee /bin/bash
    ```

En el ejemplo anterior suponemos que `a1e4a567e9ee` es id del contenedor correspondiente.
