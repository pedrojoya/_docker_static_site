# 1.1.- ¿Qué es Docker?

## Intro

Desde hace unos años la adopción de la tecnología de contenedores docker por parte de los equipos de desarrollo de software ha sufrido un incremento exponencial.

Su uso aumenta la productividad de los desarrolladores y les permite empaquetar y distribuir sus aplicaciones y todas sus dependencias en contenedores que pueden ser desplegados con gran facilidad en todo tipo de sistemas operativos, servidores, nubes públicas o privadas etc.

Durante este curso nos introduciremos en los fundamentos de los contenedores Docker y buscaremos cómo usarlos en nuestra práctica docente.

Ejecutaremos contenedores, los configuraremos, crearemos nuestros propios contenedores y, para finalizar, realizaremos un flujo de despliegue continuo usando contenedores.

## ¿Para qué sirve?

Si tuviéramos que definir Docker de una manera rápida y poco formal diríamos que  Docker es una tecnología de virtualización ligera cuyo elemento básico es la utilización de contenedores en vez de máquinas virtuales y cuyo objetivo principal es el despliegue de aplicaciones encapsuladas en dichos contenedores.

Dicho de esta manera puede parecer que no es más que otra tecnología de virtualización, pero para entender mejor cómo ha surgido esta tecnología y comprender las ventajas que aporta debemos echar un poco la vista atrás y conocer la evolución en el despliegue de aplicaciones.

## Evolución del despliegue de aplicaciones

En esa evolución nos podemos encontrar, de manera general y simplificada, con tres grandes pasos: arquitectura de un único servidor, virtualización y contenedores, que pasamos a describir a continuación haciendo especial hincapié en sus ventajas e inconvenientes.

**a) Arquitectura de servidor físico único**

Inicialmente, allá por la prehistoria de la informática, las aplicaciones se desplegaban en un único servidor siguiendo un esquema similar al que podemos ver en la imagen:

!!!info inline "Arquitectura de Servidor Físico"
    <figure>
      <img src="../assets/Servidor_Fisico.png">
      <figcaption>Arquitectura de Servidor Físico</figcaption>
    </figure>

Este enfoque tuvo su momento pero tenía varias limitaciones:

- Era un enfoque de costes elevados porque era necesaria un máquina de precio elevado.
- El despliegue de aplicaciones era un proceso lento que podía suponer en algunos casos una parada del servicio.
- El escalado de las aplicaciones era costoso y complicado. Si nuestra máquina llegaba un momento que se quedaba corta no había más remedio que sustituirla por otra más potente.
- La migración a otro sistema era un proceso complicado. La configuración del nuevo servidor, de su sistema operativo y de todas las dependencias tenía que ser compatible. Esto era algo complicado de gestionar y en algunos casos difícil de conseguir.
- En muchos momentos, aquellos en los que el servidor no se utilizaba aprovechando su potencia, se estaban desperdiciando recursos.
- Había mucha dependencia del fabricante del servidor.

**b) Virtualización**

Con el tiempo, y para superar las limitaciones del modelo de un único servidor, la tecnología evolucionó hacia servidores con características de virtualización. De una manera simplificada podríamos decir que para desplegar aplicaciones nos encontrábamos con arquitecturas similares a las siguientes:

!!!info "Arquitecturas basadas en virtualización"
    <figure>
      <img src="../assets/Virtual_Machine.png" style="max-width: 600px">
      <figcaption>Arquitecturas basadas en virtualización</figcaption>
    </figure>

Estos enfoques tenían una serie de beneficios que derivaban principalmente de superar las limitaciones del modelo de servidor único. A saber:

- Hay un mejor aprovechamiento de los recursos. Un servidor grande y potente se puede compartir entre distintas aplicaciones.
- Los procesos de migración y escalado no son tan dolorosos, simplemente le doy más recursos a la máquina virtual dentro de mi servidor o bien muevo la máquina virtual a un nuevo servidor, propio o en la nube, más potente y que también tenga características de virtualización.
- Esto además hizo que aparecieran nuevos modelos de negocio en la nube que nos permiten en cada momento tener y dimensionar las máquinas virtuales según nuestras necesidades y pagar únicamente por esas necesidades.

No obstante este enfoque también tiene algunos inconvenientes:

- Todas las máquinas virtuales siguen teniendo su propia memoria RAM, su almacenamiento y su CPU que será aprovechada al máximo...o no.
- Para arrancar las máquinas virtuales tenemos que arrancar su sistema operativo al completo.
- La portabilidad no está garantizada al 100%.

**c) Contenedores**

