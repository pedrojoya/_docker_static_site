# 9.1.- Conceptos básicos de integración y despliegue continuo

Para acabar el curso vamos a montar un pseudo flujo de despliegue continuo mediante un servidor Jenkins que lanzaremos desde un contenedor. 

De esta manera mostraremos otro entorno donde los contenedores son útiles y abriremos el campo de conocimientos demostrando la utilidad de este tipo de conceptos dentro del mundo del desarrollo de software actual.

Jenkins es un servidor de automatización opensource que nos va a permitir automatizar ciertas partes del proceso de desarrollo de software. 

Proporciona un sistemas de plugins muy completo que permite integrar en cada una de estas fases distintos tipos de herramientas, como por ejemplo control de versiones, construcción de software, testing, etc.

!!!info "Servidor de automatización Jenkins"
    <figure>
      <img src="../assets/jenkins.png">
      <figcaption>Servidor de automatización Jenkins</figcaption>
    </figure>

Una vez hayamos levantado un servicio de Jenkins definiremos un flujo automático o *pipeline* con las siguientes fases:

1. Fase 1: Descarga de un código desde un repositorio de GitHub y construcción (build)
2. Fase 2: Simulación de Testing.
3. Fase 3: Generación de una nueva imagen docker que contenga ese código a través de un fichero `Dockerfile` que esté conectado con DockerHub.
4. Fase 4: Despliegue del código en un servidor que también será un contenedor.

Este flujo es un realizado expresamente para este curso, que no responde exactamente a las definiciones generales que veremos en el próximo apartado, pero nos va a servir para repasar conceptos que hemos estudiado a la vez que nos introducimos en el mundo de la automatización.

Cuando hablamos de **integración continua** (*continuos integration*, CI) nos referimos a la práctica dentro del desarrollo de software que consiste en hacer de manera automática integraciones dentro de un proyecto.  Cuando hablamos de integración nos estamos refiriendo a la compilación y a la realización de las pruebas necesarias para comprobar la corrección de software. 

El objetivo es que este proces de integración continua sea automático, en el sentido de que se inicie automáticamente tras realizar cambios en el proyecto y subir dichos cambios al repositorio del proyecto en un sistema de control de versiones, como por ejemplo Git.

Debemos hacer  especial hincapié en la automatización de los test para que se compruebe automáticamente que no hay errores en el proyecto tras realizar los cambios. Dentro de los tests automatizados incluiremos distintos tipos de pruebas: unitarias (U), de integración (I) , de aceptación (A), etc.

Una evolución del proceso de integración continua corresponede a lo que se conoce como **generación de versiones continua** (*continuos delivery*, CD), que pretende automatizar la generación de nuevas versiones de nuestro software en cualquier momento, de una manera segura y confible.

Así, a las operaciones correspondientes a la integración continua, se añade el proceso de generación de versiones (*releases*) del software, aunque el despliegue de dichas versiones al usuario final seguirá siendo manual (*staging deployment*).

Esto implica que tras el proceso de *build* y testing, se realizará un proceso de generación de la nueva versión. Para realizar el proceso de despliegue de la aplicación aún será necesario la intervención humana, que deberá confirmar dicho despliegue.

La evolución natural de lo anterior es conseguir que el proceso de despligue deje de ser manual y esté automatizado, a lo que se conoce como **despliegue continuo** (*continuos deployment*, CDep). De esta manera, tras realizar el proceso de integración continuam correctamente, se generá una nueva versión del software que será desplegada automáticamente a producción sin que sea necesaria la intervención humana, acelerando todo el proceso.

!!!info "Conceptos de integración y despligue continuo"
    <figure>
      <img src="../assets/CI_CD_CDep.png">
      <figcaption>Conceptos de integración y despligue continuo</figcaption>
    </figure>
