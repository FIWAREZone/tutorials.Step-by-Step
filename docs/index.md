# FIWARE paso a paso

[![Documentation](https://nexus.lab.fiware.org/repository/raw/public/badges/chapters/documentation.svg)](https://fiware-tutorials.rtfd.io)
[![Support badge](https://nexus.lab.fiware.org/repository/raw/public/badges/stackoverflow/fiware.svg)](https://stackoverflow.com/questions/tagged/fiware)

Esto es una colección de tutoriales para la plataforma FIWARE. Cada tutorial consiste en una serie de ejercicios para demostrar el correcto
uso de componentes FIWARE individualmente, y mostrar el flujo del contexto con una solución Smart simple ya sea conectándose a una serie de 
dispositivos de IoT simulados o manipulando el contexto directamente mediante algun código.

## Créditos

 [![FIWARE Zone logo](https://raw.githubusercontent.com/FIWAREZone/misc/master/Group%400%2C2x.png)](www.fiware.zone)

 Este tutorial ha sido traducido por **FIWARE ZONE**, una iniciativa sin ánimo de lucro entre **Telefónica** y la **Junta de Andalucía** cuyo fin es la divulgación, promoción y difusión de la tecnología *FIWARE*, para hacer crecer el ecosistema y generar conocimiento y oportunidades a los desarrolladores y empresas andaluzas. **FIWARE ZONE**, como *iHub* de 3 estrellas ofrece servicios de alto nivel en formación, mentorización y consultoría de forma totalmente **gratuita**. Si necesitas cualquier tipo de ayuda o quieres contarnos tu proyecto, puedes ponerte en contacto con nostros a través de la direción [fiware.zone@fiware.zone](mailto:fiware.zone@fiware.zone), por nuestro [formulario web](https://fiware.zone/contacto/), en cualquiera de nuestras redes sociales o físicamente en [nuestros centros en Málaga y Sevilla](https://fiware.zone/contacto/)

<a href="http://www.fiware.zone"><img src="https://badgen.net/badge/iHub/3%20stars/yellow" align="left"  height="30" style="border-right-style:solid; border-right-width:0px; border-color:transparent; background: transparent"></a>

[![Twitter](https://raw.githubusercontent.com/FIWAREZone/misc/master/twitter.png)](https://twitter.com/FIWAREZone) [![Linkedin](https://raw.githubusercontent.com/FIWAREZone/misc/master/linkedin.png)](https://www.linkedin.com/company/fiware-zone) [![Instagram](https://raw.githubusercontent.com/FIWAREZone/misc/master/instagram.png)](https://www.instagram.com/fiwarezone/) [![Github](https://raw.githubusercontent.com/FIWAREZone/misc/master/github.png)](https://github.com/FIWAREZone) [![Facebook](https://raw.githubusercontent.com/FIWAREZone/misc/master/facebook.png)](https://www.facebook.com/FIWAREZone/)

<h3>Cómo usar</h3>

Cada tutorial es un ejercicio de aprendizaje autocontenido diseñado para enseñar al desarrollador sobre un 
único aspecto de FIWARE. Se puede encontrar un resumen del objetivo del tutorial en la descripción que aparece 
en la cabecera de cada página. Cada tutorial está asociado con un repositorio GitHub que contiene los archivos 
de configuración necesarios para ejecutar los ejemplos. La mayoría de los tutoriales se basan en conceptos o 
enablers descritos en ejercicios anteriores para crear una solución compleja e inteligente que sea _"powered by FIWARE"_.

Los tutoriales se dividen según los capítulos definidos en el [catálogo FIWARE](https://www.fiware.org/developers/catalogue/) 
y están numerados por orden de dificultad dentro de cada capítulo, de ahí que la introducción a un determinado enabler 
se produzca antes de que se exploren con mayor profundidad todas las capacidades de ese componente.

Se recomienda comenzar con la lectura completa de **Core Context Management: Fundamentos** antes de pasar a otros temas, 
ya que esto le dará una comprensión más completa del papel de los datos de contexto en general. Sin embargo, no es necesario 
seguir todos los tutoriales posteriores en orden - ya que FIWARE es un sistema modular, puede elegir los módulos que más le interesen.

## Prerequisitos

### Docker y Docker Compose <img src="https://www.docker.com/favicon.ico" align="left"  height="30" width="30" style="border-right-style:solid; border-right-width:10px; border-color:transparent; background: transparent">

Cada tutorial ejecuta todos los componentes empleando [Docker](https://www.docker.com). **Docker** **Docker** es una tecnología de contenedores que 
permite aislar diferentes componentes en sus respectivos entornos.

-   Para instalar Docker en Windows siga las instrucciones [aquí](https://docs.docker.com/docker-for-windows/)
-   Para instalar Docker en Mac siga las instrucciones [aquí](https://docs.docker.com/docker-for-mac/)
-   Para instalar Docker en Linux siga las instrucciones [aquí](https://docs.docker.com/install/)

**Docker Compose** es una herramienta para definir y ejecutar aplicaciones Docker multi-contenedor. A
Se utiliza el [archivo YAML](https://raw.githubusercontent.com/Fiware/tutorials.Getting-Started/master/docker-compose.yml) para configurar los 
servicios requeridos para la aplicación. Esto significa que todos los servicios de los contenedores pueden ser lanzados en un solo comando. 
Docker Compose se instala de forma predeterminada como parte de Docker para Windows y Docker para Mac, sin embargo los usuarios de Linux
tendrá que seguir las instrucciones que se encuentran [aquí](https://docs.docker.com/compose/install/)

Puede comprobar sus versiones actuales de **Docker** y **Docker Compose** usando los siguientes comandos:

```console
docker-compose -v
docker version
```

Por favor, asegúrese de que está utilizando la versión 18.03 o superior de Docker y 
la versión 1.21 o superior de Docker Compose y actualícela si es necesario.

Si está usando una distribución Linux con una versión desactualizada de 
docker-compose, los ficheros se pueden istalar como se indica a continuación:

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

### Postman <img src="https://www.getpostman.com/favicon.ico" align="left"  height="30" width="30" style="border-right-style:solid; border-right-width:10px; border-color:transparent; background: transparent">

Los tutoriales que usan peticciones HTTP proveen de una colección para utilizar con la utilidad Postman. Postman es una utilidad de 
pruebas para APIs REST. La herrmienta se puede descargar desde [www.getpostman.com](www.getpostman.com). Todas las colecciones FIWARE de
Postman se pueden descargar directamente desde [Postman API network](https://explore.postman.com/team/3mM5EY6ChBYp9D)

### Cygwin para Windows <img src="https://www.cygwin.com/favicon.ico" align="left"  height="30" width="30" style="border-right-style:solid; border-right-width:10px; border-color:transparent; background: transparent">

En los tutoriales se inician los servicios empleando un script de Bash. Los usuarios de windows deben descargar [cygwin](http://www.cygwin.com/)
para proveer de una línea de comandos similar a las de las distribuciones Linux en Windows.

### Apache Maven <img src="https://maven.apache.org/favicon.ico" align="left"  height="30" width="30" style="border-right-style:solid; border-right-width:10px; border-color:transparent; background: transparent">

[Apache Maven](https://maven.apache.org/download.cgi) es una herramienta de gestión de proyectos software. 
Está basada en el concepto de un Modelo de objeto de proyecto (POM). Puede gestionar la compilación, el 
reporte y la documentación desde un solo lugar. Se puede usar para definir y descargar las dependencias y compilar y empaquetar código Java o Scala en un fichero JAR.

## Lista de tutoriales

<h3 style="box-shadow: 0px 4px 0px 0px #233c68;">Core Context Managment: Fundamentals</h3>

These first tutorials are an introduction to the FIWARE Context Broker, and are an essential first step when learning to
use FIWARE

&nbsp; 101. [Getting Started](getting-started.md)<br/> &nbsp; 102. [Entity Relationships](entity-relationships.md)<br/>
&nbsp; 103. [CRUD Operations](crud-operations.md)<br/> &nbsp; 104. [Context Providers](context-providers.md)<br/>
&nbsp; 105. [Altering the Context Programmatically](accessing-context.md)<br/> &nbsp; 106.
[Subscribing to Changes in Context](subscriptions.md)<br/>

<h3 style="box-shadow: 0px 4px 0px 0px #5dc0cf;">Internet of Things, Robots and third-party systems</h3>

In order to make a context-based system aware of the state of the real world, it will need to access information from
Robots, IoT Sensors or other suppliers of context data such as social media. It is also possible to generate commands
from the context broker to alter the state of real-world objects themselves.

&nbsp; 201. [Introduction to IoT Sensors](iot-sensors.md)<br/> &nbsp; 202.
[Provisioning an IoT Agent](iot-agent.md)<br/> &nbsp; 203. [IoT over MQTT](iot-over-mqtt.md)<br/> &nbsp; 250.
[Introduction to Fast-RTPS and Micro-RTPS](fast-rtps-micro-rtps.md)<br/>

<h3 style="box-shadow: 0px 4px 0px 0px #233c68;">Core Context Management: History Management</h3>

These tutorials show how to manipulate and store context data so it can be used for further processesing

&nbsp; 301. [Persisting Context Data using Apache Flume (MongoDB, MySQL, PostgreSQL)](historic-context-flume.md)<br/>
&nbsp; 302. [Persisting Context Data using Apache NIFI (MongoDB, MySQL, PostgreSQL)](historic-context-nifi.md)<br/>
&nbsp; 303. [Querying Time Series Data (MongoDB)](short-term-history.md)<br/> &nbsp; 304.
[Querying Time Series Data (Crate-DB)](time-series-data.md)<br/>

<h3 style="box-shadow: 0px 4px 0px 0px #ff7059;">Security: Identity Management</h3>

These tutorials show how to create and administer users within an application, and how to restrict access to assets, by
assigning roles and permissions.

&nbsp; 401. [Administrating Users and Organizations](identity-management.md)<br/> &nbsp; 402.
[Managing Roles and Permissions](roles-permissions.md)<br/> &nbsp; 403.
[Securing Application Access](securing-access.md)<br/> &nbsp; 404.
[Securing Microservices with a PEP Proxy](pep-proxy.md)<br/> &nbsp; 405.
[XACML Rules-based Permissions](xacml-access-rules.md)<br/> &nbsp; 406.
[Administrating XACML via a PAP](administrating-xacml.md)<br/>

<h3 style="box-shadow: 0px 4px 0px 0px #88a1ce;">Processing, Analysis and Visualization</h3>

These tutorials show how to create, process, analyze or visualize context information

&nbsp; 501. [Creating Application Mashups](application-mashups.md)<br/> &nbsp; 503.
[Introduction to Media Streams](media-streams.md)<br/> &nbsp; 505.
[Real-time Processing and Big Data Analysis](big-data-analysis.md)<br/>

<h3 style="box-shadow: 0px 4px 0px 0px #233c68;">Core Context Management: Linked Data</h3>

These tutorials show how to create, process, analyze or visualize context information

&nbsp; 601. [Introduction to Linked Data](linked-data.md)<br/> &nbsp; 602.
[Linked Data Relationships and Data Models](relationships-linked-data.md)<br/>
