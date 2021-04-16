# 5.4.- Asociando redes a contenedores

En los tres apartados anteriores hemos hablado de aspectos relativos a las redes docker pero no hemos dicho nada sobre cómo conectar esas redes a nuestros contenedores, que es el paso fundamental para que dichos contenedores puedan conectarse entre ellos, puedan ofrecer servicios al exterior y puedan conectarse a Internet para poder actualizarse y/o instalar cualquier dependencia que necesiten. 

Al arrancar un contenedor podemos especificar a qué red está conectado inicialmente usando el *flag* `--network` seguido del nombre de la red a la que queremos conectarlo. Por ejemplo:

!!! success "Asociar un contenedor a una red al arrancarlo"
    ```bash
    # Arranchar un contenedor Apache asociándolo a la red red1 
    > docker run -d --name web1 --network red1 -p 8080:80 httpd
    ```

Si al arrancar un contenedor no especificamos una red, el contenedor se conectará a la red *bridge* por defecto. Por ejemplo.

!!! success "Asociar un contenedor a la red por defecto al arrancarlo"
    ```bash
    # Arranchar un contenedor Apache asociándolo a la red por defecto 
    > docker run -d --name web2 -p 8181:80 httpd
    ```

Para indicar la IP que queremos asociarle al contenedor a la red podemos usar el *flag* `--ip`. Por ejemplo:

!!! success "Asociar un contenedor a una red al arrancarlo asignándole una ip"
    ```bash
    # Arranchar un contenedor Apache asociándolo a la red red1 
    > docker run -d --name web3 --network red1 --ip 172.18.0.5 -p 8282:80 httpd
    ```

Debemos tener en cuenta que no podemos conectar inicialmente un contenedor a más de una red, aunque una vez arrancado sí podremos conectarlo a más redes o desconectarlo de alguna red. 

Si no se trata de la red *bridge* por defecto, dicha conexión o desconexión se podrá realizar en caliente, esto es, con el contenedor en ejecución. Sin embargo, en el caso de la red *bridge* por defecto tendremos que parar el contenedor antes.

La conexión la llevaremos a cabo mediante la orden `docker network connect red contenedor`. Si un contenedor está en ejecución, podremos desconectarlo de una red mediante la orden `docker network disconnect red contenedor`. Veamos un ejemplo:

!!! success "Conexión y desconexión de un contenedor a una red"
    ```bash
    # El contenedor web3 se conecta inicialmente a la red1.
    > docker run -d --name web3 --network red1 --ip 172.18.0.5 -p 8282:80 httpd
    # Conectar el contenedor web3 a la red2. 
    > docker network connect --ip 172.28.0.3 red2 web3
    # Desconectar el contenedor web3 de la red1. 
    > docker network disconnect red1 web3
    ```

La orden `docker connect` tiene opciones adicionales que podemos consultar en su documentación oficial. 

Además, la orden `docker run` dispone de una serie de *flags* relacionados con redes y que pueden resultar de interés, como por ejemplo:

- `--dns`: para establecer unos servidores DNS predeterminados.
- `--ip6`: para establecer la dirección de red ipv6
- `-h` o `--hostname`: para establecer el nombre de host del contenedor. Si no se establece será el ID del mismo.

Si queremos conocer las caractísticas de red de un contenedor, como por ejemplo su ip, debemos usar la orden `docker inspect contenedor`, en cuyo objeto JSON resultante encontraremos una sección `Network` dedicada a las redes a las que está conectado dicho contenedor, con toda la información relevante, como el tipo de red del que se trata, la dirección IP del contenedor, la del *gateway*, la *MAC address*, etc.