E siguiente paso en la evolución, fue la aparición de los contenedores, eso que anteriormente hemos llamado "máquinas virtuales ligeras" . Su arquitectura general se puede ver en la imagen.

!!!info "Entorno de ejecución basado en contenedores"
    <figure>
      <img src="../assets/Entorno_Docker.png" style="max-width: 600px">
      <figcaption>Entorno de ejecución basado en contenedores</figcaption>
    </figure>

Y sus principales características son las siguientes:

- Los contenedores utilizan el mismo Kernel Linux que la máquina física en la que se ejecutan gracias a la estandarización de los Kernel y a características como los Cgroups y los Namespaces. Esto elimina la sobrecarga que en las máquinas virtuales suponía la carga total del sistema operativo invitado.
- Me permiten aislar las distintas aplicaciones que tenga en los distintos contenedores (salvo que yo estime que deban comunicarse).
- Facilitan la distribución de las aplicaciones ya que éstas se empaquetan junto con sus dependencias y pueden ser ejecutadas posteriormente en cualquier sistema en el que se pueda lanzar el contenedor en cuestión.
- Se puede pensar que se añade una capa adicional, el Docker Engine, pero esta capa apenas añade sobrecarga debido a que se hace uso del mismo Kernel.

Este enfoque por lo tanto aporta los siguientes beneficios:

- Una mayor velocidad de arranque, ya que prescindimos de la carga de un sistema operativo invitado. Estamos hablando de apenas segundos para arrancar un contenedor (a veces menos).
- Un gran portabilidad, ya que los contenedores empaquetan tanto las aplicaciones como sus dependencias de tal manera que pueden moverse a cualquier sistema en el que tengamos instalados el Docker Engine, y este se puede ser instalado en casi todos, por no decir todos.
- Una mayor eficiencia, ya que hay un mejor aprovechamiento de los recursos. Ya no tenemos que reservar recursos, como hacemos con las máquinas virtuales, sin saber si serán aprovechados al máximo o no.

Aunque, como todo en esta vida, la tecnología de contenedores tiene algún inconveniente:

- Los contenedores son más frágiles que las máquinas virtuales y en ocasiones se quedan en un estado desde el que no podemos recuperarlos. No es algo frecuente pero ocurre, y para eso hay soluciones como la orquestación de contenedores que es algo que queda fuera del alcance de este curso que está más orientado a desarrolladores que a sistemas.

## Terminología básica

Una vez hemos hecho una pequeña introducción al mundo de los contenedores, y para acabar el apartado, vamos a proceder a definir una serie de términos relacionados, que usaremos con frecuencia a lo largo del curso:

!!! abstract "Imagen"
    Una imagen es una **plantilla**, ya sea de una aplicación o de un sistema, que podremos utilizar como base para la ejecución posterior de nuestras aplicaciones (contenedores). 
    
    Si queremos una descripción más técnica y detallada diremos que es un **archivo comprimido** en el que, partiendo de un sistema base, se han ido añadiendo capas cada una de las cuáles contiene elementos necesarios para poder ejecutar una aplicación o sistema. **No tiene estado** y no cambia salvo que generemos una nueva versión o una imagen derivada de la misma.

!!! abstract "Contenedor"
    Un contenedor es una **imagen en ejecución**, con una serie de instrucciones y variables de entorno determinadas. **Tiene estado** y podemos modificarlo. Estos cambios no afectan a la imagen que ha servido de base.

!!! abstract "Repositorio"
    Un repositorio es un **almacén de imágenes**, normalmente en la nube, desde el cuál podemos descargar distintas versiones de una misma imagen para poder empezar a construir nuestras aplicaciones basadas en contenedores.

!!! abstract "Docker"
    Docker es una plataforma, mayormente opensource, para el desarrollo, empaquetado y distribución de aplicaciones, creada por la empresa *Docker Inc* (anteriomente *Dot Cloud Inc*). Es un término que se suele utilizar indistintamente al del *Docker Engine*.

!!! abstract "Docker engine"
    El *docker engine* es la aplicación cliente-servidor que consta de tres componentes: 

    * un servicio *docker* para la ejecución de los contenedores
    * una API para que otras aplicaciones puedan comunicarse con ese servicio
    * una aplicación de línea de comandos *docker cli* (*command line interface*) que se usa para gestionar los distintos elementos (contenedores, imágenes, redes, volúmenes, etc..)

!!! abstract "Docker Hub"
    *Docker Hub* es un registro de repositorios de imágenes de la empresa *Docker Inc*, accesible a través de la URL [https://hub.docker.com/](https://hub.docker.com/).