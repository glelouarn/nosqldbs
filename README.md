# Etude de solutions NoSQL

## Objectif

Le but de cet étude est de réaliser un premier état des lieux des solutions NoSQL envisageables afin de mettre en place une solution de partage de données dites `chaudes` entre nos sites d'exploitation.

La solution retenue devra apporter une solution à ces problématiques:
+ Partage de données en temps réel entre les deux sites de production afin de s'afranchir des problématiques de consistance des données et des sessions auxquelles nous avons des difficultés à faire face aujourd'hui.
+ Permettre de gérer en parallèle au moins deux versions différentes de l'applicatif sans nécessiter de phase de migration bloquant l'usage normal de la base.

## Etude préliminaire

### Solutions de type `Key-value Stores`

#### Memcached http://memcached.org/

TODO

#### Redis http://redis.io/

TODO

#### riak http://basho.com/riak/

TODO

#### Scalaris https://code.google.com/p/scalaris/

TODO

#### Kyoto Tycoon http://fallabs.com/kyototycoon/

TODO

### Solutions de type `Wide Column Stores`

#### Cassandra  http://cassandra.apache.org/

TODO

#### HBase http://hbase.apache.org/

TODO

#### Hypertable http://hypertable.org/

TODO

### Accumulo http://accumulo.apache.org/

TODO

### Solutions de type `Document Stores`

#### Couchbase http://www.couchbase.com/

TODO

#### CouchDB http://couchdb.apache.org/

TODO

#### MongoDB http://www.mongodb.org/

TODO

### Solutions pour usages spécifiques

#### Neo4j http://www.neo4j.org/

TODO

#### elasticSearch http://www.elasticsearch.org/

TODO

## Ressources

+ http://kkovacs.eu/cassandra-vs-mongodb-vs-couchdb-vs-redis
+ http://db-engines.com/en/article/Key-value+Stores
+ http://db-engines.com/en/system/Memcached%3BRedis%3BRiak
+ http://db-engines.com/en/article/Wide+Column+Stores
+ http://db-engines.com/en/system/Cassandra%3BHBase
+ http://db-engines.com/en/article/Document+Stores
+ http://db-engines.com/en/system/CouchDB%3BCouchbase%3BMongoDB
