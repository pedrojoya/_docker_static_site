# 2.4.- Arranque interactivo de un contenedor

Una de los problemas que nos encontramos al empezar a trabajar con Docker es el hecho de que, si no los lanzamos correctamente, algunos contenedores se paran y ya no se puede acceder a ellos ni iniciarlos de nuevo.

A diferencia de las máquinas virtuales, los contenedores no están diseñados específicamente para ejecutar sistemas operativos, sino más bien para ejecutar una proceso concreto, como un servidor web o un servidor de bases de datos, o simplemente para ejectuar un tarea concreta de computación, de manera que cuando ésta se completa, el contenedor se termina (*exits*). 

Un contenedor dura vivo lo que dure vivo el proceso o tarea que ejecuta. Por ejemplo, si el servidor web que está ejecutando el contenedor es parado o termina con un fallo, el contenedor terminará.

Este es el motivo por el que si ejecutamos un contenedor correspondiente a un sistema operativo sin indicar nada más, éste es completado directamente, sin posibilidad de reiniciarlo.

Para evitarlo, debemos seguir unas reglas muy simples.

En primer lugar, debemos siempre usar las *flags* `-it` al lanzar un contenedor con el que queramos interactuar.

Por defecto, los contenedores no escuchan a la entrada estándar. Para que lo hagan, debemos usar el *flag* `-i` o `--interactive`. Y tampoco están enganchados al terminal por defecto. Si queremos que lo estén tendremos que usar el *flag* `-t`.

Así que usaremos la combinación `-it`, unión del *flag* `-i` (`--interactive`) y del *flag* `-t` (`--tty`), que abren la entrada estándar del contenedor y lo enganchan a un terminal, lo que nos va a permitir interactuar con el contenedor a través de un terminal.

Por otra parte, no debemos añadir al final de `docker run` una orden distinta de `/bin/bash` o de cualquier otro *shell*, ya que eso sobrescribe la orden de arranque de algunos contenedores.

Normalmente la orden de arranque de los contenedores correspondientes a sistemas operativos consiste en ejecutar la orden `sh`, que hace que se muestre el *shell*.

En los contenedores correspondientes a servicios la orden de arranque por defecto corresponde al inicio del propio servicio, por lo que tampoco debemos sobrescribir dicha orden.

Veamos un ejemplo en el que se arranca un contenedor de Ubuntu 18.04 de forma interactiva de manera que se nos muestre un shell en el que ejecutar comandos como root:

!!! success "Ubuntu 18.04 interactivo en shell con root"
    ```bash
    # Descargar una imagen de manera previa
    > docker pull ubuntu:18.04
    # Crear un contenedor de ubuntu:18.04 y tener acceso a un shell en él. 
    > docker run -it --name ubuntu ubuntu:18.04 /bin/bashroot@ef2bea1d6cb1:/#
    ```

Debemos de tener en cuenta que si los contenedores no son contenedores con servicios, son parados automáticamente si tras interactuar con ellos la primera vez decidimos salir con la orden `exit` en la consola del contenedor. 

Por ejemplo si estando en el contenedor anterior hacemos:

!!! success "Salida del shell del contenedor"
```bash
@ef2bea1d6cb1:/# exit
```

el contenedor será parado. Para iniciarlo de nuevo, tenemos la orden `docker start contenedor`, que puede recibir el id del contenedor o su nombre. Por ejemplo, para el contenedor anterior haremos:

!!! sucess "Iniciar el contenedor llamado ubuntu"
    ```bash
    # Arrancar el contenedor llamado ubutu
    > docker start ubuntu
    ```

Más adelante estudiaremos con detenimiento las órdenes para parar, iniciar tras parada y reiniciar un contenedor.
