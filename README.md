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
g = graph.traversal()

```

## Config Database (Bigtable)

```
storage.backend=hbase
storage.hbase.ext.hbase.client.connection.impl=com.google.cloud.bigtable.hbase1_x.BigtableConnection
storage.hbase.ext.google.bigtable.project.id=<Google Cloud Platform project id>
storage.hbase.ext.google.bigtable.instance.id=<Bigtable instance id>
```

## Graph example

[Simple query examples](https://docs.datastax.com/en/dse/5.1/dse-dev/datastax_enterprise/graph/using/querySimple.html)

```
juliaChild = graph.addVertex(label,'author', 'name','Julia Child', 'gender','F')

frenchChefCookbook = graph.addVertex(label, 'book', 'name', 'The French Chef Cookbook', 'year' , 1968, 'ISBN', '0-394-40135-2')

juliaChild.addEdge('authored', frenchChefCookbook)
```

More examples:
```
johnDoe = graph.addVertex(label, 'reviewer', 'name','John Doe')
johnSmith = graph.addVertex(label, 'reviewer', 'name', 'John Smith')
janeDoe = graph.addVertex(label, 'reviewer', 'name','Jane Doe')
sharonSmith = graph.addVertex(label, 'reviewer', 'name','Sharon Smith')
betsyJones = graph.addVertex(label, 'reviewer', 'name','Betsy Jones')

beefBourguignon = g.V().has('recipe', 'name','Beef Bourguignon').tryNext().orElseGet {graph.addVertex(label, 'recipe', 'name', 'Beef Bourguignon')}
spicyMeatLoaf = g.V().has('recipe', 'name','Spicy Meatloaf').tryNext().orElseGet {graph.addVertex(label, 'recipe', 'name', 'Spicy Meatloaf')}
carrotSoup = g.V().has('recipe', 'name','Carrot Soup').tryNext().orElseGet {graph.addVertex(label, 'recipe', 'name', 'Carrot Soup')}

// reviewer - recipe edges
johnDoe.addEdge('rated', beefBourguignon, 'timestamp', '2014-01-01T05:15:00.00Z', 'stars', 5, 'comment', 'Pretty tasty!')
johnSmith.addEdge('rated', beefBourguignon, 'timestamp', '2014-01-23T00:00:00.00Z', 'stars', 4)
janeDoe.addEdge('rated', beefBourguignon, 'timestamp', '2014-02-01T00:00:00.00Z', 'stars', 5, 'comment', 'Yummy!')
sharonSmith.addEdge('rated', beefBourguignon, 'timestamp', '2015-01-01T00:00:00.00Z', 'stars', 3, 'comment', 'It was okay.')
johnDoe.addEdge('rated', spicyMeatLoaf, 'timestamp', '2015-12-31T10:56:00.00Z', 'stars', 4, 'comment', 'Really spicy - be careful!')
sharonSmith.addEdge('rated', spicyMeatLoaf, 'timestamp', '2014-07-23T00:30:00.00Z', 'stars', 3, 'comment', 'Too spicy for me. Use less garlic.')
janeDoe.addEdge('rated', carrotSoup, 'timestamp', '2015-12-30T01:20:00.00Z', 'stars', 5, 'comment', 'Loved this soup! Yummy vegetarian!')
```

Traversal:
```
g.V().hasLabel('reviewer').values('name')

g.V().has('reviewer', 'name','John Doe').outE('rated').values('comment')

g.V().has('reviewer', 'name','John Doe').outE('rated').inV().values('name')

//all the reviews that give a recipe more than 3 stars
g.E().hasLabel('rated').has('stars', gt(3)).valueMap()

g.E().hasLabel('rated').has('stars', gt(3)).inV().values('name')


g.V().hasLabel('reviewer').as('reviewer','rating').out().as('recipe').
select('reviewer','rating','recipe').
  by('name').
  by(outE('rated').values('stars')).
  by(values('name'))

```

Visualize:
```
graph.io(IoCore.graphson()).writeGraph("my-graph.json")
graph.io(IoCore.graphml()).writeGraph("my-graph.graphml")
```
Import into [gephi](https://gephi.org/users/supported-graph-formats/graphml-format/)
