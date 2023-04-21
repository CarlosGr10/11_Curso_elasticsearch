# Curso Elesticserch

¿Qué es Elasticsearch?

La configuración física de elastic se agrupa en un cluster que almacena la configuration física de todos los nodos, entendiendo como nodo cualquier tipo de cómputo que puede correr Elasticsearch. Los nodos agrupan Shards, que son las divisiones que tiene un índice. y operan de manera autónoma.

En un nodo se puede tener un segmento de información principal y las replicas de información de otros segmentos. Algo que es importante tener en cuenta es que las replicas de un segmento en específico no pueden estar almacenadas en el mismo nodo ya que si llega a fallar el nodo, se perdería tambien la replica del mismo.

![](https://i.imgur.com/IOArQc3.png)

- Elastic Search, es una herramienta que te permite realizar búsquedas y analítica en tiempo real.

- Luego de que el documento es almacenado e indexado se encuentra disponible casi en tiempo real, debido a que es una herramienta distribuida.

- Utiliza una interfaz HTTP con documentos JSON, para realizar las consultas.

- El guardado de los documentos es lo más eficiente posible.

- Búsquedas de información en websites y apps.

- Motor de almacenamiento para automatización de casos de negocio.

- Machine Learning, para modelar comportamiento de datos.

- Como un GIS, ya que puede manejar información geoespacial.

- Elastic Search(Lógico) esta formado por indices y documentos, un documento es la información más pequeña que se puede guardar y un indice que es donde se almacenan los documentos.

- Elastic Search(Físico) esta formando por un cluster y el cluster por nodos y los nodos por shards, un shard son las distintas divisiones que tiene un indice, un indice puede estar compuesto por una o varias de estas piezas y estas piezas pueden operar por si solas.

- Las replicas son los backups de las piezas primarias que se guardan en los nodos donde no se encuentra la pieza primaria.

# Poner a correr Elasticsearch

## Paso 1 - Instalar Postman

Para instalar Postman accedes a la URL https://www.postman.com/downloads/ la cual te provee con el instalador de tu sistema operativo.

## Paso 2 - Crear archivo de configuración

Para crear el archivo de configuración te recomiendo utilices un directorio personalizado para este curso, en el cual puedas guardar todos los archivos que usarás. Entonces lo primero será crear este directorio y entrar en él.

Nota: los siguientes comandos se deben ejecutar en una terminal

```
mkdir curso-elastic
```

```
cd curso-elastic
```

Ahora abres un editor de texto (te recomiendo Visual Studio Code) y creas un archivo dentro de este directorio. El nombre del archivo es docker-compose.yml

```
touch docker-compose.yml
```

Una vez creado el archivo, le vas a agregar la siguiente configuración:

```
version: '3.7'
services:
  elasticsearch:
    restart: always
    image: docker.elastic.co/elasticsearch/elasticsearch:7.10.0
    container_name: elasticsearch
    environment:
      - discovery.type=single-node
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data01:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - elastic

volumes:
  data01:
    driver: local

networks:
  elastic:
    driver: bridge
```

Este archivo es basado en las configuraciones recomendadas para correr Elasticsearch con Docker. Esta guía se puede consultar en https://www.elastic.co/guide/en/elasticsearch/reference/7.6/docker.html.

Acá un breve repaso de lo que hace este archivo:

Le dice a Docker que use la imagen de Elasticsearch con versión 7.6.0. Esta es la versión que vas a usar durante el curso.
- Le dice que el nombre del contenedor será es01.
- Le indica unas configuraciones para crear el cluster y el nodo por defecto.
- Le indica que el puerto sobre el que corre Elasticsearch es el 9200. A su vez le dice que exponga ese mismo puerto a tu máquina para que puedas usar el servicio bajo el puerto estándar.

Por último guardamos el archivo.

# Paso 3 - Levantar el servicio
Luego de tener el archivo listo, le dices a Docker que levante el servicio ejecutando el siguiente comando:

```
docker-compose up
```

Este comando se encarga de ejecutar las directivas que especificamos en el archivo dejando el servicio de Elasticsearch funcionando correctamente en el puerto 9200. Esto puede tomar unos segundos mientras se configura el contenedor y se levanta el servicio.

Para comprobar que Elasticsearch ya está corriendo sobre tu máquina, abres Postman y ejecutas un GET sobre la url http://localhost:9200. Si todo salió, correctamente verás un resultado similar a este:


**DELETE**

```python
PUT localhost:9200/usuarios_falsos/_doc/1
```

Creamos una nueva coleccion de datos (POST)

```python
DELETE localhost:9200/usuarios_falsos/_doc/1
```

Borrando un dato

```python
DELETE localhost:9200/usuarios_falsos/
```

Borrando todo el indice / coleccion de datos

# Mapeo de datos

- Para rendimiento optimo , indicar un mapeo explicito

- Para texto: Text y keyword

**TEXT**: Busquedas de texto completas **(match)**

**KEYWORD**: valores exactos **(term)**

## Tipos de datos

TEXTO: text, key

FECHAS: date

NUMEROS: integer, long, float, double

BOOLEANOS: boolean

OBJETOS: object, nested

GEOGRAFICOS: geo_point, geo_shape

### Creando nuestro modelo

```python
PUT localhost:9200/platos

{
    "mappings": {
        "properties": {
            "nombre": {"type": "text"},
            "descripción": {"type": "text"},
            "pedidosUltimaHora": {"type": "integer"},
            "ultimaModificacion": {
                "properties": {
                    "usuario": {"type": "text"},
                    "fecha": {"type": "date"}
                }
            }
        }   
    }
}
```

Creando el modelo


```python
PUT localhost:9200/platos/_mapping

{
    "properties": {
        "estado": {"type": "keyword"}
    }
}
```

Modificando el modelo


```python
PUT localhost:9200/platos/_doc/1

{
    "nombre": "",
    "descripcion": "",
    "estado": "",
    "pedidosUltimaHora": ,
    "ultimaModificacion": {
        "usuario": "",
        "fecha": ""
    }
}
```



```python
PUT localhost:9200/platos/_doc/1
{
  "nombre": "Bowl Picante",
  "descripcion": "Pollo, salsa picante, frijoles, plátano y aguacate",
  "estado": "activo",
  "pedidosUltimaHora": 42,
  "ultimaModificacion": {
    "usuario": "rick@mail.com",
    "fecha": "2020-02-19"
  }
}

PUT localhost:9200/platos/_doc/2
{
  "nombre": "Ensaladísima",
  "descripcion": "Aceitunas, cebolla, queso, tomate, aguacate (saludable)",
  "estado": "activo",
  "pedidosUltimaHora": 0,
  "ultimaModificacion": {
    "usuario": "rick@mail.com",
    "fecha": "2020-01-22"
  }
}
```

# Puntaje

- Que tan bien coincide un documento con la busqueda

- Algoritmo verifica
numero de concurrencias / unicidad de palabras

- Los resultados vienen ordenados por defecto usando dicho puntaje

```python
PUT localhost:9200/platos/_doc/3

{
    "nombre": "Nachos XL",
    "descripcion": "Nachos de carne, pico de gallo",
    "estado": "activo",
    "pedidosUltimaHora": 11 ,
    "ultimaModificacion": {
        "usuario": "jeery@gmail.com",
        "fecha": "2020-01-22"
    }
}
```


```python
GET localhost:9200/platos/_search

{
    "query":{
        "simple_query_string": {"query":"Nachos XL"}
    }
}
```

En el documento vemos la palabra reservada "_score", que entre mas alto , coincide mas con los resultados

```python
{
    "query":{
        "simple_query_string": {
        "query": "gallo",
        "fields": ["nombre^2", "descripcion"]
        }
    }
}
```

Para darle mas peso a una busqueda en un campo utilizamos la sig. estructura "^2" despues de un campo 


# Tipos de cláusulas

**Must, Filter,Should & Must Not**

- Para una consulta:
usar {} (un objeto)

- Para ,as de una consulta
Usar [] (una lista de objetos)

**MUST**<br>

- Funciona como un AND logico
- La consulta debe de aparecer en los documentos retornados
- Influye en el puntaje

**FILTER**<br>

- Funciona como un AND logico tambien
- La consulta debe de aparecer en los documentos retornados
- **No** influye en el **puntaje**
- Usar Filter en vez de Must, si no nos interesa el puntaje
- Permite cache

**SHOULD**

- Funciona como un OR logico
- Alguna de las consultas deberia aparecer en los documentos retornados
- Influye en el puntaje
- **minimum_should_match** permite alterar el comprtamiento, Cuantas consultas deben coincidir 

**NOTA:** Si Must o Filter (AND) estan presentes en la consulta booleana

minimum_should_match = 0 (defecto) [should se vulve opcional]

De lo contario

minimum_should_match = 1 (defecto) [Al estar sola almenos una condicion debe de cumplirse]

**Must Not**
- Funciona como un NOT logico
- La consulta no debe aoarecer en los documentos retornados 
- No influye en el puntaje
- Permite cache

# Consultas booleanas

```python
{
    "query": {
        "bool": {
            "must": {
                "match": {"descripcion":"picante"}
            },
            "filter": {
                "term": {"estado": "activo"}
            },
            "must_not": {
                "term": {"pedidosUltimaHora": 0}
            },  
            "should": [
                {"match":{"descripcion":"aguacate"}},
                {"match":{"descripcion":"guacamole"}}
            ],
            "minimum_should_match": 1
        }
    }
}
```

"Term" es un valor exacto 
"Match" busca en la toda cadena

```python
{
    "query": {
        "bool": {
            "must": [
               {"match": {"descripcion":"picante"}},
                {"term": {"estado": "activo"}}
            ],
            "must_not": {
                "term": {"pedidosUltimaHora": 0}
            },  
            "should": [
                {"match":{"descripcion":"aguacate"}},
                {"match":{"descripcion":"guacamole"}},
                {"match":{"descripcion":"pico"}}
            ],
            "minimum_should_match": 1
        }
    }
}
```

# Consultas compuestas

Para condiciones mas elaboradas , podemos componer consultas booleanas para lograr el resultado

Ejercicio:

![](https://i.imgur.com/Ze3I9y3.png)

Asi se representa en elasticsearch

![](https://i.imgur.com/PlR6SMg.png)

En codigo 

![](https://i.imgur.com/afBplxR.png)

# Construyendo una consulta compuesta

```python
{
    "query": {
        "bool": {
            "must": [
                {
                    "bool": {
                        "should": [
                            {"term": {"estado": "activo"}},
                            {"term": {"estado": "pendiente"}}
                        ]
                    }
                },
                {
                    "bool": {
                        "should": [
                            {"match": {"ultimaModificacion.usuario": "mail.com"}},
                            {"match": {"ultimaModificacion.usuario": "vendor.com"}}
                        ]
                    }
                },
                {
                    "term": {"pedidosUltimaHora":0}
                }
            ]
        }
    }
}
```

# Consultas anidadas

- Guardad una lista de objetos dentro de un documento (pertenencia)
- Esta consulta encuentra los documenyos usando sus objetos anidados
- Al encontrar una coincidencia el documento raiz es devuelto

```python
PUT localhost:9200/restaurantes

{
  "mappings":{
    "properties":{
      "nombre":{ "type":"text" },
      "categorias" : {
        "type" : "nested",
        "properties": {
          "nombre": { "type" : "keyword" },
          "principal": { "type" : "boolean" }
        }
      }
    }
  }
}
```

Agreguemos restaurantes

```python
PUT /restaurantes/_doc/1
{
  "nombre": "McDonalds",
  "categorias": [
    { "nombre": "Comida Rápida", "principal": true },
    { "nombre": "Desayunos", "principal": false },
    { "nombre": "Hamburguesas", "principal": false }
  ]
}

PUT /restaurantes/_doc/2
{
  "nombre": "Burger King",
  "categorias": [
    { "nombre": "Comida Rápida", "principal": false },
    { "nombre": "Hamburguesas", "principal": true }
  ]
}

PUT /restaurantes/_doc/3
{
  "nombre": "Subway",
  "categorias": [
    { "nombre": "Comida Saludable", "principal": false },
    { "nombre": "Sándwiches", "principal": true }
  ]
}
```

Busquedas elastic

```python
Búsqueda No. 1: Categoria=Comida Saludable
GET localhost:9200/restaurantes/_search
{
  "query": {
    "nested": {
      "path": "categorias",
      "query": {
        "bool": {
          "must": {
            "term": { "categorias.nombre": "Comida Saludable" }
          }
        }
      }
    }
  }
}
Búsqueda No. 2: Categoria=Comida Rápida
GET localhost:9200/restaurantes/_search
{
  "query": {
    "nested": {
      "path": "categorias",
      "query": {
        "bool": {
          "must": {
            "term": { "categorias.nombre": "Comida Rápida" }
          }
        }
      }
    }
  }
}
Búsqueda No. 3: Categoria=Comida Rápida, principal=true
GET localhost:9200/restaurantes/_search
{
  "query": {
    "nested": {
      "path": "categorias",
      "query": {
        "bool": {
          "must": [
            { "term": { "categorias.nombre": "Comida Rápida" } },
            { "term": { "categorias.principal": true } }
          ]
        }
      }
    }
  }
}
```

# Proyecto: unificación de datos

### Mapeamos las colecciones

```python
localhost:9200/restaurantes/_mapping

localhost:9200/platos/_mapping
```

### Actualizamos restaurantes con platos y nuevos campos

El campo de tipo anidado es "nested"

```python
PUT localhost:9200/restaurantes/_mapping

{
  "properties":{
    "calificacion": { "type": "double" },
    "direccion": { "type": "text" },
    "platos": {
      "type": "nested",
      "properties": {
        "descripcion": { "type": "text" },
        "estado": { "type": "keyword" },
        "nombre": { "type": "text" },
        "pedidosUltimaHora": { "type": "integer" }
      }
    },
    "ultimaModificacion": {
      "properties": {
        "usuario": { "type": "text" },
        "fecha": { "type": "date" }
      }
    }
  }
}

```

### Actualizamos restaurantes existentes con POST y _update

```python
POST localhost:9200/restaurantes/_update/1

{
  "doc": {
    "calificacion": 4.22,
    "direccion": "Calle Primera, Barrio Centro",
    "platos": [
      {
        "nombre": "Bowl Picante",
        "descripcion": "Pollo, salsa picante, frijoles negros, plátano maduro y aguacate.",
        "estado": "activo",
        "pedidosUltimaHora": 42
      }
    ],
    "ultimaModificacion": {
      "usuario": "rick@mail.com",
      "fecha": "2020-02-19"
    }
  }
}
```

```python
POST localhost:9200/restaurantes/_update/2

{
  "doc": {
    "calificacion": 3.75,
    "direccion": "Carrera Segunda, Barrio Occidental",
    "platos": [
      {
        "nombre": "Nachos XL",
        "descripcion": "Nachos con carne moplida, guacamole, pico de gallo, salsa picante, queso chedar y frijoles negros",
        "estado": "activo",
        "pedidosUltimaHora": 11
      }
    ],
    "ultimaModificacion": {
      "usuario": "jerry@mail.com",
      "fecha": "2020-03-01"
    }
  }
}
```

SUBWAY SIN CALIFICACIÓN

```python
POST localhost:9200/restaurantes/_update/3

{
  "doc": {
    "direccion": "Carrera Tercera, Barrio Norte",
    "platos": [
      {
        "nombre": "Ensaladísima",

        "descripcion": "Aceitunas, cebolla, queso, pimentón, tomate cherry, aguacate. (vegetariano y saludable)",
        "estado": "activo",
        "pedidosUltimaHora": 0
      }
    ],
    "ultimaModificacion": {
      "usuario": "rick@mail.com",
      "fecha": "2020-01-22"
    }
  }
}
```

# Consultas de rango y agregaciones

# Rangos

```python
{
    "_source": ["nombre", "calificacion"],
    "query": {
        "range": {
            "calificacion": {
                "gt": 3.5,
                "lte": 4.5
            }
        }
    }
}
```

Una consulta de rango lo hace sobre un grupo de valores, lo hace con , numeros y fechas.

- Con "_source": indicamos los campos a mostrar.

- "gt" significa mayor que

- "lt" significa menor que

- La "e" al final ya sea de "gt" o "lt" significa "igual que"


```python
GET localhost:9200/restaurantes/_search

{
    "_source": ["nombre", "ultimaModificacion.fecha"],
    "query": {
        "range": {
            "ultimaModificacion.fecha": {
                "gte": "2020-01-15",
                "lt": "2020-03-01"
            }
        }
    }
}
```

# Agregaciones

Las agregaciones no son consultas, son metricas

```python
GET localhost:9200/restaurantes/_search

{
    "aggs":{
        "calificacionPromedio": {"avg": {"field": "calificacion"}},
        "calificacionMaximo":{"max": {"field": "calificacion"}},
        "calificacionMinima":{"min": {"field": "calificacion"}}
    }
}
```


```python
{
    "aggs": {
        "calificacionPromedio": {
            "avg": {
                "field": "calificacion",
                "missing": 3.0
            }
        }
    }
}
```

"missing" es una forma de poner por defecto valores que no lo tienen


# Proyecto: revisión final del directorio

# FINAL

https://github.com/krsarmiento/contenido-curso-elastic/blob/clase-03-poner-a-correr-elasticsearch/clase.md
