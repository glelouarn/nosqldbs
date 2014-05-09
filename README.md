# Etude de solutions NoSQL

## Objectif

Le but de cet étude est de réaliser un premier état des lieux des solutions NoSQL envisageables afin de mettre en place une solution de partage de données dites _chaudes_ entre nos sites d'exploitation.

La solution retenue devra apporter une solution à ces problématiques:
+ Partage de données en temps réel entre les deux sites de production afin de s'afranchir des problématiques de consistance des données et des sessions auxquelles nous avons des difficultés à faire face aujourd'hui.
+ Permettre de gérer en parallèle au moins deux versions différentes de l'applicatif sans nécessiter de phase de migration bloquant l'usage normal de la base.

## Etude préliminaire

### Solutions de type _Key-value Stores_

#### Memcached http://memcached.org/

__Rejeté__ car ce n'est pas une base de donnée mais un système de mise en cache de données en mémoire. Certaines solutions comme Couchbase sont bassées sur Memcached.

#### Redis http://redis.io/

__Rejeté__ car c'est une solution orienté mémoire, ce qui fait qu'elle est limitée par la RAM disponible sur la machine http://redis.io/topics/faq. De plus, sa réplication de type _maitre-esclave_ ne répond pas à notre besoin.

#### riak http://basho.com/riak/

__Rejetté__ rapidement pour ces raisons, nous n'avons par conséquent pas particulièrement creusé son étude:
+ Réplication _multi datacenter_ seulement disponible au travers d'une licence commerciale: http://basho.com/pricing/
+ Même s'il faut se méfier de telles publication, certains utilisateurs semblent avoir du mal à bien gérer les performances de l'outil come ici: http://boundary.com/blog/2012/12/21/case-study-inaka/

#### Scalaris https://code.google.com/p/scalaris/

__Rejetté__ car le projet semble très peu connu et peu actif.

#### Kyoto Tycoon http://fallabs.com/kyototycoon/

__Rejetté__ car le projet semble particulièrement peu actif.

### Solutions de type _Wide Column Stores_

#### Cassandra  http://cassandra.apache.org/

TODO

#### HBase http://hbase.apache.org/

__Rejetté__ car orienté _big data_, ce qui n'est pas réellement notre cas:

> __When Should I Use HBase?__
>
> HBase isn't suitable for every problem. First, make sure you have enough data. If you have hundreds of millions or billions of rows, then HBase is a good candidate.
>
> http://hbase.apache.org/book/architecture.html#arch.overview

#### Hypertable http://hypertable.org/

__Rejetté__ car orienté _big data_.

### Accumulo http://accumulo.apache.org/

__Rejetté__ car orienté _big data_.

### Solutions de type _Document Stores_

#### Couchbase http://www.couchbase.com/

TODO

#### CouchDB http://couchdb.apache.org/

TODO

#### MongoDB http://www.mongodb.org/

Il est vrai que c'est une solution actuellement à la mode et réellement séduisane à l'usage. Cependant, nous l'avons __rejetté__ et n'avons pas creusé son éude car sa réplication de type _maitre-esclave_ ne répondant pas à notre principale exigence de partage d'informations entre sites.

### Solutions pour usages spécifiques

#### Neo4j http://www.neo4j.org/

__Rejetté__ car il s'agit d'une base de données orienté graphe. C'est égallement une solution très inéressant mais de répondant pas le moins du monde à notre problématique.

#### elasticSearch http://www.elasticsearch.org/

__Rejetté__ pour le besoin étudié ici car il s'agit d'une base de données orienté recherches textuelles.

## Ressources

+ http://kkovacs.eu/cassandra-vs-mongodb-vs-couchdb-vs-redis
+ http://db-engines.com/en/article/Key-value+Stores
+ http://db-engines.com/en/system/Memcached%3BRedis%3BRiak
+ http://db-engines.com/en/article/Wide+Column+Stores
+ http://db-engines.com/en/system/Cassandra%3BHBase
+ http://db-engines.com/en/article/Document+Stores
+ http://db-engines.com/en/system/CouchDB%3BCouchbase%3BMongoDB
