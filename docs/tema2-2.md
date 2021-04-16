# 2.2.- Listar los contenedores existentes

Conforme vayamos usando Docker, irá en aumento el número de contenedores que tengamos funcionando, por lo que será más complejo seguir la pista de memoria sobre aspectos como si el contenedor tenía persistencia o no, si tenía redirección de puertos o no, si le había puesto algún nombre o estaba usando un nombre concedido por Docker, etc.

Por lo tanto, se hace necesario disponer de una serie de órdenes que nos permitan obtener información sobre los contenedores disponibles.

Conforme vayamos creando contenedores vamos a encontrarnos en la necesidad de listas los contenedores existentes. Para ello usaremos la orden `docker ps`

!!! success "Listar contenedores en ejecución"
    ```bash
    # Mostrar los contenedores en ejecución (Estado Up) 
    > docker ps
    ```

que produce una salida por consola parecida a la siguiente, en la que se muestra un listado de contenedores con la información básica sobre ellos:

!!! quote "Salida por consola"
    ```bash
    CONTAINER ID IMAGE           COMMAND              CREATED      STATUS PORTS NAMES
    36100d33c6a2 ubuntu:18.04     "/bin/bashroot"     3 hours ago  Up (0)       sleepy_wozniak
    ```

Sin embargo, en otras ocasiones no solo querremos listar los contenedores que estén en ejecución, sino también los que estén parados. Para ello deberemos agregar el *flag* `-a` a la orden `docker ps`:

!!! success "Lista todos los contenedores"
    ```bash
    # Mostrar todos los contenedores, ya estén en ejecución (estado Up) o parados (estado Exited) 
    > docker ps -a 
    ```

que produce una salida por consola parecida a la siguiente:

!!! quote "Salida por consola"
    ```bash
    CONTAINER ID IMAGE           COMMAND              CREATED      STATUS PORTS NAMES
    36100d33c6a2 ubuntu:18.04     "/bin/bashroot"     3 hours ago  Up (0)       sleepy_wozniak
    f2c325a26ac6 docker/whalesay "cowsay Hello World" 3 hours ago  Exited (0)   strange_saha
    d189e0c26b84 hello-world     "/hello"             19 hours ago Exited (0)   great_greider
    ```

Aunque la información que nos proporciona es menos exhaustiva que la que podemos obtener con `docker inspect`, sí que nos da los datos básicos sobre los contenedores, tales como:

- El estado del contenedor: parado (*Exited*) o funcionando (*Up*).
- La imagen de la que deriva el contenedor.
- El tamaño actual del contenedor. Para verlo en la salida debemos usar el *flag* `-s` o `--size`.
- La orden que ejecuta el contenedor al arrancar, lo que se conoce el *entry point*.
- El nombre del contenedor, ya sea dado por nosotros o por docker.
- Cuándo fue creado el contenedor.
- Las redirecciones de puertos, en caso de haberlas.

Podemos ver que las filas de la salida nos muestran información de cada uno de los contenedores. El tipo de cada información vienen reflejado en las columnas y podemos apreciar que las redirecciones de puertos solo se muestran en aquellos contenedores que están en ejecución (estado *Up*). Los contenedores parados tiene el estado *Exited*.

También tenemos disponibles otras *flags* para poder filtrar la lista de contenedores retornada por la orden. Por ejemplo:

!!! success "Opciones de filtrado"
    ```bash
    # Mostrar información del último contenedor que se ha creado (-l o --latest). Da igual el estado
    > docker ps -l
    # Filtar los contenedores de acuerdo a algún criterio usando la opción (-f o --filter)
    # Filtrado por nombre
    > docker ps --filter name=servidor_web
    # Filtrado por puerto. Contenedores que hacen público el puerto 8080
    > docker ps --filter publish=8080
    ```

Incluso podemos formatear la salida usando el *flag* `--format`.

Otro *flag* muy interesante es `-q` o `--quiet`, que hace que sólo se retornen los ids de los contenedores. Este flag se utiliza sobre todo para usarlo como entrada de otros comandos docker. Así, en el siguiente ejemplo se borran todos los contenedores de una vez, con el comando `docker rm`, que estudiaremos dentro de poco, haciendo que la entrada de dicho comando sea la salida de listar los ids de los contenedores existentes:

!!! success "Forzar el borrado de todos los contenedores"
    ```bash
    # Forzar el borrado de todos los contenedores
    > docker rm -fv `docker ps -aq`
    ```

Para conocer la referencia completa de `docker run` visite [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)