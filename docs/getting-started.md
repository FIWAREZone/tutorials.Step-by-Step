[![FIWARE Core Context Management](https://nexus.lab.fiware.org/repository/raw/public/badges/chapters/core.svg)](https://github.com/FIWARE/catalogue/blob/master/core/README.md)
[![NGSI v2](https://img.shields.io/badge/NGSI-v2-blue.svg)](https://fiware-ges.github.io/orion/api/v2/stable/)

**Description:** Este es un Tutorial Introductorio a la Plataforma FIWARE. Empezaremos con los datos del buscador de tiendas de una cadena de supermercados 
y crearemos una aplicación muy sencilla _"Powered by FIWARE"_ pasando la dirección y ubicación de cada tienda 
como datos de contexto al agente de contexto FIWARE.

El tutorial usa comandos [cUrl](https://ec.haxx.se/) en el, pero también está disponible como
[documentación Postman](https://fiware.github.io/tutorials.Getting-Started/)

[![Ejecutar en Postman](https://run.pstmn.io/button.svg)](https://app.getpostman.com/run-collection/d6671a59a7e892629d2b)

<hr class="core"/>

# Arquitectura
Nuestra aplicación de demostración sólo hará uso de un componente FIWARE - el
[Orion Context Broker](https://fiware-orion.readthedocs.io/en/latest/). El uso del Orion Context Broker es suficiente
para que una aplicación califique como _"Powered by FIWARE"_.

Actualmente, el Orion Context Broker se basa en la tecnología de código abierto [MongoDB](https://www.mongodb.com/) para la persistencia de los datos de contexto que contiene. Por lo tanto, la Arquitectura constará de dos elementos:

-   El [Orion Context Broker](https://fiware-orion.readthedocs.io/en/latest/) quien recibirá las peticiones usando
    [NGSI](https://fiware.github.io/specifications/OpenAPI/ngsiv2)
-   La base de datos que hay por debajo [MongoDB](https://www.mongodb.com/) :
    -   Utilizada por el Orion Context Broker para mantener información de datos de contexto como los datos de las entidades, suscripciones y
        registros

Dado que todas las interacciones entre los dos elementos son iniciadas por peticiones HTTP, estos pueden ser contenedorizado sy correr desde los puertos expuestos.

![](https://fiware.github.io/tutorials.Getting-Started/img/architecture.png)

## Prerequisitos

### Docker

Para mantener las cosas simples, ambos componentes se ejecutarán usando [Docker](https://www.docker.com). **Docker** es una tecnología de contenedores que permite aislar diferentes componentes en sus respectivos entornos.

- Para instalar Docker en Windows siga las instrucciones [aquí](https://docs.docker.com/docker-for-windows/)
- Para instalar Docker en Mac siga las instrucciones [aquí](https://docs.docker.com/docker-for-mac/)
- Para instalar Docker en Linux siga las instrucciones [aquí](https://docs.docker.com/install/)

### Docker Compose (Opcional)

**Docker Compose** es una herramienta para definir y ejecutar aplicaciones Docker multi-contenedor. A
Se utiliza el [archivo YAML](https://raw.githubusercontent.com/Fiware/tutorials.Getting-Started/master/docker-compose.yml) para configurar los servicios requeridos para la aplicación. Esto significa que todos los servicios de los contenedores pueden ser lanzados en un solo comando. Docker Compose se instala de forma predeterminada como parte de Docker para Windows y Docker para Mac, sin embargo los usuarios de Linux
tendrá que seguir las instrucciones que se encuentran [aquí](https://docs.docker.com/compose/install/)

Puede comprobar sus versiones actuales de **Docker** y **Docker Compose** usando los siguientes comandos:

```bash
docker-compose -v
docker version
```
Por favor, asegúrese de que está utilizando la versión 18.03 o superior de Docker y la versión 1.21 o superior de Docker Compose y actualícela si es necesario.

## Iniciando los contenedores

### Opcion 1) Usando los comandos de Docker directamente

Primero haga pull de las imágenes necesarias de Docker Hub y cree una red para que nuestros contenedores se conecten a ella:

```bash
docker pull mongo:3.6
docker pull fiware/orion
docker network create fiware_default
```

Para iniciar un contenedor Docker corriendo una base de datos [MongoDB](https://www.mongodb.com/) conectada a la red tenemos que ejecutar el siguiente comando:

```bash
docker run -d --name=mongo-db --network=fiware_default \
  --expose=27017 mongo:3.6 --bind_ip_all --smallfiles
```

El Orion Context Broker se puede iniciar y conectar a la red empleando el siguiente comando:

```bash
docker run -d --name fiware-orion -h orion --network=fiware_default \
  -p 1026:1026  fiware/orion -dbhost mongo-db
```

> **Nota:** Si desea limpiar el entorno y empezar de nuevo puede hacerlo con los siguientes comandos:
>
> ```
> docker stop fiware-orion
> docker rm fiware-orion
> docker stop mongo-db
> docker rm mongo-db
> docker network rm fiware_default
> ```

### Opcion 2) Usando Docker Compose

Todos los servicios pueden ser inicializados desde la línea de comandos usando el comando "docker-compose". Por favor, clone el repositorio y cree las imágenes necesarias ejecutando los comandos como se muestra:

```bash
git clone https://github.com/FIWARE/tutorials.Getting-Started.git
cd tutorials.Getting-Started

docker-compose -p fiware up -d
```

> **Nota:** Si desea limpiar el entorno y empezar de nuevo puede hacerlo con los siguientes comandos:
>
> ```
> docker-compose -p fiware down
> ```


## Creando tu primera Powered by FIWARE app

### Comprobando la salud del servicio

Puede comprobar si el Orion Context Broker se está ejecutando haciendo una petición HTTP al puerto expuesto:

#### 1 Peticion:

```bash
curl -X GET \
  'http://localhost:1026/version'
```

#### Respuesta:

La respuesta debe parecerse a la siguiente:

```json
{
    "orion": {
        "version": "1.12.0-next",
        "uptime": "0 d, 0 h, 3 m, 21 s",
        "git_hash": "e2ff1a8d9515ade24cf8d4b90d27af7a616c7725",
        "compile_time": "Wed Apr 4 19:08:02 UTC 2018",
        "compiled_by": "root",
        "compiled_in": "2f4a69bdc191",
        "release_date": "Wed Apr 4 19:08:02 UTC 2018",
        "doc": "https://fiware-orion.readthedocs.org/en/master/"
    }
}
```

 **Que pasa si obtengo `Failed to connect to localhost port 1026: Connection refused` como respuesta?**
>
> Si recibes una respuesta `Connection refused`, el Orion Content Broker no se puede encontrar donde se espera para este tutorial - necesitas sustituir la URL y el puerto en cada comando cUrl con la dirección IP correcta. Todos los comandos cUrl de este tutorial suponen que Orion está disponible en `localhost:1026`
> Prueba las siguientes soluciones:
>
> -   Para comprobar si los contenedores de docker están corriendo pruebe lo siguiente
>
> ```
> docker ps
> ```
>
> Tendría que ver los dos contenedores corriendo. Si Orion no está corriendo, puede reiniciar los contenedores si es necesario.
> Este comando también mostrará la información de los puertos abiertos
>
> -   Si tiene instalado  [`docker-machine`](https://docs.docker.com/machine/) y
>     [Virtual Box](https://www.virtualbox.org/), el contenedor de docker de orio quizás corra en otra direccion IP - Con el siguiente comando podrá obtener la IP del host:
>
> ```
> curl -X GET \
>  'http://$(docker-machine ip default):1026/version'
> ```
>
> De forma alternativa, ejecute todos los comandos cUrl desde la red de los contenedores:
>
> ```
> docker run --network fiware_default --rm appropriate/curl -s \
>  -X GET 'http://orion:1026/version'
> ```

## Creando datos de contexto

En el fondo, FIWARE es un sistema de gestión de información contextual, por lo que permite añadir algunos datos contextuales al sistema mediante
creando dos nuevas entidades (tiendas en **Berlin**). Cualquier entidad debe tener un `id` y atributos de tipo `type`, los atributos adicionales son opcionales y dependerán del sistema que se describa. Cada atributo adicional debe tener también un tipo definido y un atributo de valor.

#### 2 Petición:

```bash
curl -iX POST \
  'http://localhost:1026/v2/entities' \
  -H 'Content-Type: application/json' \
  -d '
{
    "id": "urn:ngsi-ld:Store:001",
    "type": "Store",
    "address": {
        "type": "PostalAddress",
        "value": {
            "streetAddress": "Bornholmer Straße 65",
            "addressRegion": "Berlin",
            "addressLocality": "Prenzlauer Berg",
            "postalCode": "10439"
        },
        "metadata": {
            "verified": {
                "value": true,
                "type": "Boolean"
            }
        }
    },
    "location": {
        "type": "geo:json",
        "value": {
             "type": "Point",
             "coordinates": [13.3986, 52.5547]
        }
    },
    "name": {
        "type": "Text",
        "value": "Bösebrücke Einkauf"
    }
}'
```

#### 3 Petición:

Cada entidad siguiente debe tener un "id" único para el "tipo" dado.

```bash
curl -iX POST \
  'http://localhost:1026/v2/entities' \
  -H 'Content-Type: application/json' \
  -d '
{
    "type": "Store",
    "id": "urn:ngsi-ld:Store:002",
    "address": {
        "type": "PostalAddress",
        "value": {
            "streetAddress": "Friedrichstraße 44",
            "addressRegion": "Berlin",
            "addressLocality": "Kreuzberg",
            "postalCode": "10969"
        },
        "metadata": {
            "verified": {
                "value": true,
                "type": "Boolean"
            }
        }
    },
    "location": {
        "type": "geo:json",
        "value": {
             "type": "Point",
             "coordinates": [13.3903, 52.5075]
        }
    },
    "name": {
        "type": "Text",
        "value": "Checkpoint Markt"
    }
}'
```

### Directrices de los modelos de datos

Aunque cada entidad de datos dentro de su contexto variará según su caso de uso, la estructura común dentro de cada entidad de datos debe ser estandarizada para promover la reutilización. Las directrices completas de los modelos de datos de FIWARE se pueden encontrar
[aquí](https://fiware-datamodels.readthedocs.io/en/latest/guidelines/index.html). Este tutorial demuestra el uso
de las siguientes recomendaciones:

#### Todos los terminos están definidos en inglés americano

Aunque los campos `value` de los datos de contexto pueden estar en cualquier idioma, todos los atributos y tipos se escriben usando el idioma inglés.

####  Los nombres de los tipos de entidad deben comenzar con una letra mayúscula
En este caso sólo tenemos un tipo de entidad - **Store**

#### Los ID de las entidades deben ser una URN siguiendo las directrices de NGSI-LD

NGSI-LD ha sido recientemente publicado como una [especificación](https://www.etsi.org/deliver/etsi_gs/CIM/001_099/009/01.01.01_60/gs_CIM009v010101p.pdf)
completa de la ETSI, la propuesta es que cada `id` es una URN que sigue un formato estándar: `urn:ngsi-ld:<entity-type>:<entity-id>`. Esto significará 
que cada `id` en el sistema será único.

#### Los nombres de los tipos de datos deben reutilizar los tipos de datos de schema.org siempre que sea posible

[Schema.org](http://schema.org/) es una iniciativa para crear esquemas de datos estructurados comunes. Para promover la reutilización hemos utilizado
 deliberadamente el los tipos de atributo [`Text`](http://schema.org/PostalAddress) y
[`PostalAddress`](http://schema.org/PostalAddress) con nuestra entidad **Store** . Otros estándares existentes como [Open311](http://www.open311.org/) 
(para el seguimiento de problemas de ciudad) o [Datex II](https://datex2.eu/) (para sistemas de tranposte) tambine pueden ser usado, pero la clave es 
comprobar la existencia de el mismo atributo en modelos de datos existentes y reusarlo.

#### Uso de la sintaxis camel case para el nombre de los atributos

Los atributos `streetAddress`, `addressRegion`, `addressLocality` y`postalCode` son todos ejemplo del uso de camel case.

#### Location information should be defined using `address` and `location` attributes

-   Hemos usado un atributo `address` para las direcciones civiles definido por [schema.org](http://schema.org/)
-   Hemos usado el atributo `location` para las coordenadas geográficas.

#### Uso de GeoJSON para codificar propiedades geoespaciales

[GeoJSON](http://geojson.org) es un formato estándar abierto diseñado para representar elementos geográficos simples. El atributo 
`location` ha sido condificado como un elemento geoJSON de tipo punto `Point`.

### Metadatos de atributos

Los metadatos son _"datos acerca de los datos"_, son información adicional usada para describir propiedades de los valores de los atributos 
como precisión, proveedor del dato, o marca temporal. Existen diveros accuracy, provider, or a timestamp. Ya existen varios atributos de 
metadatos predefinidos y sus nombres están reservados:

-   `dateCreated` (tipo: DateTime): Fecha de creación del atributo como un string ISO 8601.
-   `dateModified` (tipo: DateTime): fecha de modificación del atributo como un string ISO 8601.
-   `previousValue` (tipo: cualquiera): sólo en las notificaciones. El valor anterior
-   `actionType` (tipo: Text): sólo en las notificaciones.

Un metadato que se puede encontrar en atributos de tipo `address`. es `verified` que indica cuando la dirección se ha confirmado

## Consultando datos de contexto

Una aplicación puede solicitar datos de contexto haciendo peticiones HTTP al Orion Context Broker. La interfaz NGSI existente nos permite hacer consultas complejas y filtrar los resultados.

En este momento, para la demostración del buscador de tiendas, todos los datos de contexto se han añadido directamente a través de peticiones HTTP, sin embargo, en una solución inteligente más compleja, el Orion Context Broker también puede recuperar el contexto directamente de los sensores adjuntos asociados a cada entidad.

Aquí hay algunos ejemplos, en cada caso se ha utilizado el parámetro de consulta `options=keyValues` para acortar las respuestas quitando los elementos de tipo de cada atributo

### Obteniendo datos de la entidad por ID

Este ejemplo devuelve los datos de `urn:ngsi-ld:Store:001`

#### 4 Petición:

```console
curl -G -X GET \
   'http://localhost:1026/v2/entities/urn:ngsi-ld:Store:001' \
   -d 'options=keyValues'
```

#### Respuesta:

Debido al uso del modificador `options=keyValues`, la respuesta consta solo de un JSON sin los attributos `type` y sin elementos `metadata`.

```json
{
    "id": "urn:ngsi-ld:Store:001",
    "type": "Store",
    "address": {
        "streetAddress": "Bornholmer Straße 65",
        "addressRegion": "Berlin",
        "addressLocality": "Prenzlauer Berg",
        "postalCode": "10439"
    },
    "location": {
        "type": "Point",
        "coordinates": [13.3986, 52.5547]
    },
    "name": "Bösebrücke Einkauf"
}
```

### OObteniendo datos de la entidad por tipo

Este ejemplo devuelve el dato de todas las entidades `Store` con sus datos de contexto. El parámetro `type` limita la 
respuesta sólo a las entidades de tipo tienda

#### 5 Petición:

```bash
curl -G -X GET \
    'http://localhost:1026/v2/entities' \
    -d 'type=Store' \
    -d 'options=keyValues'
```

#### Respuesta:

Debido al uso del modificador `options=keyValues`, la respuesta consta solo de un JSON sin los attributos `type` y sin elementos `metadata`.

```json
[
    {
        "id": "urn:ngsi-ld:Store:001",
        "type": "Store",
        "address": {
            "streetAddress": "Bornholmer Straße 65",
            "addressRegion": "Berlin",
            "addressLocality": "Prenzlauer Berg",
            "postalCode": "10439"
        },
        "location": {
            "type": "Point",
            "coordinates": [13.3986, 52.5547]
        },
        "name": "Bose Brucke Einkauf"
    },
    {
        "id": "urn:ngsi-ld:Store:002",
        "type": "Store",
        "address": {
            "streetAddress": "Friedrichstraße 44",
            "addressRegion": "Berlin",
            "addressLocality": "Kreuzberg",
            "postalCode": "10969"
        },
        "location": {
            "type": "Point",
            "coordinates": [13.3903, 52.5075]
        },
        "name": "Checkpoint Markt"
    }
]
```

### Filtrando datos de contexto comparando los valores de los atributos

Este ejemplo devuelve todos las tiendas con el atributo `name` con el valor _Checkpoint Markt_. Se puede filtrar usando el parámetro `q` - si una cadena tiene espacios en el, se puede condificar en URL y mantenerse entre comillas simples `'` = `%27`

#### 6 Petición:

```bash
curl -G -X GET \
    'http://localhost:1026/v2/entities' \
    -d 'type=Store' \
    -d 'q=name==%27Checkpoint%20Markt%27' \
    -d 'options=keyValues'
```

#### Respuesta:

Debido al uso del modificador `options=keyValues`, la respuesta consta solo de un JSON sin los attributos `type` y sin elementos `metadata`.

```json
[
    {
        "id": "urn:ngsi-ld:Store:002",
        "type": "Store",
        "address": {
            "streetAddress": "Friedrichstraße 44",
            "addressRegion": "Berlin",
            "addressLocality": "Kreuzberg",
            "postalCode": "10969"
        },
        "location": {
            "type": "Point",
            "coordinates": [13.3903, 52.5075]
        },
        "name": "Checkpoint Markt"
    }
]
```

### Filtrar datos de contexto comparando valores de un sub-atributo 

Este ejemplo devuelve todas las tiendas encontradas en Kreuzberg District.

El filtrado se puede hacer usando el parámetro `q` - los sub-atributos Los sub-atributos se apuntan usando la sintaxis 
de puntos, por ejemplo, `address.addressLocality`

#### 7 Petición:

```bash
curl -G -X GET \
    'http://localhost:1026/v2/entities' \
    -d 'type=Store' \
    -d 'q=address.addressLocality==Kreuzberg' \
    -d 'options=keyValues'
```

#### Respuesta:

Debido al uso del modificador `options=keyValues`, la respuesta consta solo de un JSON sin los attributos `type` y sin elementos `metadata`.

```json
[
    {
        "id": "urn:ngsi-ld:Store:002",
        "type": "Store",
        "address": {
            "streetAddress": "Friedrichstraße 44",
            "addressRegion": "Berlin",
            "addressLocality": "Kreuzberg",
            "postalCode": "10969"
        },
        "location": {
            "type": "Point",
            "coordinates": [13.3903, 52.5075]
        },
        "name": "Checkpoint Markt"
    }
]
```

### Filtrar datos de contexto buscando por metadato

Este ejemplo devuelve todos los datos de las entidades `Store` con la dirección verificada.

Las búsquedas por metadatos se pueden usar empleando el parámetro `mq`.

#### 8 Petición:

```bash
curl -G -X GET \
    'http://localhost:1026/v2/entities' \
    -d 'type=Store' \
    -d 'mq=address.verified==true' \
    -d 'options=keyValues'
```

#### Respuesta:

Debido al uso del modificador `options=keyValues`, la respuesta consta solo de un JSON sin los attributos `type` y sin elementos `metadata`.

```json
[
    {
        "id": "urn:ngsi-ld:Store:001",
        "type": "Store",
        "address": {
            "streetAddress": "Bornholmer Straße 65",
            "addressRegion": "Berlin",
            "addressLocality": "Prenzlauer Berg",
            "postalCode": "10439"
        },
        "location": {
            "type": "Point",
            "coordinates": [13.3986, 52.5547]
        },
        "name": "Bösebrücke Einkauf"
    },
    {
        "id": "urn:ngsi-ld:Store:002",
        "type": "Store",
        "address": {
            "streetAddress": "Friedrichstraße 44",
            "addressRegion": "Berlin",
            "addressLocality": "Kreuzberg",
            "postalCode": "10969"
        },
        "location": {
            "type": "Point",
            "coordinates": [13.3903, 52.5075]
        },
        "name": "Checkpoint Markt"
    }
]
```

### Filtrando datos de contexto comparando los valores de un atributo geo:json

Este ejemplo devuelve todas las tiendas a 1.5Km de distancia de **La puerta de Brandenburgo** en **Berlin** (_52.5162N 13.3777W_)

#### 9 etición:

```bash
curl -G -X GET \
  'http://localhost:1026/v2/entities' \
  -d 'type=Store' \
  -d 'georel=near;maxDistance:1500' \
  -d 'geometry=point' \
  -d 'coords=52.5162,13.3777'
```

#### Respuesta:

Debido al uso del modificador `options=keyValues`, la respuesta consta solo de un JSON sin los attributos `type` y sin elementos `metadata`.

```json
[
    {
        "id": "urn:ngsi-ld:Store:002",
        "type": "Store",
        "address": {
            "streetAddress": "Friedrichstraße 44",
            "addressRegion": "Berlin",
            "addressLocality": "Kreuzberg",
            "postalCode": "10969"
        },
        "location": {
            "type": "Point",
            "coordinates": [13.3903, 52.5075]
        },
        "name": "Checkpoint Markt"
    }
]
```

## Proximos pasos

Quieres aprender como añadir más complejidad a tu aplicación añadiendo funcionalidades avanzadas? Puedes encontrarlas leyendo otros tutoriales de esta misma serie

### Desarrollo iterativo

El contexto del ejemplo buscador de tienda es muy simple, se puede ampliar fácilmente para que contenga la totalidad de una gestión stock.

se puede ampliar fácilmente para que contenga el conjunto de un sistema de gestión de existencias pasando el recuento de existencias actual de cada tienda como datos de contexto al sistema, pasando el recuento de existencias actual de cada tienda como datos de contexto al [Orion Context Broker](https://fiware-orion.readthedocs.io/en/latest/).

Hasta aquí, muy sencillo, pero considere los siguientes pasos de esta aplicación Smart:

-   Se pueden crear cuadros de mando en tiempo real para controlar el estado del     stock en cada tienda mediante el componente de visualización
    \[[Wirecloud](https://github.com/FIWARE/catalogue/blob/master/processing/README.md#Wirecloud)\]
-   La disposición actual tanto del almacén como de la tienda podría pasarse al corredor de contexto para que la ubicación del stock pueda visualizarse en un mapa
    \[[Wirecloud](https://github.com/FIWARE/catalogue/blob/master/processing/README.md#Wirecloud)\]
-   Componentes de gestión de usuarios \[[Wilma](https://github.com/FIWARE/catalogue/blob/master/security/README.md#Wilma),
    [AuthZForce](https://github.com/FIWARE/catalogue/blob/master/security/README.md#Authzforce),
    [Keyrock](https://github.com/FIWARE/catalogue/blob/master/security/README.md#Keyrock)\] pueden ser añadidos, así, sólo los directores de las tiendas pueden cambiar el precio de los artículos.
-   Se podría activar una alerta de umbral en el almacén a medida que se venden las mercancías para asegurar que las estanterías no se dejen vacías.
    [publish/subscribe function of [Orion Context Broker](https://fiware-orion.readthedocs.io/en/latest/)]
-   Cada lista generada de artículos a ser colocados en el almacén podría ser calculada para maximizar la eficiencia del reabastecimiento
    \[[Complex Event Processing - CEP](https://github.com/FIWARE/catalogue/blob/master/processing/README.md#new-perseo-incubated)\]
-   Se podría añadir un sensor de movimiento en la entrada para contar el número de clientes
    \[[IDAS](https://github.com/FIWARE/catalogue/blob/master/iot-agents/README.md)\]
-   El sensor de movimiento podría hacer sonar una campana cada vez que un cliente entra
    \[[IDAS](https://github.com/FIWARE/catalogue/blob/master/iot-agents/README.md)\]
-   Se podría añadir una serie de cámaras de vídeo para introducir una señal de vídeo en cada tienda.
    \[[Kurento](https://github.com/FIWARE/catalogue/blob/master/processing/README.md#Kurento)\]
-   Las imágenes de vídeo podrían ser procesadas para reconocer dónde están los clientes dentro de una tienda
    \[[Kurento](https://github.com/FIWARE/catalogue/blob/master/processing/README.md#Kurento)\]
-   Al mantener y procesar los datos históricos dentro del sistema, se puede calcular el tiempo de permanencia y por donde transitan - estableciendo qué áreas de la tienda son de mayor interés [conexión a través de 
    [Cygnus](https://github.com/FIWARE/catalogue/blob/master/core/README.md#Cygnus) o Apache Nifi\]
-   El reconocimiento de patrones de comportamientos inusuales pueden ser utilizados para disparar una alerta para evitar el robo
    \[[Kurento](https://github.com/FIWARE/catalogue/blob/master/processing/README.md#Kurento)\]
-   Los datos sobre el movimiento de las multitudes serían útiles para la investigación científica - los datos sobre el estado de la tienda podrían ser publicados externamente
    \[[extensions to CKAN](https://github.com/FIWARE/catalogue/tree/master/data-publication#extensions-to-ckan)\]

Cada iteración añade valor a la solución a través de los componentes existentes con interfaces estándar y, por lo tanto, minimiza el tiempo de desarrollo.