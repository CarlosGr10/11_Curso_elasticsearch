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


