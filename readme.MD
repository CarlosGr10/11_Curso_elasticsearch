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
version: '2.2'
services:
  es01:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.6.0
    container_name: es01
    environment:
      - node.name=es01
      - cluster.name=es-docker-cluster
      - cluster.initial_master_nodes=es01
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
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

TEXT: Busquedas de texto completas (match)

KEYWORD: valores exactos (term)

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
localhost:9200/platos/_search

{
    "query":{
        "simple_query_string": {"query":"Nachos XL"}
    }
}
```

En el documento vemos la palabra reservada "_score", que entre mas alto , coincide mas con los resultados

