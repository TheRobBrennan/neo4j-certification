# Introduction to Neo4j 4.0

[Introduction to Neo4j 4.0](https://neo4j.com/graphacademy/online-training/introduction-to-neo4j-40/) is a free two-day course.

## Lesson Overview

### Neo4j is a Graph Database

#### What is a graph?

The first use of graphs was by Leonhard Euler in 1735; he used graph theory to prove that the famous Seven Bridges of Konigsberg problem had no solution:

![https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/SevenBridges.png](https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/SevenBridges.png)

The Seven Bridges problem is this: Is there any possible route through the city that crosses every bridge exactly once?

Source: [https://neo4j.com/graphacademy/online-training/v4/01-neo4j-graph-database/](https://neo4j.com/graphacademy/online-training/v4/01-neo4j-graph-database/)

#### What is a graph database?

![https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/GraphDatabaseInEnterprise.png](https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/GraphDatabaseInEnterprise.png)

Source: [https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/GraphDatabaseInEnterprise.png](https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/GraphDatabaseInEnterprise.png)

Unlike other databases, relationships take first priority in graph databases. This means your application doesn’t have to infer data connections using foreign keys or out-of-band processing, such as MapReduce.

#### Querying a graph is intuitive in Cypher

For example, in an organizational domain, here is what a SQL statement that lists the employees in the IT Department would look like:

```sql
SELECT name FROM Person
LEFT JOIN Person_Department
  ON Person.Id = Person_Department.PersonId
LEFT JOIN Department
  ON Department.Id = Person_Department.DepartmentId
WHERE Department.name = "IT Department"
```

In contrast, Cypher syntax stays clean and focused on domain concepts since queries are expressed visually:

```cypher
MATCH (p:Person)<-[:EMPLOYEE]-(d:Department)
WHERE d.name = "IT Department"
RETURN p.name
```

Source: [https://neo4j.com/graphacademy/online-training/v4/01-neo4j-graph-database/#\_querying_a_graph_is_intuitive_in_cypher](https://neo4j.com/graphacademy/online-training/v4/01-neo4j-graph-database/#_querying_a_graph_is_intuitive_in_cypher)

There are many uses for graph databases in an enterprise. Here are some of them:

![https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/UseCases.png](https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/UseCases.png)

#### Exploring graphgists

GraphGists are teaching tools which allow you to explore how data in a particular domain would be modeled as a graph and see some example queries of that graph data. Any developer can create a GraphGist by visiting [https://portal.graphgist.org](https://portal.graphgist.org).

[Neo4j GraphGists](https://neo4j.com/graphgists/)

### The Neo4j Graph Platform

#### Neo4j DBMS

A Neo4j instance is a single process that runs the Neo4j server code. A Neo4j instance at a minimum contains two databases, the system database and the default database, neo4j.

The **system** database stores metadata about the databases for the installation, as well as security configuration. The **default** database (named _neo4j_ by default) is the “user” database where you implement your graph data model.

##### Neo4j 4.0 DBMS

In Neo4j Enterprise Edition 4.0, you may have more than one “user” database.

##### Index-free adjacency in Neo4j

With index-free adjacency, when a node or relationship is written to the database, it is stored in the database as connected and any subsequent access to the data is done using pointer navigation which is very fast. Since Neo4j is a native graph database (i.e. it has a graph as its core data model), it supports very large graphs where connected data can be traversed in **constant time** without the need for an index.

### Introduction to Cypher

#### What is Cypher?

Complex database queries can easily be expressed through Cypher, allowing you to focus on your domain instead of getting lost in the syntax of database access.

The guiding goal is to make the simple things easy, and the complex things possible.

Being a declarative language, Cypher focuses on the clarity of expressing what to retrieve from a graph, not on how to retrieve it.

![https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/Nouns.png](https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/Nouns.png)

The pattern matching functionality of Cypher borrows concepts from SPARQL. And some of the collection semantics have been borrowed from languages such as Haskell and Python.

#### Examples specifying nodes

```
// anonymous node that will not be referenced later in the query
()
// variable p, a reference to a node used later
(p)
// anonymous node of type Person
(:Person)
// p, a reference to a node of type Person
(p:Person)
// p, a reference to a node of types Actor and Director
(p:Actor:Director)
```

#### Syntax: Using MATCH to retrieve nodes

When you specify a pattern for a `MATCH` clause, you should always specify a node label if possible. In doing so, the graph engine uses an index to retrieve the nodes which will perform better than not using a label for the `MATCH`.

```
MATCH (n) 			// returns all nodes in the graph
RETURN n
```

#### Exercise 1: Retrieving nodes

```
:play 4.0-intro-neo4j-exercises
```

#### Properties

Properties can be used to filter queries so that a subset of the graph is retrieved. In addition, with the `RETURN` clause, you can return property values from the retrieved nodes, rather than the nodes.

#### Relationships

Here is how Cypher uses ASCII art to specify the path used for a query:

```
()          // a node
()--()      // 2 nodes have some type of relationship
()-->()     // the first node has a relationship to the second node
()<--()     // the second node has a relationship to the first node
```

Using a relationship in a query:

```
// Show me all people who were actors in The Matrix
MATCH (p:Person)-[rel:ACTED_IN]->(m:Movie {title: 'The Matrix'})
RETURN p, rel, m

// Show me movies that Tom Hanks acted in and directed
MATCH (p:Person {name: 'Tom Hanks'})-[:ACTED_IN|DIRECTED]->(m:Movie)
RETURN p.name, m.title

// Find actors that acted in The Matrix, but you do not need any information returned about the Movie node using an anonymous node for the movie
MATCH (p:Person)-[:ACTED_IN]->(:Movie {title: 'The Matrix'})
RETURN p.name
```

A best practice is to place named nodes (those with variables) before anonymous nodes in a `MATCH` clause.

Using an anonymous relationship for a query:

```
// Find all people who are in any way connected to the movie "The Matrix"
MATCH (p:Person)-->(m:Movie {title: 'The Matrix'})
RETURN p, m
```

In this training, we will use `-->`, `--`, and `<--` to represent anonymous relationships as it is a Cypher best practice.

Retrieving the relationship types:

```sh
// There is a built-in function, type() that returns the type of a relationship.
MATCH (p:Person)-[rel]->(:Movie {title:'The Matrix'})
RETURN p.name, type(rel)
```

##### Properties for relationships

![https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/REVIEWEDProperties.png](https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/REVIEWEDProperties.png)

```
// Find all people who have reviewed The Da Vinci Code with with a rating of 65
MATCH (p:Person)-[:REVIEWED {rating: 65}]->(:Movie {title: 'The Da Vinci Code'})
RETURN p.name
```

##### Querying by any direction of the relationship

```
// Find all Person nodes connected by the FOLLOWS relationship in either direction
MATCH  (p1:Person)-[:FOLLOWS]-(p2:Person {name:'Angela Scope'})
RETURN p1, p2
```

##### Traversing multiple relationships

```
// Return all followers of the followers of Jessica Thompson
MATCH  (p:Person)-[:FOLLOWS]->(:Person)-[:FOLLOWS]->(:Person {name:'Jessica Thompson'})
RETURN p

// Return each person along the matched path
MATCH  (p:Person)-[:FOLLOWS]->(p2:Person)-[:FOLLOWS]->(p3:Person {name:'Jessica Thompson'})
RETURN p.name, p2.name, p3.name
```

##### Using patterns to focus the query

```
// Retrieve all unique relationships between an actor, a movie, and a director
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(d:Person)
RETURN a.name, m.title, d.name
```

##### Returning paths

```
// Graph will be a person who follows a person who follows Jessica Thompson
MATCH  path = (:Person)-[:FOLLOWS]->(:Person)-[:FOLLOWS]->(:Person {name:'Jessica Thompson'})
RETURN  path

// Return all paths from actors to a movie that was directed by Ron Howard
MATCH  path = (:Person)-[:ACTED_IN]->(:Movie)<-[:DIRECTED]-(:Person {name:'Ron Howard'})
RETURN  path
```

##### Cypher style recommendations

Here is an example showing some best coding practices

```
MATCH (:Person {name: 'Diane Keaton'})-[movRel:ACTED_IN]->
(:Movie {title:"Something's Gotta Give"})
RETURN movRel.roles
```

### Using Where to Filter Queries

### Working with Patterns

### Working with Cypher Data

### Controlling the Query Chain

### Controlling Results Returned

### Creating Nodes

### Creating Relationships

### Deleting Nodes and Relationships

### Merging Data in the Graph

### Defining Constraints for your Data

### Using Indexes

### Using Query Best Practices

### Overview of Importing Data into Neo4j

### Using LOAD CSV for Import

### Using APOC for Import

### Using the neo4j-admin tool for Import

### Using an Application for Import

## Resources

Here are some resources you will use as you learn about Neo4j and Cypher for this course:

- [Neo4j Cypher Manual](https://neo4j.com/docs/cypher-manual/4.0/)
- [Neo4j Cypher Refcard](https://neo4j.com/docs/cypher-refcard/current/)
- [APOC Documentation](https://neo4j.com/docs/labs/apoc/current/)
- [Neo4j Drivers Manual](https://neo4j.com/docs/driver-manual/current/)
- [Neo4j Community Site](https://community.neo4j.com/c/general/online-training?_ga=2.145081532.928352283.1602607035-1250704953.1598219608)
