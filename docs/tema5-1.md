# 5.1.- Tipos de redes en Docker

En los temas anteriores hemos comprobado varias veces que los contenedores pueden conectarse a internet para realizar distintas tareas, como bajarse actualizaciones o instalar paquetes a partir de los repositorios que venían con las distintas distribuciones.

Sin que tengamos que llevar cabo ningún tipo de configuración, los contenedores que creamos son capaces de conectarse, no solo entre ellos, sino también con el exterior.

De hecho, el contenedor ni siquiera es consciente del funcionamiento de la red ni de la plataforma sobre la que funciona. Podríamos decir que es agnóstico respecto a la red y al sistema.

Además, se nos permitirá acceder a los contenedores desde fuera, aunque en algunos tipos de redes sera necesario que configuremos el mapeo (redirección) de puertos entre el contenedor y la máquina anfitrina.

Cada red deberá ser gestionada por un *driver* o controlador, que hace que la red tenga un determinado tipo de funcionamiento. Así, docker permite crear redes gestionadas por los siguientes tipos de *drivers*:

- **Bridge:** Es el *driver* que se usa por defecto si al crear una red no lo especificamos. Nuestro equipo *host* actúa como puente del contenedor con el exterior y como medio de comunicación entre los distintos contenedores de la misma red.
- **Host:** Se caracteriza porque el contenedor usará directamente la red de nuestra máquina *host*.
- **Overlay:** Corresponde a un sistema que conecta distintos servicios docker de máquinas diferentes. Se utiliza para docker Swarm, que es la tecnología de docker para la orquestación de contenedores.
- **MacVlan:** Nos permite asignar una MAC a nuestro contenedor para simular que se trata de un dispositivo físico en nuestra red.

Las redes que usan el *driver* *bridge* se tratan de redes internas privadas creadas por docker en la máquina anfitriona. Cuando un contenedor es conectado a una red de este tipo, recibe una ip, por defecto del rango `172.17.X.X`. Cada contenedor de la red puede acceder a los otros usando dichas ip internas. Sin embargo, para poder acceder a cualquier de estos contenedores desde fuera, será necesario mapear determinados puertos del contenedor hacia puertos de la máquina anfitriona *host*.

Por el contrario, las redes que usan el *driver* *host*, trabajan directamente sobre la red en la que está funcionando la máquina anfitriona, lo que implica que si un contenedor está usando un determinado puerto podremos acceder a él directamente desde el exterior usando la ip de la máquina anfitriona, sin que sea necesario realizar una mapeo de puertos. Sin embargo, esto tiene un gran inconveniente, que es el hecho de que no podremos tener en cualquier red de este tipo dos contenedores que estén usando el mismo puerto, ya que entrarían en conflicto, algo que sí podemos hacer en las redes de tipo `bridge` mientras mapeemos el mismo puerto de los dos contenedores a dos puertos distintos de la máquina anfitriona.

Un ejemplo de una configuración de una red construida con el *driver* **bridge** podría ser la siguiente:

!!!info "Ejemplo de red docker con driver bridge"
    <figure>
      <img src="../assets/Docker_Brigde.png" style="max-width: 500px">
      <figcaption>Ejemplo de red docker con driver bridge</figcaption>
    </figure>

Este esquema representa una aplicación típica compuesta por dos servicios, un servidor web y un servidor de base de datos, cada uno de ellos en un contenedor diferente y haciendo accesible al exterior nuestro servidor web a través del puerto `8080`. 

Para que todo esto funcione, docker creará de manera automática los interfaces virtuales y los puentes de red necesarios para cada uno de los dispositivos, y configurará las reglas necesarias para que esos interfaces tengan acceso a internet, aislando los contenedores del resto de las redes y estableciendo las redirecciones de puertos necesarias.

Además, cada contenedor podra referirse a otro contenedor en la red usando simplemente su nombre de contenedor, ya que docker contiene su propio servidor DNS integrado, que ayuda a los contenedores a descubrir el resto de contenedores de la red a través de sus nombres de contenedor. Dicho servidor DN integrado siempre se ejecuta en la dirección `127.0.0.11`.
