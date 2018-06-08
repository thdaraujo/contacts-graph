# contacts-graph
An experiment in graph database querying with janus graph

## How to run

Run docker image:
```
$ docker run -it mignic/janusgraph:0.2.0
```

Run gremlin:
```
$ cd /src/janusgraph-0.2.0-hadoop2
$ bin/gremlin.sh
```

## Config in memory DB

```
storage.backend=inmemory
graph = JanusGraphFactory.build().set('storage.backend', 'inmemory').open()
```

## Config Database (Bigtable)

```
storage.backend=hbase
storage.hbase.ext.hbase.client.connection.impl=com.google.cloud.bigtable.hbase1_x.BigtableConnection
storage.hbase.ext.google.bigtable.project.id=<Google Cloud Platform project id>
storage.hbase.ext.google.bigtable.instance.id=<Bigtable instance id>
```

## Graph example

```





```
