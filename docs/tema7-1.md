# 7.1.- docker-compose

Hasta ahora hemos estado hablando de contenedores de forma aislada, pero en la actualidad las aplicaciones actuales siguen una arquitectura multicapa formada por distintas aplicaciones o servicios.

Las arquitectura más clásica dispone de tres capas:

- Una **capa de presentación**, que hace referencia al cliente que obtiene los datos, como por ejemplo un navegador, una app móvil, etc.
- Una **capa lógica**, que normalmente está representada por un servidor web, un servidor de aplicaciones, etc.
- Una **capa de datos**, que reside normalmente en un servidor de base de datos, ya sea relacional o no.

!!!info "Arquitectura típica de una aplicación multi-capa"
    <figure>
      <img src="../assets/APP_MULTICAPA.png">
      <figcaption>Arquitectura típica de una aplicación multi-capa</figcaption>
    </figure>

Sin embargo, nos podemos encontrar aplicaciones con arquitecturas mucho más complejas, con diversos servidores de bases de datos, distintos APIs independientes, servidores de autentificación, etc., aunque nosotros nos centraremos en las aplicaciones con las tres capas indicadas anteriormente.

En el módulo anterior ya vimos un ejemplo de este tipo de aplicaciones donde teníamos, por un lado, un contenedor con un servidor web Apache, con el módulo PHP instalado y el código de Wordpress descargado en la carpeta adecuada, y por otro lado, un contenedor con un servidor de base de datos MariaDb para guardar los datos de la aplicación.

Hasta hora, hemos creado y configurado estos tipos de contenedores por separado, aunque conformaban una única aplicación. De hecho, hemos tenido que arrancarlos y configurarlos uno a uno de manera manual, lo que evidentemente no es lo ideal. 

Sería mucho más óptimo poder definir los contenedores de la aplicación de manera declarativa, para que no tener que repetir todo el proceso cada vez, y poder lanzar todos los contenedores de la aplicación de una sola vez, debidamente configurados, asegurándonos de que hay comunicación entre los contenedores que pertenecen a la aplicación.

En algunas ocasiones además es necesario garantizar que los contenedores se arrancan en el orden adecuado. Por ejemplo, puede que una aplicación no pueda funcionar debidamente hasta que el servidor de bases de datos no esté arrancado y en funcionamiento.

Para poder realizar todos esto de forma cómoda, docker nos proporciona la herramienta `docker-compose`, que nos permite desplegar grupos de contenedores que forman parte de una misma aplicación o un mismo entorno.

!!!info "Proceso general de docker-compose"
    <figure>
      <img src="../assets/DOCKERCOMPOSE.png">
      <figcaption>Proceso general de docker-compose</figcaption>
    </figure>

Si somos usuarios de MAC o Windows, la herramienta `docker-compose` vendrá incluida dentro de Docker Desktop. Sin embargo, si somos usuarios de Linux tendremos que realizar su instalación, mediante las siguientes instrucciones:

!!! success "Instalación de docker-compose en Linux"
    ```bash
    # Descarga del fichero mediante la orden curl y colocación en el directorio adecuado. Actualmente (Enero 2021) la versión vigente es la 1.27.4 
    > sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-(uname -s)-(uname -m)" -o /usr/local/bin/docker-compose
    # Concesión de los permisos de ejecución
    > sudo chmod +x /usr/local/bin/docker-compose 
    # Comprobación de que la instalación está correcta.
    > docker-compose --versiondocker-compose version 1.27.4, build 1110ad01
    ```

Como es obvio, para que `docker-compose` funcione será necesario que tengamos instalado docker.

Para usar esta herramienta, primero en la carpeta de nuestro proyecto crearemos un archivo `docker-compose.yml` en el que especificaremos de forma declarativa los contenedores que forma parte de nuestra aplicación, tal y como veremos en el siguiente apartado. Este fichero tiene formato [YAML](https://docs.ansible.com/ansible/latest/reference_appendices/YAMLSyntax.html).

Después, para ejecutar la aplicación, de manera que se lancen los distintos contenedores que la conforman deberemos ejecutar la orden `docker-compose up`. Más adelante estudiaremos todas las posibilidades que nos ofrece esta herramienta.

