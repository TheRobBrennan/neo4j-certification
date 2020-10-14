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

#### Example: Testing ranges

```
// Test multiple values
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE m.released = 2008 OR m.released = 2009
RETURN p, m

// Specify a range
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE m.released >= 2003 AND m.released <= 2004
RETURN p.name, m.title, m.released

// ALTERNATIVE: Specify a range
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE 2003 <= m.released <= 2004
RETURN p.name, m.title, m.released
```

#### Testing existence of a property

Recall that a property is associated with a particular node or relationship. A property is not associated with a node with a particular label or relationship type.

```
// Only match Movie nods that contain a tagline property
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE p.name='Jack Nicholson' AND exists(m.tagline)
RETURN m.title, m.tagline
```

#### Testing strings

```
// find all actors in the Movie database whose first name is Michael
MATCH (p:Person)-[:ACTED_IN]->()
WHERE p.name STARTS WITH 'Michael'
RETURN p.name

// In this example where we are converting a property to lower case, if an index has been created for this property, it will not be used at runtime.
MATCH (p:Person)-[:ACTED_IN]->()
WHERE toLower(p.name) STARTS WITH 'michael'
RETURN p.name
```

#### Testing with regular expressions

If you prefer, you can test property values using regular expressions. You use the syntax `=~` to specify the regular expression you are testing with.

```
// Retrieve all Person nodes with a name property that begins with ‘Tom’
MATCH (p:Person)
WHERE p.name =~'Tom.*'
RETURN p.name
```

If you specify a regular expression. The index will never be used. In addition, the property value must fully match the regular expression.

#### Example: Testing with patterns – 1

```
// Return all Person nodes of people who wrote movies
MATCH (p:Person)-[:WROTE]->(m:Movie)
RETURN p.name, m.title
```

#### Example: Testing with patterns – 2

```
// Modify above example to exclude people who directed that particular movie
MATCH (p:Person)-[:WROTE]->(m:Movie)
WHERE NOT exists( (p)-[:DIRECTED]->(m) )
RETURN p.name, m.title
```

#### Example: Testing with patterns – 3

```
// Find Gene Hackman and the movies that he acted in with another person who also directed the movie.
MATCH (gene:Person)-[:ACTED_IN]->(m:Movie)<-[:ACTED_IN]-(other:Person)
WHERE gene.name= 'Gene Hackman'
AND exists( (other)-[:DIRECTED]->(m) )
RETURN  gene, other, m
```

#### Testing with list values

If you have a set of values you want to test with, you can place them in a list or you can test with an existing list in the graph. A Cypher list is a comma-separated set of values within square brackets.

You can place either numeric or string values in the list, but typically, elements of the list are of the same type of data. If you are testing with a property of a string type, then all the elements of the list should be strings.

```
// Retrieve Person nodes of people born in 1965 or 1970
MATCH (p:Person)
WHERE p.born IN [1965, 1970]
RETURN p.name as name, p.born as yearBorn
```

#### Testing list values in the graph

You can also compare a value to an existing list in the graph.

We know that the :ACTED_IN relationship has a property, roles that contains the list of roles an actor had in a particular movie they acted in.

```
// Return the name of the actor who played Neo in the movie The Matrix
MATCH (p:Person)-[r:ACTED_IN]->(m:Movie)
WHERE  'Neo' IN r.roles AND m.title='The Matrix'
RETURN p.name
```

#### Exercise 4: Filtering queries using the WHERE clause

```
// Retrieve all movies released in 2000 by testing the node label and the released property, returning the movie titles.
MATCH (m)
WHERE m:Movie AND m.released = 2000
RETURN m.title

// Retrieve all people that wrote movies by testing the relationship between two nodes, returning the names of the people and the titles of the movies.
MATCH (a)-[rel]->(m)
WHERE a:Person AND type(rel) = 'WROTE' AND m:Movie
RETURN a.name as Name, m.title as Movie

// Retrieve all people in the graph that do not have a born property, returning their names.
MATCH (a:Person)
WHERE NOT exists(a.born)
RETURN a.name as Name

// Retrieve all people related to movies where the relationship has the rating property, then return their name, movie title, and the rating.
MATCH (a:Person)-[rel]->(m:Movie)
WHERE exists(rel.rating)
RETURN a.name as Name, m.title as Movie, rel.rating as Rating

// Retrieve all REVIEWED relationships from the graph where the summary of the review contains the string fun, returning the movie title reviewed and the rating and summary of the relationship
MATCH (:Person)-[r:REVIEWED]->(m:Movie)
WHERE toLower(r.summary) CONTAINS 'fun'
RETURN  m.title as Movie, r.summary as Review, r.rating as Rating

// Retrieve all people who have produced a movie, but have not directed a movie, returning their names and the movie titles.
MATCH (a:Person)-[:PRODUCED]->(m:Movie)
WHERE NOT ((a)-[:DIRECTED]->(:Movie))
RETURN a.name, m.title

// Retrieve the movies and their actors where one of the actors also directed the movie, returning the actors names, the director’s name, and the movie title.
MATCH (a1:Person)-[:ACTED_IN]->(m:Movie)<-[:ACTED_IN]-(a2:Person)
WHERE exists( (a2)-[:DIRECTED]->(m) )
RETURN  a1.name as Actor, a2.name as `Actor/Director`, m.title as Movie

// Retrieve all movies that were released in the years 2000, 2004, and 2008, returning their titles and release years.
MATCH (m:Movie)
WHERE m.released IN [2000, 2004, 2008]
RETURN m.title, m.released

// Retrieve the movies that have an actor’s role that is the name of the movie, return the movie title and the actor’s name.
MATCH (a:Person)-[r:ACTED_IN]->(m:Movie)
WHERE m.title in r.roles
RETURN  m.title as Movie, a.name as Actor
```

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
