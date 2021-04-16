# 6.1.- Imágenes personalizadas

Hasta ahora hemos estado usando contenedores basados en imágenes de terceros que nos descargábamos desde DockerHub. En este apartado vamos a ir un paso mas allá, ya que vamos a crear nuestras propias imágenes personalizadas a partir de la ejecución de una serie de acciones de configuración sobre una imagen existente, para adecuarla a nuestras necesidades.

Como vimos anteriormente, las imágenes descargadas de DockerHub incluyen únicamente lo mínimo para poder trabajar. Por ejemplo en una imagen de Ubuntu ni siquiera vienen cargados los repositorios, por lo que si queremos instalar algo nuevo tenemos que ejecutar antes `apt update`.

Sin embargo, nosotros querremos instalar librerías, incluir código de nuestras aplicaciones  y si es necesario configurar los sistemas o servicios para adecuarlos a nuestras necesidades. En esto consiste la personalización de los contenedores, cuyo objetivo es poder distribuir nuestras imágenes para puedan ser usadas sin problemas en cualquier sistema en el que docker esté instalado.

Así, en nuestra práctica docente, podremos distribuir nuestras propias imágenes al alumnado, con la seguridad de que no van a tener ningún problema para ejecutarlas, gracias a docker. 

En el sentido contrario, cuando el alumnado tenga que entregar un proyecto, podrá hacerlo en un contenedor, de manera que a la hora de corregirlo no tengamos que montar el entorno necesario con todas las dependencias del proyecto.

La creación de imágenes personalizadas proporciona una ventaja final: podremos compartir nuestras imágenes con la comunidad.
