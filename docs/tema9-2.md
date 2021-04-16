# 9.2.- Ejemplo práctico de Jenkins y Docker

Para finalizar,  vamos a realizar una práctica que cubra muchos de los aspectos que hemos visto a lo largo del curso y que introduzca ideas y conceptos relacionados con la unión de los contenedores y de los procesos de integración (CI), entrega (CD) y despliegue continuo (CDep). 

El proceso que vamos a describir no es exactamente el correspondiente a los conceptos mencionados anteriormente, sino uno diseñado específicamente para las clases de módulos profesionales de desarrollo.

Nuestro objetivo va a ser un automatizar el siguiente proceso mediante un *pipeline* de Jenkins,

!!!info "Pipeline de Jenkins - Pseudo CDep"
    <figure>
      <img src="../assets/Jenkins_Pipelin.png">
      <figcaption>Pipeline de Jenkins - Pseudo CDep</figcaption>
    </figure>

con los siguiente pasos:

1. **BUILD**: Descargar el código del ejemplo y compilarlo.
2. **TESTS**: Realizar los Test. En este ejemplo se deja vacío para no complicar más las cosas. Si nuestro proyecto tuviera pruebas sería el lugar para ejecutarlas.
3. **CONTAINER**: Clonaremos el repositorio de Github donde tenemos el `Dockerfile` y tras añadir el fichero resultante de la compilación se hará un *git push*. Este repositorio estará conectado a su vez con un repositorio de DockerHub que será el que haga el *build* de la imagen de manera automática.
4. **DEPLOY**: Pondremos el archivo en un contenedor Tomcat al que accederemos por SSH.

Para ello vamos a necesitar los siguiente recursos:

* Un contenedor de la imagen `jenkins/jenkins:lts`, en el que instalaremos la herramienta Maven y los plugins Maven Integration y SSH Pipeline Steps. Así crearemos el siguiente contenedor:

!!! success "Lanzar contenedor Jenkins"
    ```bash
    docker run -d --name serverJenkins -p 9393:8080 -p 50001:50000 jenkins/jenkins:lts
    ```

* Un contenedor de la imagen `jperjim398/tomcatcursocep`, que contiene un servidor Tomcat y un servidor SSH que usaremos para conectarnos y desplegar la aplicación en el servidor Tomcat. Lanzaremos este contenedor con la siguiente orden:

!!! success "Lanzar contenedor Tomcat SSH"
    ```bash
    docker run -d -it --name cep -p 9292:8080 -p 2222:22 jperjim398/tomcatcursocep
    ```

* El código de la aplicación del ejemplo, que puede encontrar en [https://github.com/jleetutorial/maven-project](https://github.com/jleetutorial/maven-project)

* Un repositorio en github que tenga inicialmente el siguiente `Dockerfile`.

!!! success "Dockerfile para la construcción de la imagen"
    ```Dockerfile
    FROM tomcat:9.0.39-jdk11
    COPY *.war /user/local/tomcat/webapps
    ```

* Un repositorio en DockerHub conectado  con el repositorio anterior.
* El fichero `Jenkinsfile` que define el Pipeline es el siguiente:

!!! success "Jenkinsfile del pipeline"
    ```Jenkinsfile
    def produccion = [:]
                produccion.name = 'curso'
                produccion.host = '172.17.0.2'
                produccion.user = 'root'
                produccion.password = 'root'
                produccion.allowAnyHosts = true
    pipeline {
        agent any
        stages {
            stage('Build') {
                steps {
                    // Create code dir
                    dir('codigo') {
                        // Get some code from a GitHub repository
                        git 'https://github.com/jleetutorial/maven-project.git'

                        // Run Maven on a Unix agent.
                        sh "mvn clean package -DskipTests"

                        // To run Maven on a Windows agent, use
                        // bat "mvn -Dmaven.test.failure.ignore=true clean package"    
                    }
                    
                }
                post {
                    // If Maven was able to run the tests, even if some of the test
                    // failed, record the test results and archive the jar file.
                    success {
                        archiveArtifacts 'codigo/webapp/target/*.war'
                    }
                }
            }
            stage('Test') {
                steps {
                    sh "echo 'Realización de algunos Test'"    
                }
                
            }
            stage('Container') {
                steps {
                    sh "echo 'Creo el contenedor'"
                    dir('contenedor') {
                        withCredentials([usernamePassword(credentialsId: 'gitprueba', passwordVariable: 'password', usernameVariable: 'usuario')]) {
                            git 'https://github.com/pruebainf/dockerimage-from-jenkins-pipelin.git'
                            sh('''
                                cp ../codigo/webapp/target/webapp.war .
                                git add .
                                git config --global user.email 'testif@iesalixar.org'
                                git config --global user.name  'testif@iesalixar.org'
                                git commit -m 'Desde Jenkinsfile'
                                git config --local credential.helper "!f() { echo username=\\$usuario; echo password=\\$password; }; f"
                                git push origin master
                            ''')
                        }
                    }
                    
                }
            }
            stage('Deploy') {
                steps {
                    sh "echo 'Desplegando'"    
                    sshPut remote: produccion, from: 'codigo/webapp/target/webapp.war' , into: '/usr/local/tomcat/webapps/'    
                }
                
            }
        }
    }
    ```

En el siguiente vídeo se describe el proceso de ejecución del pipeline Jenkins anterior:

!!! info "Ejecución del pipeline Jenkins"
    <iframe width="100%" style="aspect-ratio: 16 / 9;" src="https://www.youtube.com/embed/qEZx4mQzTd4" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    
    Enlace: [https://www.youtube.com/watch?v=qEZx4mQzTd4](https://www.youtube.com/watch?v=qEZx4mQzTd4)

