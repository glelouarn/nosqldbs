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

### Couchbase http://www.couchbase.com/

#####Architecture
+ The base of the product is a CouchDB engine with a memcache layer (key/value cache). 
+ Basically, it works storing documents (can be any type of content, but normally JSON) associated with an unique ID key.
+ Buckets are the basis, and a bucket == database. They have a name, they are balanced (sharding) automatically in the cluster, can be secured by password and can be easily monitored from built-in console
+ When buckets are created, they can be defined as memory buckets (Memcached only) or Couchbase buckets (uses disk to store and memory for caching)
+ Application can talk with one or more buckets but the buckets can’t communicate to each other.
+ The limit of buckets in a single cluster is 10
+ A bucket is divided in different vBuckets. There is a vBucket map that maps each vBucket to a node.
+ When the cluster is extended the vBucket are rebalanced (reallocated) between the different nodes (the information is “sharded” in the cluster).
+ Buckets can be configured to be replicated within the cluster (backups of the the vBuckets are copied in different nodes) so no information is lost if a node fails

#####Working with CouchBase
+ Operations are very basic Create-Read-Update-Delete (CRUD operations) using the ID. Since it is a Map, the performance is always O(1).
+ It is possible to execute bulk operations (execute several basic operations as a batch on one call)
+ It is possible to specify a TTL for a document

#####Consistency
+ There is always only a single active copy of data within the cluster
+ Consistency of views is a little bit more complicated (data is only eligible to be included when it is persisted in replicas and/or disk)
+ Couchbase provides strong consistency between nodes of the same cluster
+ In XDCR it provides eventual consistency (in case of conflict it takes the document with more updates)

#####Concurrency
+ Only one active version of data
+ Operations are atomic
+ Some information can be updated by two hosts simultaneously. In this case it is possible to use the CAS (Compare and Swap) functionality. This allows to obtain a hash to check if the information has changed. Also, the operation can perform also explicit locking if necessary

#####Replication
+ It is possible to create replicas of the buckets within the cluster and configure up to 3 replicas for each bucket. In this case, replica == backup
+ From version 2.0 there is a system to make replication between different clusters that can be geographically separated. It is called 'cross datacenter replication' (XDCR)
+ XDCR can be configured to work in unidirectional and bidirectional mode.
+ The consistency XDCR is eventual. It applies a mechanism to resolve conflicts (several updates over the same date).
+ XDCR replication works by push and is automatic and instantly

#####Views
+ Exist from Couchbase 2.0 and they are the only way to perform queries/search by fields different from unique ID in CouchBase
+ Views transform documents into structured data in order to perform operations on them
+ Views use map reduce functions. Map translates document structure to table structure. Reduce simplifies and summarize, creating counts, sums, etc
+ To minimize expensiveness of map/reduce in performance CB uses an incremental map/reduce. Each map-reduce creates and index and it only reproduces and recalculates for the new data updated and not for the entire dataset.
+ If we change the definition of the view, it invalidates the index that must be reprocessed for all the data
+ Once created a view, the information is ready to be queried
+ Views are coded in JavaScript  and must be created in design time and from the admin app (although it is possible to create them from code: http://www.javacodegeeks.com/2013/01/couchbase-101-create-views-mapreduce-from-your-java-application.html)

#####Clients and code integration with CouchBase
+ CouchBase protocol is a binary one taken from memcache. 
+ Clients available in Java, .NET, Ruby, Python, PHP, C (also a Go client but not stable)
+ Not JPA/JDO or abstraction layer support
+ CouchBase is working hard in N1QL, a query language to query documents in a way very similar to SQL:
http://www.couchbase.com/communities/n1ql
http://blog.couchbase.com/n1ql-it-makes-cents
http://query.pub.couchbase.com/tutorial/#1

#####License
+ There are two options: Community and Enterprise. It is the same license model that has MySQL. In Enterprise option, the (annual?) cost per node is 2700$: http://www.couchbase.com/couchbase-support


#### CouchDB http://couchdb.apache.org/

+ Many CouchBase core features: views, operations, indexes, bulk operations... apply also to CouchDB.
+ Default web interface (Futon) little bit different and much more simple and limited than that of CouchBase
+ Views are also made in Javascript with the same syntax. They can be created from Futon console.
+ The reduce funcions are a little bit more complicated (in CouchDB there are some kind of macros to do them)
+ CouchDB has Update Handlers that are similar to triggers. This functionality is still not available on CouchBase: http://www.couchbase.com/forums/thread/unable-fire-update-handler-couchbase
+ CouchDB has built-in memory cache. Instead, it just recommends using memcached manually to provide it.
+ Consistency: eventually consistent: conflict resolution on CouchDB is much more limited than the one of CouchBase.
+ Clustering and load balancing: recommends using HTTP load balancing software or hardware (proxies) manually. CouchDB does not natively support sharding. There are third-party tools that do it like BigCouch, Lounge or Pillow
+ Replication: replication between datacenters can be done by REST or using Futon. Automatic replication can be done using the flag ‘continuous: true’. It is not immediate, and it has an algorithm that decides the best moment for performance. Since CouchDB 1.1 it can remember replications after reboot.
+ Clients: CouchDB uses a protocol based on a REST interface over HTTP so it can integrate easily with any language and technology.
+ Performance: we have found some documents that state the low performance of the product: http://sauceio.com/index.php/2012/05/goodbye-couchdb/ http://blog.signalhq.com/2012/01/24/getting-off-the-couchdb/ Even the creator of CouchDB (now on charge of CouchBase) recognizes it: http://damienkatz.net/2012/01/the_future_of_couchdb.html#comments
+ License: it is an Apache project, so totally open source. 


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
+ http://nosql.findthebest.com/
+ http://db-engines.com/en/ranking_trend
