# 5.5.- iptables en contenedores

En algunas ocasiones querremos montar ciertos entornos compuestos por varias máquinas, en los que sea necesario que seamos nosotros quienes tengamos el control de las reglas *iptables* de las distintas máquinas que los conforman.

Normalmente esto se hace con distintas máquinas virtuales pero en vista de las posibilidades de red que nos ofrecen los contenedores, ¿podríamos montar entornos de ese estilo con contenedores?. Por defecto, esto no es posible en los contenedores, pero podemos conseguirlo de una de estas dos maneras:

- Arrancar el contenedor con el *flag* `--cap-add=NET_ADMIN` para darle al contenedor la capacidad Linux de administrar la red.
- Arrancar el contenedor con el flag `--privileged` para darle capacidades similares  a las del propio *host*. Con esto, se habilita el uso de *iptables*, pero debemos ser cuidadosos con esta característica, ya que podemos tener problemas al levantar ciertas limitaciones a los *cgroups*, por tener demasiados privilegios, por lo que podremos hacer cosas como instalar docker dentro de un contenedor docker.

Independientemente de la opción que elijamos, antes de poder usar *iptables*, y dado las pocas cosas que incluye por defecto un contenedor, deberemos hacer:

- `apt update` para recargar los repositorios que no vienen cargados por defecto.
- `apt install -y iptables` para instalar la herramienta de firewall para Linux.

