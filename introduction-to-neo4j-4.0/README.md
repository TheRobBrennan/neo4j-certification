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
