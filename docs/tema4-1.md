# 4.1.- Persistencia en los contenedores con volúmenes y bind mounts

## Intro

Debemos tener en cuenta que cuando destruimos un contenedor, los ficheros, datos y configuraciones que hayamos creado en él son destruidos. Esto no sucede cuando simplemente paramos el contenedor, en cuyo caso son preservados. 

Los datos de los contenedores están fuertemente acoplados con el host en el que el contenedor está ejecutándose. Además la escritura en los contenedores es más lento que la escritura en el host, ya que tenemos una capa adicional.

Docker nos proporciona varias soluciones para persistir datos de contenedores. En este curso nos vamos a centrar en las dos más importantes, los volúmenes docker y los *bind mount*.

Con ambas soluciones, el sistema de archivos del contenedor hará referencia a un directorio que en realidad se encuentra en la máquina host donde está instalado docker, de manera que pueda ser usado desde dentro del contenedor como si fuera suyo. Veámoslo gráficamente:

!!!info "Persistencia en contenedores"
    <figure>
      <img src="../assets/almacenamiento.png">
      <figcaption>Persistencia en contenedores</figcaption>
    </figure>

Un volumen es una parte del sistema de ficheros de la máquina host que es gestionada por docker, y a la que, debido a sus permisos, sólo docker tendrá acceso. 

Si elegimos conseguir la persistencia de los datos de los contenedores usando *bind mount* lo que estamos haciendo es mapear una parte de del sistema de archivos de nuestro *host*, sobre la que normalmente tenemos control, con una parte del sistema de ficheros del contenedor.

!!! note "Otras formas de persistencia"
    Existen otras soluciones, como los *tmpfs mounts* para Linux y los *named pipes* para Windows.

## Volúmenes

!!!info inline end "Volúmenes docker"
    <figure>
      <img src="../assets/docker_volumes.png">
      <figcaption>Volúmenes docker</figcaption>
    </figure>

Docker nos permite crear volúmenes, esto es, definir zonas del sistema de ficheros de la máquina host para que sean usadas para persistencia por los contenedores.

Para que un contenedor use un determinado volumen debemos indicárselo explícitamente cuando lancemos el contenedor. 

Un volumen puede se usado por más de un contenedor, lo que permite compartir datos entre varios contenedores.

Debemos tener en cuenta el espacio de arhivos que representa un volumne, solo podrá ser accedido desde docker, y a través de los contenedores a los que se las asociemos.

La zona reservada por docker para crear los volúmnes cambia de un sistema operativo a otro y también puede cambiar dependiendo de la forma de instalación, pero las rutas habituales son:

- `/var/lib/docker/volumes` en las distribuciones de Linux si lo hemos instalado desde paquetes estándar.
-  `/var/snap/docker/common/var-lib-docker/volumes` en Linux si hemos instalado docker mediante snap (no está recomiendo).
- `C:\ProgramData\docker\volumes*` en las instalaciones de Windows.
- `/var/lib/docker/volumes*` también en Mac aunque se requiere que haya una conexión previa a la máquina virtual que se crea.

Los volúmenes se suelen usar con los siguientes objetivos:

- Para compartir datos entre contenedores. Simplemente tendrán que usar el mismo volumen.
- Para crear copias de seguridad, ya sea para ser usadas posteriormente por otros contenedores o para mover esos volúmenes a otros hosts.
- Para almacenar los datos de nuestro contenedor no localmente si no en un proveedor *cloud*.
- Para conseguir un mayor rendimiento cuando estemos usando Docker Desktop.

## Bind mount

Una alternativa al uso de volúmenes para la persistencia de los datos es lo que se conoce como *bind mount*, que consiste básicamente en indicarle al contenedor, cuando lo lanzamos, un directorio del sistema de archivos de nuestro *host* para que sea usado por el contenedor como si perteneciera al sisetma de archivos de éste.

Este mecanismo nos va a permitir, por un lado, compartir ficheros entre el host y los containers, y lo es aún más importante, que otras aplicaciones externas al contenedor y que no sean docker tengan acceso a esos ficheros.

El hecho de que otras aplicaciones externas accedan a esos datos puede parecer en un principio algo negativo, pero precisamente esa es la cualidad que hace que los *bind mounts* sea el mecanismo recomendado para la fase de desarrollo de un proyecto, ya que:

- Los IDEs o editores de código podrán acceder a esos ficheros.
- Estaremos modificando con aplicaciones locales código que a la vez se encuentra en nuestro equipo y en el contenedor.
- Desde nuestro propio equipo estaremos probando nuestro código en el entorno elegido, o en varios entornos a la vez, sin necesidad de tener que instalar absolutamente nada en nuestro sistema.
