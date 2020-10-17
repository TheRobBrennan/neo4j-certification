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

#### Traversal in a MATCH clause

```
// Find all of the followers of people who reviewed the movie, The Replacements
MATCH (follower:Person)-[:FOLLOWS]->(reviewer:Person)-[:REVIEWED]->(m:Movie)
WHERE m.title = 'The Replacements'
RETURN follower.name, reviewer.name
```

Here is the traversal that the graph engine performed. It first found the movie, The Replacements. Then it found all Person nodes that reviewed that movie, Angela, Jessica, and James. Then it found all Person nodes who follow the people who reviewed the movie, Paul, Angela, and James. In all, six relationships were traversed.

![https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/TheReplacementsTraversal.png](https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/TheReplacementsTraversal.png)

#### Specifying multiple patterns in a MATCH

Suppose we want to write queries that focus on movies released in the year 2000:

```
// Returns Person nodes for people who acted in these three movies from 2000 and using that same movie node,m it retrieves the Person node who is the director for that movie, m
MATCH (a:Person)-[:ACTED_IN]->(m:Movie),
      (m)<-[:DIRECTED]-(d:Person)
WHERE m.released = 2000
RETURN a.name, m.title, d.name

// However, a better way to write this same query would be
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(d:Person)
WHERE m.released = 2000
RETURN a.name, m.title, d.name
```

#### Example: Using two patterns in a MATCH

In the first example, we want the actors that worked with Keanu Reeves to meet Hugo Weaving, who has worked with Keanu Reeves. Here we retrieve the actors who acted in the same movies as Keanu Reeves, but not when Hugo Weaving acted in the same movie. To do this, we specify two paths for the MATCH:

```
MATCH (keanu:Person)-[:ACTED_IN]->(movie:Movie)<-[:ACTED_IN]-(n:Person),
     (hugo:Person)
WHERE keanu.name='Keanu Reeves' AND
      hugo.name='Hugo Weaving'
AND NOT (hugo)-[:ACTED_IN]->(movie)
RETURN n.name
```

Here is another example where two patterns are necessary.

Suppose we want to retrieve the movies that Meg Ryan acted in and their respective directors, as well as the other actors that acted in these movies. Here is the query to do this:

```
MATCH (meg:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(d:Person),
      (other:Person)-[:ACTED_IN]->(m)
WHERE meg.name = 'Meg Ryan'
RETURN m.title as movie, d.name AS director , other.name AS `co-actors`
```

#### Specifying varying length paths

Any graph that represents social networking, trees, or hierarchies will most likely have multiple paths of varying lengths. Think of the connected relationship in LinkedIn and how connections are made by people connected to more people.

```
// Find all of the followers of the followers of a Person by specifying a numeric value for the number of hops in the path. Here is an example where we want to retrieve all Person nodes that are exactly two hops away
MATCH (follower:Person)-[:FOLLOWS*2]->(p:Person)
RETURN follower.name AS `Follower of a follower of`, p.name AS `Person`
```

If we had specified [:FOLLOWS*] rather than [:FOLLOWS*2], the query would return all Person nodes that are in the :FOLLOWS path from Paul Blythe:

```
MATCH (follower:Person)-[:FOLLOWS]->(p:Person) RETURN follower.name AS `Follower of`, p.name AS `Person`

Person	          Follower of Person
"Paul Blythe"	    "Angela Scope"
"James Thompson"	"Jessica Thompson"
"Angela Scope"	  "Jessica Thompson"
```

#### Finding the shortest path

A built-in function that you may find useful in a graph that has many ways of traversing the graph to get to the same node is the `shortestPath()` function. Using the shortest path between two nodes improves the performance of the query.

```
// Discover the shortest path between the movies The Matrix and A Few Good Men. Notice that we specify `*` for the relationship. This means any relationship; for the traversal.
MATCH p = shortestPath((m1:Movie)-[*]-(m2:Movie))
WHERE m1.title = 'A Few Good Men' AND
      m2.title = 'The Matrix'
RETURN  p
```

#### Returning a subgraph

In using `shortestPath()`, the return type is a path. A subgraph is essentially as set of paths derived from your MATCH clause.

```
MATCH paths = (m:Movie)-[rel]-(p:Person)
WHERE m.title = 'The Replacements'
RETURN paths
```

The APOC library is very useful if you want to query the graph to obtain subgraphs.

#### Specifying optional pattern matching

`OPTIONAL MATCH` matches patterns with your graph, just like `MATCH` does. The difference is that if no matches are found, `OPTIONAL MATCH` will use nulls for missing parts of the pattern. `OPTIONAL MATCH` could be considered the Cypher equivalent of the outer join in SQL.

```
// Here is an example where we query the graph for all people whose name starts with James. The OPTIONAL MATCH is specified to include people who have reviewed movies
MATCH (p:Person)
WHERE p.name STARTS WITH 'James'
OPTIONAL MATCH (p)-[r:REVIEWED]->(m:Movie)
RETURN p.name, type(r), m.title
```

Notice that for all rows that do not have the `:REVIEWED` relationship, a `null` value is returned for the movie part of the query, as well as the relationship.

#### Exercise 5: Working with patterns in queries

```
// Write a Cypher query that retrieves all movies that Gene Hackman has acted in, along with the directors of the movies. In addition, retrieve the actors that acted in the same movies as Gene Hackman. Return the name of the movie, the name of the director, and the names of actors that worked with Gene Hackman.
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(d:Person),
      (a2:Person)-[:ACTED_IN]->(m)
WHERE a.name = 'Gene Hackman'
RETURN m.title as movie, d.name AS director , a2.name AS `co-actors`

// Retrieve all nodes that the person named James Thompson directly has the FOLLOWS relationship in either direction.
MATCH (p1:Person)-[:FOLLOWS]-(p2:Person)
WHERE p1.name = 'James Thompson'
RETURN p1, p2

// Modify the query to retrieve nodes that are exactly three hops away.
// Notice here that the relationships between the nodes are not displayed because James Thompson is not connected to Paul Blythe.
MATCH (p1:Person)-[:FOLLOWS*3]-(p2:Person)
WHERE p1.name = 'James Thompson'
RETURN p1, p2

// Modify the query to retrieve nodes that are one and two hops away.
MATCH (p1:Person)-[:FOLLOWS*1..2]-(p2:Person)
WHERE p1.name = 'James Thompson'
RETURN p1, p2

// Modify the query to retrieve all nodes that are connected to James Thompson by a Follows relationship no matter how many hops are required.
MATCH (p1:Person)-[:FOLLOWS*]-(p2:Person)
WHERE p1.name = 'James Thompson'
RETURN p1, p2

// Write a Cypher query to retrieve all people in the graph whose name begins with Tom and optionally retrieve all people named Tom who directed a movie.
MATCH (p:Person)
WHERE p.name STARTS WITH 'Tom'
OPTIONAL MATCH (p)-[:DIRECTED]->(m:Movie)
RETURN p.name, m.title
```

### Working with Cypher Data

#### Using count() to aggregate

A common way to aggregate data in Cypher is to count. Cypher has a `count()` function that you can use to perform a count of nodes, relationships, paths, rows during query processing. When you aggregate in a Cypher query, this means that the query must process all patterns in the MATCH clause to complete the aggregation to either return results or perform the next part of the query.

```
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(d:Person)
RETURN a.name, d.name, count(m)
```

Aggregation in Cypher is different from aggregation in SQL. In Cypher, you need not specify a grouping key.

#### Collecting results

Cypher has a built-in function, collect() that enables you to aggregate a value into a list. The value can be a property value, a node, a relationship, or a path.

```
// Collect the list of movie titles that Tom Cruise acted in
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE p.name ='Tom Cruise'
RETURN collect(m.title) AS `movies for Tom Cruise`
```

#### Collecting nodes

```
// Collect the nodes for movie titles that Tom Cruise acted in
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE p.name ='Tom Cruise'
RETURN collect(m) AS `movies for Tom Cruise`
```

#### Counting and collecting

The Cypher `count()` function is very useful when you want to count the number of occurrences of a particular query result. If you specify `count(n)`, the graph engine calculates the number of occurrences of _n_. If you specify count(\*), the graph engine calculates the number of rows retrieved, including those with null values. When you use `count()`, the graph engine does an implicit “group by” based upon the aggregation.

```
// Count the paths retrieved where an actor and director collaborated in a movie and the count() function is used to count the number of paths found for each actor/director collaboration
MATCH (actor:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(director:Person)
RETURN actor.name, director.name,
       count(m) AS collaborations, collect(m.title) AS movies
```

Here is an alternative to using `count()` that returns the same results, but with using `size()`:

```
MATCH (actor:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(director:Person)
RETURN actor.name, director.name, size(collect(m)) AS collaborations,
       collect(m.title) AS movies
```

#### Lists

```
// Return the cast list for every movie, as well as the size of the cast
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
RETURN m.title, collect(a) as cast, size(collect(a)) as castSize
```

Notice that when viewing nodes in table view, each node is shown with {} notation and key value pairs. This structure is called a map in Cypher.

```
// We can adjust this query slightly so that the list contains the names, rather than the entire set of Person node properties.
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
RETURN m.title, collect(a.name) as cast, size(collect(a.name)) as castSize

// Return the first cast member for each movie
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
RETURN m.title, collect(a.name)[0] as `A cast member`,
       size(collect(a.name)) as castSize
```

#### Working with maps

A Cypher map is list of key/value pairs where each element of the list is of the format ‘key’: value. For example, a map of months and the number of days per month could be:

```
RETURN {Jan: 31, Feb: 28, Mar: 31, Apr: 30 , May: 31, Jun: 30 ,
       Jul: 31, Aug: 31, Sep: 30, Oct: 31, Nov: 30, Dec: 31}['Feb'] AS DaysInFeb
```

#### Map projections

Map projections are when you can use retrieved nodes to create or return some of the information in the nodes. A Movie node can have the properties title, released, and tagline. Suppose we want to return the Movie node information, but without the tagline property? You can do so as follows using map projections:

```
MATCH (m:Movie)
WHERE m.title CONTAINS 'Matrix'
RETURN m { .title, .released } AS movie
```

#### Working with dates

```
RETURN date(), datetime(), time(), timestamp();

// Since both date() and datetime() store their values as strings, you can use properties such as day, year, time to extract the values that you need
RETURN date().day, date().year, datetime().year, datetime().hour,
       datetime().minute;
```

#### Working with timestamp()

Working with timestamp() is different as its value is a long integer that represents time. The value of datetime().epochmillis is the same as timestamp(). To extract a month, year, or time from a timestamp, you would do the following:

```
RETURN datetime({epochmillis:timestamp()}).day,
       datetime({epochmillis:timestamp()}).year,
       datetime({epochmillis:timestamp()}).month
```

#### Exercise 6: Working with Cypher data

```
// Retrieve actors and the movies they have acted in, returning each actor’s name and the list of movies they acted in
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
RETURN p.name as actor, collect(m.title) AS `movie list`

// Retrieve all movies that Tom Cruise has acted in and the co-actors that acted in the same movie, returning the movie title and the list of co-actors that Tom Cruise worked with
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)<-[:ACTED_IN]-(p2:Person)
WHERE p.name ='Tom Cruise'
RETURN m.title as movie, collect(p2.name) AS `co-actors`

// Retrieve all people who reviewed a movie, returning the list of reviewers and how many reviewers reviewed the movie
MATCH (p:Person)-[:REVIEWED]->(m:Movie)
RETURN m.title as movie, count(p) as numReviews, collect(p.name) as reviewers

// Retrieve all directors, their movies, and people who acted in the movies, returning the name of the director, the number of actors the director has worked with, and the list of actors.
MATCH (d:Person)-[:DIRECTED]->(m:Movie)<-[:ACTED_IN]-(a:Person)
RETURN d.name AS director, count(a) AS `number actors` , collect(a.name) AS `actors worked with`

// Write a query that returns a map of all movies that Tom Hanks acted in. The map returned only contains the title of the movie and the year released for the movie.
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WHERE a.name = 'Tom Hanks'
RETURN  m {.title, .released}

// Write a query that retrieves all movies that Tom Hanks acted in, returning the title of the movie, the year the movie was released, the number of years ago that the movie was released, and the age of Tom when the movie was released.
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WHERE a.name = 'Tom Hanks'
RETURN  m.title, m.released, date().year  - m.released as yearsAgoReleased, m.released  - a.born AS `age of Tom`
```

#### Check your understanding

What functions below aggregate results?

[X] count()

[] size()

[] map()

[X] collect()

What construct best represents a node in the graph?

[] list

[X] map

[] collection

[] blob

### Controlling the Query Chain

#### Intermediate processing using WITH

Here is an example where we return each actor, the number of movies they acted in, and the titles of the movies:

```
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
RETURN a.name, count(a) AS numMovies,
       collect(m.title) as movies
```

What if we wanted to further qualify the query so that we only return the actors who have made 2 or 3 movies? What we really want to do is test the count of each movie, but the count is an aggregating function so we cannot test it until all nodes have been retrieved. One way to to this is with the WITH clause in Cypher.

##### Example: Using WITH

You use the WITH clause to perform intermediate processing that is not possible in a RETURN clause.

```
MATCH (a:Person)-[:ACTED_IN]->(m:Movie) // retrieving all actors and their movies
WITH  a, count(a) AS numMovies, collect(m.title) as movies  // use WITH to perform intermediate processing like count(), collect()...
WHERE 1 < numMovies < 4
RETURN a.name, numMovies, movies
```

When you use the WITH clause, you specify the variables from the previous part of the query you want to pass on to the next part of the query.

#### Using WITH and UNWIND

You have learned to create lists of nodes during a query using the collect() function. If you have collected a subset of nodes in a query, you can use UNWIND to return the rows for a collection:

```
MATCH (m:Movie)<-[:ACTED_IN]-(p:Person)
WITH collect(p) AS actors,
count(p) AS actorCount, m
UNWIND actors AS actor
RETURN m.title, actorCount, actor.name
```

The query retrieves all people who acted in movies. During the query, for each movie, the list of actors are collected, as well as the count of actors. The WITH clause makes the variables actors, actorCount, and m available for the rest of the query processing. The UNWIND clause turns the list of actors into rows of actors. Then the query returns the title of the movie, the actor count, and the actor name for each row of the actors collection.

You find that you use WITH and UNWIND frequently when you are importing data into the graph.

##### Example: Subqueries with WITH

Here is an example where we retrieve all movies reviewed by a person. For a particular movie found, we want the list of directors of the movie so we do a second query, a subquery as follows:

```
MATCH (m:Movie)<-[rv:REVIEWED]-(r:Person)
WITH m, rv, r
MATCH (m)<-[:DIRECTED]-(d:Person)
RETURN m.title, rv.rating, r.name, collect(d.name)
```

##### Example: Another subquery

Here is another example where we want to find all actors who have acted in at least five movies, and find (optionally) the movies they directed and return the person and those movies.

```
MATCH (p:Person)
WITH p, size((p)-[:ACTED_IN]->()) AS movies
WHERE movies >= 5
OPTIONAL MATCH (p)-[:DIRECTED]->(m:Movie)
RETURN p.name, m.title
```

In this example, we first retrieve all people, but then specify a pattern in the WITH clause where we calculate the number of :ACTED_IN relationships retrieved using the size() function. If this value is greater than five, we then also retrieve the :DIRECTED paths to return the name of the person and the title of the movie they directed. In the result, we see that these actors acted in more than five movies, but Tom Hanks is the only actor who directed a movie and thus the only person to have a value for the movie. Notice here that m only refers to movies that were directed by p.

#### Performing subqueries with CALL

In a CALL clause, you specify a query that returns, typically a set of nodes. The set of nodes returned in the CALL clause can be used for a subsequent query.

```
CALL
{MATCH (p:Person)-[:REVIEWED]->(m:Movie)
RETURN  m}
MATCH (m) WHERE m.released=2000
RETURN m.title, m.released
```

The variable m used in the subquery is used in the next query.

#### Exercise 7: Controlling query processing

```
// Retrieve the actors who have acted in exactly five movies, returning the name of the actor, and the list of movies for that actor.
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WITH  a, count(m) AS numMovies, collect(m.title) AS movies
WHERE numMovies = 5
RETURN a.name, movies

// Retrieve all actors that have not appeared in more than 3 movies. Return their names and list of movies.
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WITH  a,  count(a) AS numMovies, collect(m.title) AS movies
WHERE numMovies <= 3
RETURN a.name, movies

// Retrieve the movies that have at least 2 directors, and optionally the names of people who reviewed the movies.
MATCH (m:Movie)
WITH m, size((:Person)-[:DIRECTED]->(m)) AS directors
WHERE directors >= 2
OPTIONAL MATCH (p:Person)-[:REVIEWED]->(m)
RETURN  m.title, p.name

// Write a Cypher query that retrieves all actors that acted in movies, and collects the list of movies for any actor that acted in more than five movies. Return the name of the actor and the list.
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WITH p, collect(m) AS movies
WHERE size(movies)  > 5
RETURN p.name, movies

// Modify the query you just wrote so that before the query processing ends, you unwind the list of movies and then return the name of the actor and the title of the associated movie
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WITH p, collect(m) AS movies
WHERE size(movies)  > 5
WITH p, movies UNWIND movies AS movie
RETURN p.name, movie.title
```

### Controlling Results Returned

#### Example with duplicate results

Here is a simple example where duplicate data is returned. Tom Hanks both acted in and directed the movie, That Thing You Do, so the movie is returned twice in the result stream:

```
MATCH (p:Person)-[:DIRECTED | ACTED_IN]->(m:Movie)
WHERE p.name = 'Tom Hanks'
RETURN m.title, m.released

// We can eliminate the duplication by specifying the DISTINCT keyword
MATCH (p:Person)-[:DIRECTED | ACTED_IN]->(m:Movie)
WHERE p.name = 'Tom Hanks'
RETURN DISTINCT m.title, m.released
```

#### Duplication in lists

You can also specify DISTINCT when collecting elements for a list. Here is another query where we collect the names of people who acted in, directed, or wrote movies released in 2003.

```
MATCH (p:Person)-[:ACTED_IN | DIRECTED | WROTE]->(m:Movie)
WHERE m.released = 2003
RETURN m.title, collect(p.name) AS credits

// We can eliminate the duplication by specifying the DISTINCT keyword when collecting the results
MATCH (p:Person)-[:ACTED_IN | DIRECTED | WROTE]->(m:Movie)
WHERE m.released = 2003
RETURN m.title, collect(DISTINCT p.name) AS credits
```

#### Using WITH and DISTINCT to eliminate duplication

Another way that you can avoid duplication is to use WITH and DISTINCT together as follows:

```
MATCH (p:Person)-[:DIRECTED | ACTED_IN]->(m:Movie)
WHERE p.name = 'Tom Hanks'
WITH DISTINCT m
RETURN m.released, m.title
```

#### Ordering results

```
MATCH (p:Person)-[:DIRECTED | ACTED_IN]->(m:Movie)
WHERE p.name = 'Tom Hanks' OR p.name = 'Keanu Reeves'
RETURN DISTINCT m.title, m.released ORDER BY m.released DESC
```

#### Ordering multiple results

```
MATCH (p:Person)-[:DIRECTED | ACTED_IN]->(m:Movie)
WHERE p.name = 'Tom Hanks' OR p.name = 'Keanu Reeves'
RETURN DISTINCT m.title, m.released ORDER BY m.released DESC , m.title
```

#### Limiting the number of results

```
MATCH (m:Movie)
RETURN m.title as title, m.released as year ORDER BY m.released DESC LIMIT 10
```

#### Limiting number of intermediate results

Furthermore, you can use the LIMIT keyword with the WITH clause to limit intermediate results. A best practice is to limit the number of rows processed before they are collected. Here is an example where we want to limit the number of actors collected in this query by the number 6:

```
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WITH m, p LIMIT 6
RETURN collect(p.name), m.title
```

#### Another example using LIMIT

Here is another example of limiting results. Suppose we want to retrieve five movies and for each movie, return the :ACTED_IN path to at most two actors. Here is one way to perform this query:

```
MATCH (m:Movie)
WITH m LIMIT 5
MATCH path = (m)<-[:ACTED_IN]-(:Person)
WITH m, collect (path) AS paths
RETURN m, paths[0..2]
```

#### Alternative to LIMIT

```
// Count the number of movies during the query and we return the results once we have reached 5 movies
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WITH a, count(*) AS numMovies, collect(m.title) AS movies
WHERE numMovies = 5
RETURN a.name, numMovies, movies

// An alternative to the above code is:
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WITH a, collect(m.title) AS movies
WHERE size(movies) = 5
RETURN a.name, movies
```

#### Exercise 8: Controlling results returned

```
// You want to know what actors acted in movies in the decade starting with the year 1990. First write a query to retrieve all actors that acted in movies during the 1990s, where you return the released date, the movie title, and the collected actor names for the movie. For now do not worry about duplication.
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WHERE m.released >= 1990 AND m.released < 2000
RETURN m.released, m.title, collect(a.name)

// The results returned from the previous query include multiple rows for a movie released value. Next, modify the query so that the released date records returned are not duplicated. To implement this, you must add the collection of the movie titles to the results returned.
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WHERE m.released >= 1990 AND m.released < 2000
RETURN  m.released, collect(m.title), collect(a.name)

// The results returned from the previous query returns the collection of movie titles with duplicates. That is because there are multiple actors per released year. Next, modify the query so that there is no duplication of the movies listed for a year.
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WHERE m.released >= 1990 AND m.released < 2000
RETURN  m.released, collect(DISTINCT m.title), collect(a.name)

// Modify the query that you just wrote to order the results returned so that the more recent years are displayed first.
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WHERE m.released >= 1990 AND m.released < 2000
RETURN  m.released, collect(DISTINCT m.title), collect(a.name)
ORDER BY m.released DESC

// Retrieve the top 5 ratings and their associated movies, returning the movie title and the rating.
MATCH (:Person)-[r:REVIEWED]->(m:Movie)
RETURN  m.title AS movie, r.rating AS rating
ORDER BY r.rating DESC LIMIT 5
```

### Creating Nodes

#### Syntax: Creating nodes

```
// Add a node to the graph of type Movie with the title, Batman Begins
CREATE (:Movie {title: 'Batman Begins'})

// Add a node with two labels to the graph of types Movie and Action with the title, Batman Begins.
CREATE (:Movie:Action {title: 'Batman Begins'})

// Add a node to the graph of types Movie and Action with the title, Batman Begins
CREATE (m:Movie:Action {title: 'Batman Begins'})

// Add a node to the graph of types Movie and Action with the title Batman Begins and return the title
CREATE (m:Movie:Action {title: ' Batman Begins'})
RETURN m.title

```

#### Creating multiple nodes

You can create multiple nodes by simply separating the nodes specified with commas, or by specifying multiple CREATE statements.

```
// Create some Person nodes that will represent some of the people associated with the movie Batman Begins
CREATE
(:Person {name: 'Michael Caine', born: 1933}),
(:Person {name: 'Liam Neeson', born: 1952}),
(:Person {name: 'Katie Holmes', born: 1978}),
(:Person {name: 'Benjamin Melniker', born: 1913})
```

The graph engine will create a node with the same properties of a node that already exists. You can prevent this from happening in one of two ways:

- You can use MERGE rather than CREATE when creating the node.
- You can add constraints to your graph.

#### Syntax: Adding labels to a node

```
// Example: Adding labels to a node
MATCH (m:Movie)
WHERE m.title = 'Batman Begins'
SET m:Fantasy       // To set multiple labels for variable m -> SET m:Fantasy:Label2
RETURN labels(m)    // Note we are using the built-in labels() function here
```

#### Syntax: Removing labels from a node

If you attempt to remove a label from a node for which the label does not exist, it is ignored.

```
// Example: Removing labels from a node
MATCH (m:Action)
REMOVE m:Action, m:Fantasy
RETURN labels(m)
```

#### Syntax: Adding properties to a node

Here are simplified syntax examples for adding properties to a node referenced by the variable x:

```
// Set a single property
SET x.propertyName = value

// Set multiple properties
SET x.propertyName1 = value1, x.propertyName2 = value2

// Replace all properties of the node with these properties
SET x = {propertyName1: value1, propertyName2: value2}

// Update and/or create these new properties for the selected node
SET x += {propertyName1: value1, propertyName2: value2}
```

If the property does not exist, it is added to the node. If the property exists, its value is updated. If the value specified is null, the property is removed.

```
// Example: Adding properties to a node
MATCH (m:Movie)
WHERE m.title = 'Batman Begins'
SET m.released = 2005, m.lengthInMinutes = 140
RETURN m

// Example 2: Adding properties to node
MATCH (m:Movie)
WHERE m.title = 'Batman Begins'
SET  m = {title: 'Batman Begins',
          released: 2005,
          lengthInMinutes: 140,
          videoFormat: 'DVD',
          grossMillions: 206.5}
RETURN m
```

#### Retrieving properties of a node

```
MATCH (m:Movie)
WHERE m.title = 'Batman Begins'
RETURN properties(m)
```

#### Updating properties – JSON-style

Here is an example where we use the JSON-style object to add the awards property to the node and update the grossMillions property:

```
MATCH (m:Movie)
WHERE m.title = 'Batman Begins'
SET  m += { grossMillions: 300,
            awards: 66}
RETURN m
```

#### Syntax: Removing properties from a node

There are two ways that you can remove a property from a node. One way is to use the REMOVE keyword. The other way is to set the property’s value to null:

```
REMOVE x.propertyName
SET x.propertyName = null

// Example: Removing properties from a node using both techniques
MATCH (m:Movie)
WHERE m.title = 'Batman Begins'
SET m.grossMillions = null
REMOVE m.videoFormat
RETURN m
```

#### Exercise 9: Creating nodes

```
// Create a Movie node for the movie with the title, Forrest Gump
CREATE (:Movie {title: 'Forrest Gump'})

// Create a Person node for the person with the name, Robin Wright
CREATE (:Person {name: 'Robin Wright'})

// Add the label OlderMovie to any Movie node that was released before 2010
MATCH (m:Movie)
WHERE m.released < 2010
SET m:OlderMovie
RETURN DISTINCT labels(m)

// Retrieve all older movie nodes to test that the label was indeed added to these nodes.
MATCH (m:OlderMovie)
RETURN m.title, m.released

// Add the label Female to all Person nodes that has a person whose name starts with Robin
MATCH (p:Person)
WHERE p.name STARTS WITH 'Robin'
SET p:Female

// Retrieve all Female nodes. Do you notice that not all of these nodes should be labeled Female?
MATCH (p:Female)
RETURN p.name

// We’ve decided to not use the Female label. Remove the Female label from the nodes that have this label.
MATCH (p:Female)
REMOVE p:Female

// View the current schema of the graph.
CALL db.schema.visualization()

// Add properties to a movie
MATCH (m:Movie)
WHERE m.title = 'Forrest Gump'
SET m:OlderMovie,
    m.released = 1994,
    m.tagline = "Life is like a box of chocolates...you never know what you're gonna get.",
    m.lengthInMinutes = 142

// Retrieve this OlderMovie node to confirm that the properties and label have been properly set
MATCH (m:OlderMovie)
WHERE m.title = 'Forrest Gump'
RETURN m
```

### Creating Relationships

#### Syntax: Creating relationships

Here is the simplified syntax for creating a relationship between two nodes referenced by the variables x and y:

```
CREATE (x)-[:REL_TYPE]->(y)
```

When you create the relationship, it must have direction. You can query nodes for a relationship in either direction, but you must create the relationship with a direction.

In most cases, unless you are connecting nodes at creation time, you will retrieve the two nodes, each with their own variables, for example, by specifying a WHERE clause to find them, and then use the variables to connect them.

```
// Connect the actor, Michael Caine with the movie, Batman Begins. We first retrieve the nodes of interest, then we create the relationship
MATCH (a:Person), (m:Movie)
WHERE a.name = 'Michael Caine' AND m.title = 'Batman Begins'
CREATE (a)-[:ACTED_IN]->(m)
RETURN a, m
```

Before you run this Cypher code, you may see a warning in Neo4j Browser that you are creating a query that is a cartesian product that could potentially be a performance issue.

If you are familiar with the data in the graph and can be sure that the MATCH clauses will not retrieve large amounts of data, you can continue.

In our case, we are simply looking up a particular Person node and a particular Movie node so we can create the relationship.

#### Creating multiple relationships

Here is an example where we have already created Person nodes for an actor, Liam Neeson, and a producer, Benjamin Melniker. We create two relationships in this example, one for :ACTED_IN and one for :PRODUCED

```
MATCH (a:Person), (m:Movie), (p:Person)
WHERE a.name = 'Liam Neeson' AND
      m.title = 'Batman Begins' AND
      p.name = 'Benjamin Melniker'
CREATE (a)-[:ACTED_IN]->(m)<-[:PRODUCED]-(p)
RETURN a, m, p
```

When you create relationships based upon a MATCH clause, you must be certain that only a single node is returned for the MATCH, otherwise multiple relationships will be created.

#### Creating a relationship with properties

```
MATCH (a:Person), (m:Movie)
WHERE a.name = 'Katie Holmes' AND m.title = 'Batman Begins'
CREATE (a)-[rel:ACTED_IN {roles: ['Rachel','Rachel Dawes']}->(m)
RETURN a.name, rel, m.title
```

#### Creating nodes and relationships together

```
MATCH (m:Movie)
WHERE m.title = 'Batman Begins'
CREATE (a:Person)-[:ACTED_IN]->(m)
SET a.name = 'Gary Oldman', a.born=1958
RETURN a, m, p
```

#### Syntax: Adding properties to relationships

Adding properties to a relationship is the same as our previous look at adding properties to a node. The only difference, of course, is that we must have identified a relationship instead of a node to modify.

```
MATCH (a:Person), (m:Movie)
WHERE a.name = 'Christian Bale' AND m.title = 'Batman Begins'
CREATE (a)-[rel:ACTED_IN]->(m)
SET rel.roles = ['Bruce Wayne','Batman']
RETURN a, rel, m
```

If the relationship had multiple properties, we could have added them as a comma separated list or as an object, like you can do for node properties.

```
// Example: Adding properties inline
MATCH (a:Person), (m:Movie)
WHERE a.name = 'Christian Bale' AND m.title = 'Batman Begins'
CREATE (a)-[:ACTED_IN {roles: ['Bruce Wayne', 'Batman']}]->(m)
RETURN a, m
```

By default, the graph engine will create a relationship between two nodes, even if one already exists. This could be dangerous so in your code make sure that if you use CREATE to create a relationship, it does not already exist.

```
// Example: Testing before creating relationship
MATCH (a:Person),(m:Movie)
WHERE a.name = 'Christian Bale' AND
      m.title = 'Batman Begins' AND
      NOT exists((a)-[:ACTED_IN]->(m))
CREATE (a)-[rel:ACTED_IN]->(m)
SET rel.roles = ['Bruce Wayne','Batman']
RETURN a, rel, m
```

The best way to prevent duplication of relationships is to use the MERGE clause, rather than the CREATE clause. You will learn about merging data later in this course.

#### Removing properties from a relationship

There are two ways that you can remove a property from a node. One way is to use the REMOVE keyword. The other way is to set the property’s value to null, just as you do for properties of nodes.

```
MATCH (a:Person)-[rel:ACTED_IN]->(m:Movie)
WHERE a.name = 'Christian Bale' AND m.title = 'Batman Begins'
REMOVE rel.roles    // ALTERNATIVE: SET rel.roles = null
RETURN a, rel, m
```

#### Exercise 10: Creating relationships

```
// Create the ACTED_IN relationship between the actors, Robin Wright, Tom Hanks, and Gary Sinise and the movie, Forrest Gump
MATCH (m:Movie)
WHERE m.title = 'Forrest Gump'
MATCH (p:Person)
WHERE p.name = 'Tom Hanks' OR p.name = 'Robin Wright' OR p.name = 'Gary Sinise'
CREATE (p)-[:ACTED_IN]->(m)

// Return all people connected to the movie, Forrest Gump, along with their relationships.
MATCH (p:Person)-[rel]-(m:Movie)
WHERE m.title = 'Forrest Gump'
RETURN p, rel, m

// Add the roles property to the three ACTED_IN relationships that you just created to the movie, Forrest Gump using this information: Tom Hanks played the role, Forrest Gump. Robin Wright played the role, Jenny Curran. Gary Sinise played the role, Lieutenant Dan Taylor.
MATCH (p:Person)-[rel:ACTED_IN]->(m:Movie)
WHERE m.title = 'Forrest Gump'
SET rel.roles =
CASE p.name
  WHEN 'Tom Hanks' THEN ['Forrest Gump']
  WHEN 'Robin Wright' THEN ['Jenny Curran']
  WHEN 'Gary Sinise' THEN ['Lieutenant Dan Taylor']
END

// Add a new property, research to the HELPED relationship between Tom Hanks and Gary Sinise and set this property’s value to war history.
MATCH (p1:Person)-[rel:HELPED]->(p2:Person)
WHERE p1.name = 'Tom Hanks' AND p2.name = 'Gary Sinise'
SET rel.research = 'war history'

// Modify the role that Gary Sinise played in the movie, Forrest Gump from Lieutenant Dan Taylor to Lt. Dan Taylor
MATCH (p:Person)-[rel:ACTED_IN]->(m:Movie)
WHERE m.title = 'Forrest Gump' AND p.name = 'Gary Sinise'
SET rel.roles =['Lt. Dan Taylor']
```

### Deleting Nodes and Relationships

#### Deleting nodes

```
// Assuming one or more nodes match
MATCH (p:Person)
WHERE p.name = 'Jane Doe'
DELETE p
```

You can delete a node provided:

- You can obtain a reference to it (typically using MATCH).
- The node has no relationships.

So if you accidentally created multiple nodes in the graph, you can retrieve them with a MATCH clause and delete them.

#### Deleting relationships

Provided you have a reference to a relationship, you can delete it:

```
MATCH (a:Person)-[rel:WROTE | DIRECTED]->(m:Movie)
WHERE a.name = 'Katie Holmes' AND m.title = 'Batman Begins'
DELETE rel
RETURN a, m

// You can confirm that there is only one relationship associated with the Katie Holmes node as follows
MATCH (a:Person)-[rel]-()
WHERE a.name = 'Katie Holmes'
RETURN count(rel) AS `Number of Katie Holmes relationships:`
```

#### Deleting nodes and relationships

The most efficient way to delete a node and its corresponding relationships is to specify DETACH DELETE. When you specify DETACH DELETE for a node, the relationships to and from the node are deleted, then the node is deleted.

```
MATCH (p:Person)
WHERE p.name = 'Liam Neeson'
DETACH DELETE  p
```

#### Exercise 11: Deleting nodes and relationships

```
// Delete the HELPED relationship from the graph.
MATCH (:Person)-[rel:HELPED]-(:Person)
DELETE rel

// Query the graph to display Forrest Gump and all of its relationships.
MATCH (p:Person)-[rel]-(m:Movie)
WHERE m.title = 'Forrest Gump'
RETURN p, rel, m

// Try deleting the Forrest Gump node without detaching its relationships. You should receive an error "Cannot delete node<###>, because it still has relationships. To delete this node, you must first delete its relationships."
MATCH (m:Movie)
WHERE m.title = 'Forrest Gump'
DELETE m

// Delete Forrest Gump, along with its relationships in the graph.
MATCH (m:Movie)
WHERE m.title = 'Forrest Gump'
DETACH DELETE m
```

### Merging Data in the Graph

#### Creating data in the graph

Here are some facts about creating nodes and relationships in the graph when no constraints have been defined:

| CREATE       | Result is...                                                                                                                                                                                                                                   |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Node         | If a node with the same property values exists, a duplicate node is created.                                                                                                                                                                   |
| Label        | If the label already exists for the node, the node is not updated.                                                                                                                                                                             |
| Property     | If the node or relationship property already exists, it is updated with the new value. Note: If you specify a set of properties to be created using `=` rather than `+=`, existing properties are removed if they are not included in the set. |
| Relationship | If the relationship exists, a duplicate relationship is created.                                                                                                                                                                               |

#### Using MERGE

The MERGE clause is used to find nodes or patterns in the graph. If the node or pattern is not found, by default, it is created.

You use the simple MERGE clause to:

- Create a unique node based on label and key information for a property or set of properties.
- Update a node based on label and key information for a property or set of properties.
- Create a unique relationship between two nodes.
- Create a unique node and relationship in the context of another node.

#### Syntax: Using MERGE to create nodes

```
MERGE (a:Actor {name: 'Michael Caine'})
SET a.born = 1933
RETURN a
```

Be mindful that node labels and the properties for a node are significant when merging nodes.

#### Syntax: Using MERGE to create relationships

If there is an existing node with Label1 and nodeProperties1 with the :REL_TYPE relationship to an existing node with Label2 and nodeProperties2 in the graph, no relationship is created. If the relationship does not exist, the relationship is created.

```
MATCH (p {name: 'Michael Caine'}),(m:Movie {title:'Batman Begins'})
MERGE (p)-[:ACTED_IN]->(m)
RETURN p,m
```

Since the relationship between the Person node and the Movie node already exists, it is not created. The relationship between the Actor node and the Movie node is created with this merge.

Although, you can leave out the direction of the relationship being created with the MERGE, in which case a left-to-right arrow will be assumed, a best practice is to always specify the direction of the relationship. However, if you have bidirectional relationships and you want to avoid creating duplicate relationships, you must leave off the arrow.

#### Specifying creation behavior when merging

You can use the MERGE clause, along with ON CREATE to assign specific values to a node being created as a result of an attempt to merge.

```
MERGE (a:Person {name: 'Sir Michael Caine'})
ON CREATE SET a.birthPlace = 'London',
              a.born = 1934
RETURN a
```

We know that there are no existing Sir Michael Caine Person nodes. When the MERGE executes, it will not find any matching nodes so it will create one and will execute the ON CREATE clause where we set the birthplace and born property values.

```
// Example: Verifying the merge
MATCH (p)-[*0..1]-(m)
WHERE p.name CONTAINS 'Caine'
RETURN p, m
```

#### Specifying update behavior when merging

You can also specify an ON MATCH clause during merge processing. If the exact node is found, you can update its properties or labels. Here is an example:

```
MERGE (a:Person {name: 'Sir Michael Caine'})
ON CREATE SET a.born = 1934,
              a.birthPlace = 'UK'
ON MATCH SET a.birthPlace = 'UK'
```

#### Using MERGE to create relationships

Using MERGE to create relationships is expensive and you should only do it when you need to ensure that a relationship is unique and you are not sure if it already exists.

```
MATCH (p:Person), (m:Movie)
WHERE m.title = 'Batman Begins' AND p.name ENDS WITH 'Caine'
MERGE (p)-[:ACTED_IN]->(m)
RETURN p, m
```

You must be aware of the behavior of the MERGE clause and how it will automatically create nodes and relationships. MERGE tries to find a full pattern and if it doesn’t find it, it creates that full pattern. That’s why in most cases you should first MERGE your nodes and then your relationship afterwards.

#### Use MERGE carefully

Only if you intentionally want to create a node within the context of another (like a month within a year) then a MERGE clause with one bound and one unbound node makes sense.

```
MATCH (fromDate:Date {year: 2018})
MERGE (toDate:Date {month: 'January'})-[:IN_YEAR]->(fromDate)
```

#### Exercise 12: Merging data in the graph

```
// Use MERGE to create (ON CREATE) a node of type Movie with the title property, Forrest Gump. If created, set the released property to 1994.
MERGE (m:Movie {title: 'Forrest Gump'})
ON CREATE SET m.released = 1994
RETURN m

// Use MERGE to update (ON MATCH) a node of type Movie with the title property, Forrest Gump. If found, set the tagline property to "Life is like a box of chocolates…​you never know what you’re gonna get.".
MERGE (m:Movie {title: 'Forrest Gump'})
ON CREATE SET m.released = 1994
ON MATCH SET m.tagline = "Life is like a box of chocolates...you never know what you're gonna get."
RETURN m

// Query the graph to find labels for nodes with the title property, Forrest Gump.
MATCH (m)
WHERE m.title = 'Forrest Gump'
RETURN  labels(m)

// Execute the following Cypher statement that uses MERGE to create two nodes and a single relationship
MERGE (p:Person {name: 'Robert Zemeckis'})-[:DIRECTED]->(m {title: 'Forrest Gump'})

// IMPORTANT: The previous example Tfirst finds all Person nodes that have only the name property value of Robert Zemeckis. It then finds all nodes with only the title property set to Forrest Gump. There are no Person or other nodes that have only these properties so the graph engine creates them. Then the graph engine creates the relationship between these two nodes. That is, this MERGE operation creates two nodes and a single relationship. If we had provided all of the property values for the nodes, we would not have created the extra nodes.In fact, you should never create nodes and relationships together like this! This example is here to show you how powerful Cypher can be. A best practice is to create nodes first, then relationships.

// Find the correct Person node to delete
MATCH (p:Person {name: 'Robert Zemeckis'})-[rel]-(x)
WHERE NOT EXISTS (p.born)
RETURN p, rel, x

// Delete this Person node, along with its relationships.
MATCH (p:Person {name: 'Robert Zemeckis'})--()
WHERE NOT EXISTS (p.born)
DETACH DELETE p

// Find the correct Forrest Gump node to delete by executing this statement:
MATCH (m)
WHERE m.title = 'Forrest Gump' AND labels(m) = []
RETURN m, labels(m)

// Delete the node. It shouldn't have any relationships, so DELETE would work, but let's just use DETACH DELETE as a best practice
MATCH (m)
WHERE m.title = 'Forrest Gump' AND labels(m) = []
DETACH DELETE m

// Use MERGE to create the DIRECTED relationship between Robert Zemeckis and the Movie, Forrest Gump.
MATCH (p:Person), (m:Movie)
WHERE p.name = 'Robert Zemeckis' AND m.title = 'Forrest Gump'
MERGE (p)-[:DIRECTED]->(m)

// Use MERGE to create the ACTED_IN relationship between the actors, Tom Hanks, Gary Sinise, and Robin Wright and the Movie, Forrest Gump.
MATCH (p:Person), (m:Movie)
WHERE p.name IN ['Tom Hanks','Gary Sinise', 'Robin Wright']
      AND m.title = 'Forrest Gump'
MERGE (p)-[:ACTED_IN]->(m)

// Modify the relationship property, role for their roles in Forrest Gump
MATCH (p:Person)-[rel:ACTED_IN]->(m:Movie)
WHERE m.title = 'Forrest Gump'
SET rel.roles =
CASE p.name
  WHEN 'Tom Hanks' THEN ['Forrest Gump']
  WHEN 'Robin Wright' THEN ['Jenny Curran']
  WHEN 'Gary Sinise' THEN ['Lt. Dan Taylor']
END
```

#### Check your understanding

Given this MERGE clause. Suppose that the p and m nodes exist in the graph, but you do not know whether the relationship exists. What are your options to process this MERGE clause?

```
MATCH (p {name: 'Jane Doe'}),(m:Movie {title:'The Good One'})
MERGE (p)-[rel:ACTED_IN]->(m)
SET rel.role=['role']
```

Select the correct answers.

[X] Use the default behavior. The relationship will be created if it doesn’t exist.

[X] Specify ON CREATE to perform additional processing when the relationship is created.

[X] Specify ON MATCH to perform additional processing when the relationship is not created.

[] Specify ON DELETE to perform additional processing when the relationship is deleted.

### Defining Constraints for your Data

#### Uniqueness and existence in the graph

Unfortunately, you cannot prevent duplication by checking the existence of the exact node (with properties) as this type of test is not cluster or multi-thread safe as no locks are used. This is one reason why MERGE is preferred over CREATE, because MERGE does use locks.

A third scenario with graph data is where you want to ensure that a set of property values for nodes of the same type, have a unique value. This is the same thing as a primary key in a relational database.

In Neo4j, you can use Cypher to:

- Add a uniqueness constraint that ensures that a value for a property is unique for all nodes of that type.
- Add an existence constraint that ensures that when a node or relationship is created or modified, it must have certain properties set.
- Add a node key that ensures that a set of values for properties of a node of a given type is unique.

#### Ensuring that a property value for a node is unique

Here is an example for ensuring that the title for a node of type Movie is unique:

```
CREATE CONSTRAINT UniqueMovieTitleConstraint ON (m:Movie) ASSERT m.title IS UNIQUE
```

Although the name of the constraint, `UniqueMovieTitleConstraint` is optional, Neo4j recommends that you name it. Otherwise, it will be given an auto-generated name.

Note that you can create a uniqueness constraint, even if some Movie nodes do not have a title property.

##### Example: Uniqueness at runtime

In addition, if you attempt to modify the value of a property where the uniqueness assertion fails, the property will not be updated.

#### Ensuring that properties exist

Here is an example for adding the existence constraint to the tagline property of all Movie nodes in the graph:

```
CREATE CONSTRAINT ExistsMovieTagline ON (m:Movie) ASSERT exists(m.tagline)
```

Note that if at least one node exists that violates this constraint, it cannot be added to the graph because a node has been detected that violates the constraint.

##### Example: Adding the existence constraint

We know that in the Movie graph, all :REVIEWED relationships currently have a property, rating. We can create an existence constraint on that property as follows:

```
CREATE CONSTRAINT ExistsREVIEWEDRating
       ON ()-[rel:REVIEWED]-() ASSERT exists(rel.rating)
```

Note that we cannot remove a property from a node or relationship if it would violate any of our defined constraints.

#### Retrieving constraints defined for the graph

You can query for the set of constraints defined in the graph as follows:

```
CALL db.constraints()
```

#### Dropping constraints

You remove constraints defined for the graph with the DROP CONSTRAINT clause:

```
DROP CONSTRAINT ExistsREVIEWEDRating
```

#### Creating multi-property uniqueness/existence constraint: node key

A node key is used to define the uniqueness and existence constraint for multiple properties of a node of a certain type. A node key is also used as a composite index in the graph.

Suppose that in our Movie graph, we will not allow a Person node to be created where both the name and born properties are the same. We can create a constraint that will be a node key to ensure that this uniqueness for the set of properties is asserted.

Here is an example to create this node key:

```
CREATE CONSTRAINT UniqueNameBornConstraint
       ON (p:Person) ASSERT (p.name, p.born) IS NODE KEY
```

Note that this attempt to create the constraint will fail if there are Person nodes in the graph that do not have either the name or born properties defined.

##### Cleaning up the graph to support constraint

If we set these properties for all nodes in the graph that do not have born properties with:

```
MATCH (p:Person)
WHERE NOT exists(p.born)
SET p.born = 0
```

#### Exercise 13: Defining constraints on your data

```
// Add a uniqueness constraint named PersonNameUniqueConstraint to the Person nodes in the graph.
CREATE CONSTRAINT PersonNameUniqueConstraint ON (p:Person) ASSERT p.name IS UNIQUE

// Suppose we want to ensure that the graph will never contain a movie with the same title and the same year. For example, the movie studio decides to release a movie with the title, Back to the Future in 2018. There already is a movie in the graph with this title that was released in 1985. We want to allow this. To implement this type of constraint on the graph, you must add a constraint as a node key since it uses two properties of the node.
DROP CONSTRAINT MovieTitleConstraint
CREATE CONSTRAINT MovieTitleReleasedConstraint ON (m:Movie) ASSERT (m.title, m.released) IS NODE KEY
```

#### Check your understanding

Question 1 - What are some of the constraints you can create for the data in your graph?
Select the correct answers.

[] Property for a node with a given label is always a string value.
[X] Property value for a node with a given label is unique.
[X] Property for a node with a given label must exist.
[] Property value for a relationship is unique.

Question 2 - What types of uniqueness constraints can you define for a graph?
Select the correct answers.

[X] Unique values for a property of a node
[] Unique values for a property of a relationship
[X] Unique values for a set of properties of a node
[] Unique values for a set of properties of a relationship

Question 3 - How many properties can be defined for a NODE KEY constraint?
Select the correct answers.

[] 0
[X] 1
[X] 2
[X] unlimited

### Using Indexes

#### Indexes in Neo4j

The uniqueness and node key constraints that you add to a graph are essentially single-property and composite indexes respectively. Indexes are used to improve initial node lookup performance, but they require additional storage in the graph to maintain and also add to the cost of creating or modifying property values that are indexed. Indexes store redundant data that points to nodes with the specific property value or values. **Unlike SQL, there is no such thing as a primary key in Neo4j**. You can have multiple properties on nodes that must be unique.

Single-property indexes are used for:

- Equality checks =
- Range comparisons >,>=,<, <=
- List membership IN
- String comparisons STARTS WITH, ENDS WITH, CONTAINS
- Existence checks exists()
- Spatial distance searches distance()
- Spatial bounding searches point()

Composite indexes are used only for equality checks and list membership.

In this course, we introduce the basics of Neo4j b-tree indexes, but you should consult the Neo4j Operations Manual for more details about creating and maintaining indexes.

Because index maintenance incurs additional overhead when nodes are created, Neo4j recommends that for large graphs, indexes are created after the data has been loaded into the graph.

#### Indexes for range searches

When you add an index for a property of a node, it can greatly reduce the number of nodes the graph engine needs to visit in order to satisfy a query.

If there is an index on the released property, the graph engine will find the pointers to all nodes that satisfy the query without having to visit all of the nodes:
![https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/IndexForRanges.png](https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/IndexForRanges.png)

#### Creating a single-property index

A uniqueness constraint on a property is an index so you need not create an index for any properties you have created uniqueness constraints for. An index on its own does not guarantee uniqueness.

```
CREATE INDEX MovieReleased FOR (m:Movie) ON (m.released)
```

#### Creating composite indexes

If a set of properties for a node must be unique for every node, then you should create a constraint as a node key, rather than an index.

If, however, there can be duplication for a set of property values, but you want faster access to them, then you can create a composite index. A composite index is based upon multiple properties for a node.

##### Example: Creating a composite index

```
CREATE INDEX MovieReleasedVideoFormat FOR (m:Movie) ON (m.released, m.videoFormat)
```

#### Creating full-text schema indexes

A full-text schema index is based upon string values only, but they provide additional search capabilities that you do not get from property indexes. A full-text schema index can be used for:

- Node or relationship properties.
- Single property or multiple properties.
- Single or multiple types of nodes (labels).
- Single or multiple types of relationships.

Rather than using Cypher syntax to create a full-text schema index, you call a procedure to create the index. The index is not used implicitly when you query the graph. You must call a procedure to start a query that uses the index.

**By default, the underlying implementation of a full-text schema index is Lucene.** You can change the underlying index provider of any index.

##### Example: Creating a full-text schema index

Here is an example where we create a full-text schema index on data in title property of Movie nodes and data in the name property of Person nodes:

```
CALL db.index.fulltext.createNodeIndex(
      'MovieTitlePersonName',['Movie', 'Person'], ['title', 'name'])
```

Just as you can create a full-text schema index on properties of nodes, you can create a full-text schema index on properties of relationships. To do this you use `CALL db.indexfulltext.createRelationshipIndex()`.

#### Retrieving configured indexes

```
CALL db.indexes()
```

#### Using a full-text schema index

To use a full-text schema index, you must call the query procedure that uses the index.

```
CALL db.index.fulltext.queryNodes(
     'MovieTitlePersonName', 'Jerry') YIELD node
RETURN node
```

Notice that we specify `YIELD` after calling the procedure. This enables us to use return values from the procedure. In this case, we return all nodes that are found in the graph that have either a title property or name property containing the string, Jerry.

#### Retrieving the score for a full-text search

When a full-text schema index is used, it calculates a “hit score” that represents the closeness of the values in the graph to the query string:

```
CALL db.index.fulltext.queryNodes(
     'MovieTitlePersonName', 'Matrix') YIELD node, score
RETURN node.title, score
```

##### Example: Searching on a particular property

With full-text indexes created, you can also specify which property you want to search for. Here is an example where we are looking for Jerry, but only as a name property of a Person node:

```
CALL db.index.fulltext.queryNodes(
     'MovieTitlePersonName', 'name: Jerry') YIELD node
RETURN node
```

#### Dropping an index

```
DROP INDEX MovieReleasedVideoFormat
```

#### Dropping a full-text schema index

```
CALL db.index.fulltext.drop('MovieTitlePersonName')
```

#### Exercise 14: Creating indexes

```
// Create a single-property index on the born property of a Person node naming the index PersonBornIndex.
CREATE INDEX PersonBornIndex FOR (p:Person) ON (p.born)

// Create a full-text schema index for the tagline property of the Movie nodes named MovieTaglineFTIndex.
CALL db.index.fulltext.createNodeIndex('MovieTaglineFTIndex',['Movie'], ['tagline'])

// Write and execute a query to find all movies with taglines that contain the strings "real" or "world"
CALL db.index.fulltext.queryNodes('MovieTaglineFTIndex', 'real OR world')
      YIELD node
RETURN node.title, node.tagline

// Add score to the YIELD clause and return it to see what score the search produces
CALL db.index.fulltext.queryNodes('MovieTaglineFTIndex', 'real OR world')
      YIELD node, score
RETURN node.title, score, node.tagline

// Drop the full-text schema index you just created.
CALL db.index.fulltext.drop('MovieTaglineFTIndex')
```

#### Check your understanding

What is the difference between a node key and a composite index?
A composite index does not enforce uniqueness.

### Using Query Best Practices

#### Using Cypher parameters

In your Cypher statements, a parameter name begins with the `$` symbol.

Here is an example where we have parameterized the query:

```
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE p.name = $actorName
RETURN m.released, m.title ORDER BY m.released DESC
```

At runtime, if the parameter `$actorName` has a value, it will be used in the Cypher statement when it runs in the graph engine.

#### Setting a parameter

You can set values for Cypher parameters that will be in effect during your session.

You can set the value of a single parameter in the query editor pane as shown in this example where the value Tom Hanks is set for the parameter `actorName`:

```
:param actorName => 'Tom Hanks'
```

You can even specify a Cypher expression to the right of `=>` to set the value of the parameter.

#### Using the parameter

After the actorName parameter is set, you can run the query that uses the parameter.

Subsequently, you need only change the value of the parameter and not the Cypher statement to test with different values.

#### Setting multiple parameters

You can also use the JSON-style syntax to set all of the parameters in your Neo4j Browser session. The values you can specify in this object are numbers, strings, and booleans. In this example we set two parameters for our session:

```
:params {actorName: 'Tom Cruise', movieName: 'Top Gun'}
```

#### Using multiple parameters

Here is a different query that uses both of these parameters:

```
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE p.name = $actorName AND m.title = $movieName
RETURN p, m
```

If you want to remove an existing parameter from your session, you do so by using the JSON-style syntax and exclude the parameter for your session.

If you want to clear all parameters, you can simply type:

```
:params {}
```

#### Viewing parameters

If you want to view the current parameters and their values, simply type `:params`

#### Analyzing queries

There are two Cypher keywords you can prefix a Cypher statement with to analyze a query:

- `EXPLAIN` provides estimates of the graph engine processing that will occur, but does not execute the Cypher statement.
- `PROFILE` provides real profiling information for what has occurred in the graph engine during the query and executes the Cypher statement.

#### Using EXPLAIN

The `EXPLAIN` keyword provides the Cypher query plan. A Cypher query plan has operations where rows are processed and passed on to the the next operation (step). You can compare different Cypher statements to understand the stages of processing that will occur when the Cypher executes.

##### Example: Using EXPLAIN

```
// Set the parameters
:params {actorName: 'Hugo Weaving', year: 2000}

// With EXPLAIN, the query does not run, the graph engine simply produces the query plan.
EXPLAIN MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE p.name = $actorName AND
      m.released <  $year
RETURN p.name, m.title, m.released
```

##### Example: Using PROFILE

Here we see that for each phase of the graph engine processing, we can view the cache hits and most importantly the number of times the graph engine accessed the database (db hits). This is an important metric that will affect the performance of the Cypher statement at run-time. The overall execution milliseconds, however is the measurement that you typically use for query tuning. The elapsed milliseconds is affected, not only by your query, but also whether the caches are populated.

```
// Set the parameters
:params {actorName: 'Hugo Weaving', year: 2000}

// With PROFILE, the query runs and we can see cache hits and database hits
PROFILE MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE p.name = $actorName AND
      m.released <  $year
RETURN p.name, m.title, m.released
```

#### Monitoring queries

If you are testing an application and have run several queries against the graph, there may be times when your session hangs with what seems to be a very long-running query.

There are two reasons why a Cypher query may take a long time:

- The query returns a lot of data. The query completes execution in the graph engine, but it takes a long time to create the result stream.

      Example A: MATCH (a)--(b)--(c)--(d)--(e)--(f)--(g) RETURN a

- The query takes a long time to execute in the graph engine.

      Example B: MATCH (a), (b), (c), (d), (e) RETURN count(id(a))

##### Example: Monitoring queries

You might want to understand whether the query is taking a long time or whether the query has completed, but it is returning a lot of results.

You can monitor it by using the `:queries` command. Here is a screenshot where we are monitoring a long-running (Example A) query in another Neo4j Browser session:

The `:queries` command calls `dbms.listQueries()` under the hood, which is why we see two queries here.

#### Cypher query best practices

Here is a very high-level list of some of the best practices you should aim for in your Cypher queries:

- Create and use indexes effectively.
- Use parameters rather than literals in your queries.
- Specify node labels in `MATCH` clauses.
- Reduce the number of rows passed processed.
- Aggregate early in the query, rather than in the `RETURN` clause, if possible.
- Use `DISTINCT` and `LIMIT` early in the query to reduce the number of rows processed.
- Defer property access until you really need it.

Many of these guidelines are covered in the course, Advanced Cypher.

#### Exercise 15: Using query best practices

```
// Add a parameter named year to your session with a value of 2000.
:param year => 2000

// Add a parameter named ratingValue to your session with a value of 65.
:params {year: 2006, ratingValue: 65}
```

#### Check your understanding

Suppose you are executing queries in Neo4j Browser Session A and monitoring them in Neo4j Browser Session B with the :queries command. What are some ways that you can kill a query?
Select the correct answers.

[X] You can close the result pane in Session A, if the query can be seen in Session B.
[] You can close the result pane in Session A, if the query can no longer be seen in Session B.
[X] You can kill any running query seen in Session B.
[X] You can close the Neo4j Browser that is running Session A.

### Overview of Importing Data into Neo4j

#### Overview

You have many options for importing data into Neo4j. Which option you choose depends on:

- How much data you have.
- What tools you are comfortable using.
- How much time you have to perform the import.

#### Options for importing data into Neo4j

![https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/ImportOptions.png](https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/ImportOptions.png)

#### Prepare for the import

Before you import data into the graph, you should have an idea of the target graph data model you want to achieve. You should work with the data architects for your application so that everybody agrees upon:

- Names of entities (node labels).
- Names of relationships.
- Names of properties for nodes and relationships.
- Constraints to be defined.
- Indexes required.
- The most important queries?

These graph data model components should be agreed upon before you import the data. You learn how to model your data in the course Graph Data Modeling for Neo4j

#### Using Cypher’s LOAD CSV for importing data

![https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/LOADCSVWorkflow.png](https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/LOADCSVWorkflow.png)

Requirements for using LOAD CSV:

- CSV files that have been extracted from an RDBMS.
- Neo4j Browser or Cypher-shell.
- Neo4j database running locally, in Neo4j Aura, in Neo4j Sandbox.
- Optionally using a Neo4j Cluster.
- Special handling if > 100K lines of data.

#### Using APOC and Cypher for importing data

The APOC library is used by virtually all developers to reduce their programming effort. You can use many APOC procedures and functions for importing data into the graph. One thing that APOC handles very nicely is iteration and batching that may be required for importing complex and large amounts of data. In addition to CSV, APOC procedures can be used to read other formats such as XML, GraphML, and JSON.

Requirements for using APOC

- CSV, XML, or JSON files that have been extracted from an RDBMS.
- Neo4j Browser or Cypher-shell.
- Neo4j database running locally, in Neo4j Aura, in Neo4j Sandbox.
- Optionally using a Neo4j Cluster.
- No limit to size of data to import.

You can also use APOC’s jdbc connection features to access a live RDBMS. This type of connection is not covered in this course.

#### Using Drivers via Bolt

If you already have an application that accesses your RDMBS that is written in Java, Javascript, Python, C# or Go, you can use Neo4j’s supported drivers to add nodes and relationships to the graph. These languages enable you to write code that is transactional, supports batching, and even supports parallel operations. If you are comfortable with these languages, then you can use them to import the data into your graph.

![https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/DriverWorkflow.png](https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/DriverWorkflow.png)

Requirement for using Drivers via Bolt

- Neo4j Browser or Cypher-shell.
- Neo4j database running locally, in Neo4j Aura, in Neo4j Sandbox.
- Optionally using a Neo4j Cluster.
- Live RDBMS Server.
- Application responsible for transaction scoping.
- No limit to size of data to import.

#### Using the neo4j-admin import tool

Another way that you can import data into a Neo4j Database is using the import tool that is part of the `neo4j-admin` tool. With this type of import, the source data is in CSV format. The benefit of importing with this tool is that it is much faster than doing it with Cypher, APOC, or via a driver. The database is created as part of the import and it is done “offline”. That is, the database can only be started after the import has completed. If your database will be part of a Cluster, then you will need to have each Cluster member “catch up” to the database that was newly imported.

Using the admin import tool is a very common way of performing an initial load of data into a graph. Then as the application evolves, the graph data model might change.

![https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/adminToolWorkflow.png](https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/adminToolWorkflow.png)

Requirements for using the neo4j-admin import tool

- CSV files that have been extracted from an RDBMS.
- Neo4j database running locally.
- No limit to size of data to import.

#### Using the ETL tool to import data

Neo4j Labs has created a tool (graph application) that you can use to import data using a live connection to an RDBMS. With this tool, you map tables/fields to nodes/properties to perform the import.

Using the ETL tool, although convenient, may not be suitable for all types of loading, especially if you want to implement a complex graph data model. That is, the ETL tool is a “generic” load tool.

![https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/ETLTool.png](https://s3.amazonaws.com/dev.assets.neo4j.com/course/4.0-intro-neo4j/images/ETLTool.png)

Requirements for using the ETL Tool

- Neo4j Desktop with ETL tool installed.
- Neo4j database running locally, in Neo4j Aura, in Neo4j Sandbox.
- Optionally using a Neo4j Cluster.
- Live RDBMS Server.
- No limit to size of data to import.

#### Check your understanding

Suppose you want to import data into a Neo4j Database from files that were created from an RDBMS. What format is required to load the data using existing Neo4j tooling?
Select the correct answers.

[] RDF
[X] JSON
[X] XML
[X] CSV

### Using LOAD CSV for Import

At the end of this module, you should be able to:

- Describe the steps for importing data with Cypher.
- Prepare the graph and data for import:

  Inspect data.

  Determine if data needs to be transformed.

  Determine the size of the data that will be imported.

  Create the Constraints in the graph.

- Import the data with LOAD CSV.
- Create indexes for newly-loaded data.

#### Loading data with Cypher

In Cypher, you can:

- Load data from a URL (http(s) or file).
- Process data as a stream of records.
- Create or update the graph with the data being loaded.
- Use transactions during the load.
- Transform and convert values from the load stream.
- Load up to 10M nodes and relationships.

#### CSV file structure

Including headers in the CSV file reduces syncing issues. If the size of the CSV files is extremely large, it is sometimes better to separate the headers from the data, especially if multiple files will be split to use the same set of headers.

#### Example: Inspect the data at a URL

Before you load the data into your graph, you use Cypher to inspect the data.

With LOAD CSV, you can access CSV data at a URL or stored locally.

Here is an example where we can view the first 10 lines of the file at the URL where the headers are included in the CSV file and the default delimiter is the comma character:

```
LOAD CSV WITH HEADERS
FROM 'https://data.neo4j.com/v4.0-intro-neo4j/people.csv'
AS line
RETURN line LIMIT 10
```

#### Example: Inspect the data stored locally

You can only load local data into a graph with LOAD CSV if the file has been placed in the import folder for the database:

- Can do this if using Neo4j Desktop which runs a local database.
- Cannot do this for a cloud-based instance such as a Neo4j Sandbox or Neo4j Aura.

To determine where the import folder is for a local database in Neo4j Desktop, you simply go to the Manage pane for the database and then select Open Folder→Import.

Here is an example where we can view the first 10 lines of the local file that has been placed in the import folder for the database:

```
LOAD CSV WITH HEADERS
FROM 'file:///people.csv'
AS line
RETURN line LIMIT 10
```

#### Determine if data needs transformation

When you inspect a subset of the data, you should be able to determine what transformations will be required. As you have seen, data is by default interpreted as a string or null. If you want numeric data, then you must transform it with functions such as:

- `toInteger()`
- `toFloat()`

#### Preview the data transformation

You can preview the transformations you will make by returning their values:

```
LOAD CSV WITH HEADERS
FROM 'file:///movies1.csv'
AS line
RETURN toFloat(line.avgVote), line.genres, toInteger(line.movieId),
       line.title, toInteger(line.releaseYear) LIMIT 10
```

#### Transforming lists

In additions, lists in a field may need to be transformed to usable lists in Cypher. As you can see in the data, the genres field contains data separated by a colon (:). In fact, the genres field is a string and we want to turn it into a Cypher list of string values. To do this, we use the `split()` and `coalesce()` functions as shown here:

```
LOAD CSV WITH HEADERS
FROM 'file:///movies1.csv'
AS line
RETURN toFloat(line.avgVote), split(coalesce(line.genres,""), ":"),
       toInteger(line.movieId), line.title, toInteger(line.releaseYear)
       LIMIT 10
```

If all fields have data, then `split()` alone will work. If, however, some fields may have no values and you want an empty list created for the property, then you should use `split()` together with `coalesce()`.

#### Guided Exercise: Creating a new database

Thus far in this course, you have been working with a single database, referred to as the neo4j-default database. Assuming that you want to continue using this database as you explore the Movie graph, you will create a new database that will be served by the same database server. Note that hosting multiple databases in a single database server is new in release 4.0 of Neo4j.

Here are the steps to create a new database, `Movies`:

- In Neo4j Browser, select the system database.
- Create the new movies database in the query edit pane with `CREATE DATABASE Movies`
- Enter the browser command `:dbs` in the query edit pane to see the list of existing databases.
- Enter the browser command `:use movies` to switch to this newly created, empty database.

#### Create constraints before loading the data

Here is the code for creating the constraints in the graph where we will import data to Movie and Person nodes where the id property will be unique. Note that the id property is different from the internal id of a node that is created automatically by the graph engine.

```
CREATE CONSTRAINT UniqueMovieIdConstraint ON (m:Movie) ASSERT m.id IS UNIQUE;
CREATE CONSTRAINT UniquePersonIdConstraint ON (p:Person) ASSERT p.id IS UNIQUE;
```

If your load process uses `MERGE`, rather than `CREATE` to create nodes, the load will be VERY slow if constraints are not defined first because `MERGE` needs to determine if the node already exists. The uniqueness constraint is itself an index which makes a lookup fast.

Indexes, however, will slow down the creation of data due to added writes, but are necessary if you want transactionally consistent data and indexes in the database. You should create additional indexes in the graph after the data is loaded.

#### Determine the size of the data to be loaded

It is important for you to understand how much data will be loaded. By default LOAD CSV can handle the loading of up to 100K lines/rows.

You can query the size of your CSV files as follows:

```
LOAD CSV WITH HEADERS
FROM 'file:///people.csv'
AS line
RETURN count(line)
```

If the CSV file has fewer that 100K rows, it can easily be loaded with `LOAD CSV`.

#### Loading a large CSV file

If the number of rows exceeds 100K, then you have two options.

The first option is to use `:auto USING PERIODIC COMMIT LOAD CSV`. Placing `:auto USING PERIODIC COMMIT` enables the load, by default, to commit its transactions every 1000 rows which will enable the entire import of a large file to succeed. However, there are certain types of Cypher constructs that will cause `:auto USING PERIODIC COMMIT` to be ignored. Cypher statements that use eager operators will prevent you from using `:auto USING PERIODIC COMMIT`. Some examples of these eager operators include:

- `collect()`
- `count()`
- `ORDER BY`
- `DISTINCT`

If you cannot use `:auto USING PERIODIC COMMIT` because your Cypher include some eager operators, then you can use APOC to import the data, which you will learn about in the the next lesson.

#### Importing nodes

In this example, we import the movie data:

```
:auto USING PERIODIC COMMIT 500
LOAD CSV WITH HEADERS FROM
  'https://data.neo4j.com/v4.0-intro-neo4j/movies1.csv' as row
MERGE (m:Movie {id:toInteger(row.movieId)})
    ON CREATE SET
          m.title = row.title,
          m.avgVote = toFloat(row.avgVote),
          m.releaseYear = toInteger(row.releaseYear),
          m.genres = split(row.genres,":")
```

With this code, each line is read as row. Then we use the row field names (from the header row) to assign values to a new Movie node. We use built-in functions to transform the string data in the row into values that are assigned to the properties of the Movie node. MERGE is the best choice because we have our uniqueness constraint defined for the id property of the Movie node. We use split() to set the value for the genres property which will be a list.

#### Importing relationships

Then you load data that will create the relationships between the Movie and Person nodes.

Both the directors.csv and roles.csv files contain information about how Movie data is related to Person data.

In this example, we import the data to create the relationships between existing Movie and Person nodes:

```
LOAD CSV WITH HEADERS FROM
'https://data.neo4j.com/v4.0-intro-neo4j/directors.csv' AS row
MATCH (movie:Movie {id:toInteger(row.movieId)})
MATCH (person:Person {id: toInteger(row.personId)})
MERGE (person)-[:DIRECTED]->(movie)
ON CREATE SET person:Director
```

From each row that is read, we find the Movie node and the Person node. Then we create the :DIRECTED relationship between them. An finally, we add the Director label to the node.

#### Add indexes

The final step after all nodes and relationships have been created in the graph is to create additional indexes. These indexes are based upon the most important queries for the graph.

So for example:

```
// Do this only after ALL data has been imported
CREATE INDEX MovieTitleIndex ON (m:Movie) FOR (m.title);
CREATE INDEX PersonNameIndex ON (p:Person) FOR (p.name)
```

#### Exercise 16: Using LOAD CSV for import

```
// Create a new Neo4j database
:use system;
CREATE DATABASE importcsv;
:use importcsv;

// You are given the name of a file, http://data.neo4j.com/v4.0-intro-neo4j/actors.csv that you must load into your graph. Write the Cypher statement to read the actor data from this file.
LOAD CSV WITH HEADERS
FROM 'http://data.neo4j.com/v4.0-intro-neo4j/actors.csv'
AS line
RETURN line.id, line.name, line.birthYear

// Read the data and return it, ensuring that the data returned is properly formatted. Hint: You want to use birthYear as a number, not a string.
LOAD CSV WITH HEADERS
FROM 'http://data.neo4j.com/v4.0-intro-neo4j/actors.csv'
AS line
RETURN line.id, line.name, toInteger(trim(line.birthYear))

// Load the data into your graph where you will create Person nodes with the properties: name, born, and actorId
LOAD CSV WITH HEADERS
FROM 'http://data.neo4j.com/v4.0-intro-neo4j/actors.csv'
AS line
MERGE (actor:Person {name: line.name})
  ON CREATE SET actor.born = toInteger(trim(line.birthYear)), actor.actorId = line.id
  ON MATCH SET actor.actorId = line.id

// You are given the name of a file, http://data.neo4j.com/v4.0-intro-neo4j/movies.csv that you must load into your graph. Write the Cypher statement to read the movie data from this file.
LOAD CSV WITH HEADERS
FROM 'http://data.neo4j.com/v4.0-intro-neo4j/movies.csv'
AS line
RETURN line.id, line.title, line.year, line.tagLine

// Read the data and return it, ensuring that the data returned is properly formatted.
LOAD CSV WITH HEADERS
FROM 'http://data.neo4j.com/v4.0-intro-neo4j/movies.csv'
AS line
RETURN line.id, line.title, toInteger(line.year), trim(line.tagLine)

// Load the data into your graph where you will create Movie nodes with the properties: title, released, tagline, and movieId
LOAD CSV WITH HEADERS
FROM 'http://data.neo4j.com/v4.0-intro-neo4j/movies.csv'
AS line
MERGE (m:Movie {title: line.title})
ON CREATE
  SET m.released = toInteger(trim(line.year)),
      m.movieId = line.id,
      m.tagline = line.tagLine

// You are given the name of a file, http://data.neo4j.com/v4.0-intro-neo4j/actor-roles.csv that you must load into your graph. Write the Cypher statement to read the relationship data from this file.
LOAD CSV WITH HEADERS
FROM 'http://data.neo4j.com/v4.0-intro-neo4j/actor-roles.csv'
AS line FIELDTERMINATOR ';'
RETURN line.personId, line.movieId, line.Role

// Read the data and return it, ensuring that the data returned is properly formatted.
LOAD CSV WITH HEADERS
FROM 'http://data.neo4j.com/v4.0-intro-neo4j/actor-roles.csv'
AS line FIELDTERMINATOR ';'
RETURN line.personId, line.movieId, split(line.Role,',')

// Load the relationship data into your graph. Hint: You will need to use the properties actorId and movieId to find the nodes in the graph.
LOAD CSV WITH HEADERS
FROM 'http://data.neo4j.com/v4.0-intro-neo4j/actor-roles.csv'
AS line FIELDTERMINATOR ';'
MATCH (movie:Movie { movieId: line.movieId })
MATCH (person:Person { actorId: line.personId })
MERGE (person)-[:ACTED_IN { roles: split(line.Role,',')}]->(movie)
```

### Using APOC for Import

#### Installing APOC for use with your graph(s)

If you are using a Neo4j Sandbox or Neo4j Aura, your database already has access to the APOC library. If you are using Neo4j Desktop, you must add the APOC library to your database server you are using.

Here are the steps for adding the APOC library to your database server in Neo4j Desktop:

1. Stop the database server.
2. For the project you are working with, select “+” in the Add Plugin panel.
3. Select APOC to install.
4. Close the install panel.
5. Start the database server.

#### Confirming APOC is available

You can confirm in Neo4j Browser that you have APOC available by executing this code:

```
CALL dbms.procedures()
YIELD name WHERE name STARTS WITH "apoc"
RETURN name
```

#### Using APOC to clear the graph

When you are developing code to import data, you may have several attempts to perform the import correctly. Rather than creating a new database for each attempt, you can completely clear the database of all constraints, indexes, nodes, and relationships.

Here is one way that you can clear the database:

```
// Delete all constraints and indexes
CALL apoc.schema.assert({},{},true);

// Delete all nodes and relationships
CALL apoc.periodic.iterate(
  'MATCH (n) RETURN n',
  'DETACH DELETE n',
  { batchSize:500 }
)
```

In Neo4j 4.0, another way that you can clear the graph in Neo4j Browser is to use the system database and then type `CREATE OR REPLACE DATABASE <database-name>`.

#### Using APOC during import

One benefit of using APOC for loading data into the graph is that it can sometimes be faster than `LOAD CSV`. In addition, APOC has some procedures that are helpful during the load, one of which is to control conditional processing. And as you have already learned, with APOC, you can load large datasets that will fail if using `LOAD CSV` or even `:auto USING PERIODIC COMMIT LOAD CSV`.

Just as you inspect the data, determine if data needs to be transformed, and create uniqueness constraints before the import with `LOAD CSV`, you must do the same when using APOC for the import.

#### Using APOC for conditional processing

In the previous lesson, we used LOAD CSV to load Movie and Person data into the graph and then use the additional CSV files to create the relationships between the nodes. Those files represented normalized data where each file basically represents a relational table.

If you want to load denormalized data from a CSV file, you face a couple of challenges.

To load this data into the graph you could:

```
// Make a pass through the file to load the Movie nodes.
// Make a pass through the file to load the Person nodes.
// Make a pass through the file to create relationships based upon the personType field.
```

##### Better alternative for conditional processing with APOC

If the CSV files are large, making multiple passes might not be ideal if you have load time constraints. A better option might be to:

1. Make a pass through the file to load the Movie nodes, collect the person data and then add the Person nodes from the collection.
2. Use the person data to create relationships based upon the personType field.

##### Import nodes, then create relationships

Assuming that we will use the second option for importing the data and we have created the uniqueness constraints as before, here is the Cypher code to create the Person and Movie nodes:

```
CREATE CONSTRAINT UniqueMovieIdConstraint ON (m:Movie) ASSERT m.id IS UNIQUE;
CREATE CONSTRAINT UniquePersonIdConstraint ON (p:Person) ASSERT p.id IS UNIQUE;

// import the people and movie data (partial; no relationships)
LOAD CSV WITH HEADERS FROM
     'https://data.neo4j.com/v4.0-intro-neo4j/movies2.csv' AS row
WITH row.movieId as movieId, row.title AS title, row.genres AS genres,
     toInteger(row.releaseYear) AS releaseYear, toFloat(row.avgVote) AS avgVote,
     collect({id: row.personId, name:row.name, born: toInteger(row.birthYear),
        died: toInteger(row.deathYear),personType: row.personType,
        roles: split(coalesce(row.characters,""),':')}) AS personData
MERGE (m:Movie {id:movieId})
   ON CREATE SET m.title=title, m.avgVote=avgVote,
      m.releaseYear=releaseYear, m.genres=split(genres,":")
WITH *
UNWIND personData AS person
MERGE (p:Person {id: person.id})
   ON CREATE SET p.name = person.name, p.born = person.born, p.died = person.died
```

This code reads the data from a row and creates the personData collection that holds the data for a person. It creates the Movie nodes based upon the row data. With the WITH \* clause, all variables are carried forward in the query. Then the personData collection is unwound so that each element in a row can be used to create the Person nodes. Everything is in the graph, except for the relationships.

This is not quite what we want because we have not created the relationships. That is, the type of relationship created depends on the value of the personType field in each row of the CSV file. This is where APOC can help you. APOC has a procedure that will allow you to perform conditional execution, based upon a value.

#### Using APOC for conditional processing

Here is the complete code that utilizes the apoc.do.when() procedure, assuming that we have cleared the data from the graph first, but the constraints are still defined:

```
LOAD CSV WITH HEADERS FROM
     'https://data.neo4j.com/v4.0-intro-neo4j/movies2.csv' AS row
WITH row.movieId as movieId, row.title AS title, row.genres AS genres,
     toInteger(row.releaseYear) AS releaseYear, toFloat(row.avgVote) AS avgVote,
     collect({id: row.personId, name:row.name, born: toInteger(row.birthYear),
            died: toInteger(row.deathYear),personType: row.personType,
            roles: split(coalesce(row.characters,""),':')}) AS people
MERGE (m:Movie {id:movieId})
   ON CREATE SET m.title=title, m.avgVote=avgVote,
      m.releaseYear=releaseYear, m.genres=split(genres,":")
WITH *
UNWIND people AS person
MERGE (p:Person {id: person.id})
   ON CREATE SET p.name = person.name, p.born = person.born, p.died = person.died
// continue processing and use the personType to create the relationships
WITH  m, person, p
CALL apoc.do.when(person.personType = 'ACTOR',
     "MERGE (p)-[:ACTED_IN {roles: person.roles}]->(m)
                ON CREATE SET p:Actor",
     "MERGE (p)-[:DIRECTED]->(m)
         ON CREATE SET p:Director",
     {m:m, p:p, person:person}) YIELD value
RETURN count(*)  // cannot end query with this type of APOC call
```

After the Movie and Person nodes are created, we use the reference to them to create the relationships between them. The first argument to apoc.do.when() is the data that is tested. The second argument is the Cypher code to execute if the test returns true. The third argument is the Cypher code to execute if the test returns false. The last argument is the object that describes the mapping of variables both outside of the call and inside the call. For simplicity, we specify the same values. Certain apoc calls cannot end a Cypher query so we place a RETURN count(\*) at the end.

#### Using APOC to import from CSV

If you cannot load the CSV file with LOAD CSV or :auto USING PERIODIC COMMIT LOAD CSV, another option is to use APOC for the import. Previously, you learned how to clear the data from the graph using apoc.periodic.iterate(). You use this procedure to load large datasets.

```
CALL apoc.periodic.iterate(
"CALL apoc.load.csv('https://data.neo4j.com/v4.0-intro-neo4j/movies2.csv' )
 YIELD map AS row RETURN row",
 "WITH row.movieId as movieId, row.title AS title, row.genres AS genres,
      toInteger(row.releaseYear) AS releaseYear, toFloat(row.avgVote) AS avgVote,
      collect({id: row.personId, name:row.name, born: toInteger(row.birthYear),
      died: toInteger(row.deathYear),personType: row.personType,
      roles: split(coalesce(row.characters,''),':')}) AS people
 MERGE (m:Movie {id:movieId})
    ON CREATE SET m.title=title, m.avgVote=avgVote,
       m.releaseYear=releaseYear, m.genres=split(genres,':')
 WITH *
 UNWIND people AS person
 MERGE (p:Person {id: person.id})
   ON CREATE SET p.name = person.name, p.born = person.born, p.died = person.died
 WITH  m, person, p
 CALL apoc.do.when(person.personType = 'ACTOR',
      'MERGE (p)-[:ACTED_IN {roles: person.roles}]->(m)
                 ON CREATE SET p:Actor',
      'MERGE (p)-[:DIRECTED]->(m)
          ON CREATE SET p:Director',
      {m:m, p:p, person:person}) YIELD value AS value
       RETURN count(*)  ",
{batchSize: 500}
)
```

The first argument to apoc.periodic.iterate() is the call to apoc.load.csv() where we provide the file name and it returns a row. The second argument is the same Cypher code you saw earlier. The only thing that is different is that you must ensure that the code is in double quotes and the Cypher code does not use double-quotes (or visa versa). The final argument is the size of the batch, 500.

#### Using APOC to load JSON

In this example we call apoc.load.json to return 10 questions from StackOverflow so we can view them:

```
WITH "https://api.stackexchange.com/2.2/search?page=1&pagesize=5&order=asc&sort=creation&tagged=neo4j&site=stackoverflow&filter=!5-i6Zw8Y)4W7vpy91PMYsKM-k9yzEsSC1_Uxlf" AS uri
CALL apoc.load.json(uri)
YIELD value AS data
UNWIND data.items as q
RETURN q
```

##### Viewing keys in the JSON data

To help you understand the types of data available for each question, you can return the keys for each row:

```
WITH "https://api.stackexchange.com/2.2/search?page=1&pagesize=5&order=asc&sort=creation&tagged=neo4j&site=stackoverflow&filter=!5-i6Zw8Y)4W7vpy91PMYsKM-k9yzEsSC1_Uxlf" AS uri
CALL apoc.load.json(uri)
YIELD value AS data
UNWIND data.items as q
RETURN keys(q)
```

##### Viewing data that will be loaded

Next, you must determine what data from the JSON file you will use to create the graph.

Here we have made a selection for the data we want to create in the graph and we write the code to return it:

```
WITH "https://api.stackexchange.com/2.2/search?page=1&pagesize=5&order=asc&sort=creation&tagged=neo4j&site=stackoverflow&filter=!5-i6Zw8Y)4W7vpy91PMYsKM-k9yzEsSC1_Uxlf" AS uri
CALL apoc.load.json(uri)
YIELD value AS data
UNWIND data.items as q
RETURN q.question_id, q.title, q.tags, q.is_answered, q.owner.display_name
```

##### Creating the graph from JSON

We will use all values, except owner.display_name to create a Question node. We will use owner.display_name to create the User nodes. Here is the code to create the graph:

```
WITH "https://api.stackexchange.com/2.2/search?page=1&pagesize=5&order=asc&sort=creation&tagged=neo4j&site=stackoverflow&filter=!5-i6Zw8Y)4W7vpy91PMYsKM-k9yzEsSC1_Uxlf" AS uri
CALL apoc.load.json(uri)
YIELD value AS data
UNWIND data.items as q
MERGE (question:Question {id: q.question_id})
  ON CREATE SET  question.title = q.title,
                 question.tags = q.tags,
                 question.is_answered = q.is_answered
MERGE (user:User {name: q.owner.display_name})
MERGE (user)-[:ANSWERED]->(question)
```

If you were to load thousands or more questions, you should ensure that you have created uniqueness constraints on Question.question_id and User.name before you attempt the load.

#### Exercise 17: Using APOC for importing data

```
// You will start with a new database named importwithapoc. Execute these commands in Neo4j Browser to create the database.
:USE system;
CREATE DATABASE importwithapoc;
:USE importwithapoc;

// Verify that APOC has been installed
CALL dbms.procedures()
YIELD name WHERE name STARTS WITH "apoc"
RETURN name

// The data for the load comes from this file where we have denormalized data - https://data.neo4j.com/v4.0-intro-neo4j/movies2.csv
// Write and execute Cypher code to view the first 100 rows of this file.
LOAD CSV WITH HEADERS FROM
     'https://data.neo4j.com/v4.0-intro-neo4j/movies2.csv' AS row
RETURN row LIMIT 100

// Create uniqueness constraints for the id property for a Person node and the id property for a Movie node.
CREATE CONSTRAINT UniqueMovieIdConstraint ON (m:Movie) ASSERT m.id IS UNIQUE;
CREATE CONSTRAINT UniquePersonIdConstraint ON (p:Person) ASSERT p.id IS UNIQUE;

// Since APOC is new to you, examine this code and simply execute this code to load the data
CALL apoc.periodic.iterate(
"CALL apoc.load.csv('https://data.neo4j.com/v4.0-intro-neo4j/movies2.csv' ) YIELD map AS row RETURN row",
"WITH row.movieId as movieId, row.title AS title, row.genres AS genres, toInteger(row.releaseYear) AS releaseYear, toFloat(row.avgVote) AS avgVote,
 collect({id: row.personId, name:row.name, born: toInteger(row.birthYear), died: toInteger(row.deathYear),personType: row.personType, roles: split(coalesce(row.characters,''),':')}) AS people
 MERGE (m:Movie {id:movieId})
    ON CREATE SET m.title=title, m.avgVote=avgVote,
       m.releaseYear=releaseYear, m.genres=split(genres,':')
 WITH *
 UNWIND people AS person
 MERGE (p:Person {id: person.id})
    ON CREATE SET p.name = person.name, p.born = person.born, p.died = person.died
 WITH  m, person, p
 CALL apoc.do.when(person.personType = 'ACTOR',
      'MERGE (p)-[:ACTED_IN {roles: person.roles}]->(m)
                 ON CREATE SET p:Actor',
      'MERGE (p)-[:DIRECTED]->(m)
          ON CREATE SET p:Director',
      {m:m, p:p, person:person}) YIELD value AS value
       RETURN count(*)  ",
{batchSize: 500}
)

// Call the APOC procedures to clear everything from the graph, including constraints and indexes.
// Delete all constraints and indexes
CALL apoc.schema.assert({},{},true);
// Delete all nodes and relationships
CALL apoc.periodic.iterate(
  'MATCH (n) RETURN n',
  'DETACH DELETE n',
  { batchSize:500 }
)
```

#### Check your understanding

Question 1 - What APOC procedure can you use to batch transactions when a lot of data needs to be processed? `apoc.periodic.iterate()`

Question 2 - The procedure `apoc.do.when()` is used for:
Select the correct answers.

[] Scheduling when a load should occur.
[X] Executing Cypher code when a condition is true and alternate Cypher code when the condition is false.
[] An alternative to the MERGE clause.
[X] Understanding how many operations occurred during the execution.

Question 3 - What does `CALL apoc.schema.assert({},{},true)` do?
Select the correct answers.

[X] Drops all constraints in the graph.
[X] Drops all indexes in the graph.
[] Removes all relationships in the graph.
[] Removes all nodes in the graph.

### Using the neo4j-admin tool for Import

#### What is neo4j-admin?

A Neo4j installation includes a bin folder that contains some useful utilities that can be used by both developers and administrators. These include:

- `neo4j`, used for starting, stopping, and checking the status of the Neo4j instance.
- `cypher-shell`, a command-line version of a Cypher interface that enables you to execute Cypher statements.
- `admin-tool`, used for a number of administrative activities including creating a database from CSV files.

In this lesson, you will learn how to use the import feature of `neo4j-admin`. You should use `neo4j-admin` for import when you have extremely large amounts of data to import, for example greater than 10M nodes.

#### Preparing for import

Unlike using Cypher to import the data, you don’t have the flexibility to transform the data during the import. You must ensure that the data in the CSV files is clean and ready for import.

When using neo4j-admin for import, there are differences from importing with Cypher:

- The database to be imported into must not exist as it will be created as part of the import.
- The header information has additional information used for creating the nodes and relationships.
- Node CSV files are structured differently from relationship CSV files.
- All CSV files must use the same field separator.
- You create the constraints (and indexes) after the import.

#### CSV files

The format of the CSV files is important:

- For both nodes and relationships, header information must be associated with the data.
- Header information contains an ID to uniquely identify the record, optional node labels or relationship types, and names for the properties representing the imported data.
- A CSV can have a header row, or you can place the header information in a separate file.

#### Exercise 18: Importing data with the import command of neo4j-admin

```
// Download the archive containing the CSV files from http://data.neo4j.com/v4.0-intro-neo4j/crimes-dataset.zip
// In Neo4j Desktop for the project’s database you are using, select Manage > Open Folder
//    In my case, this folder is at ~/Library/Application Support/com.Neo4j.Relate/Data/dbmss/dbms-cb0fe6af-6f68-422b-8af5-8daf9069a43c/import
//    Copy the CSV files to this import folder directly
//    In a text editor of your choice, examine the CSV files. Make sure you understand the headers and how they will be used for the import.

// Open a terminal window for the Neo4j instance
// In Neo4j Desktop for the project you are using, do the following:
//    For the project’s database that should be running, select Manage > Open Terminal.
//    Navigate to the import folder.

// In the terminal you just opened, run the following command:
$ ../bin/neo4j-admin import --database crimes --nodes crimes_header.csv,crimes.csv --nodes beats.csv --nodes=PrimaryType=primaryTypes.csv --relationships crimesBeats.csv --relationships=PRIMARY_TYPE=crimesPrimaryTypes.csv --trim-strings=true > import.out

// Did the import work as expected?
$ tail import.out
.......... .......... .......... .......... ..........  95% ∆440ms
.......... .......... .......... .......... .......... 100% ∆1ms


IMPORT DONE in 16s 832ms.
Imported:
  4193782 nodes
  8386880 relationships
  12553547 properties
Peak memory usage: 864.0MiB

// IMPORTANT: At this point, the database files have been created, but you must use the `system` database to add the crimes data base to your environment.
:use system;
CREATE DATABASE crimes;
:use crimes;
```

### Using an Application for Import

#### Exercise 19: Importing data from an RDBMS using an application

```
// This example allows you to use a custom Java application and Groovy to connect to an existing database and bring data in.
//
// I am not interested in pursuing this 🤓
```

#### Check your understanding

Question 1 - What supported drivers can you use to write a Neo4j application?
Select the correct answers.

[X] Java
[X] JavaScript
[X] Python
[X] Go
[X] C#

Question 2 - What programming languages support asynchronous calls to Neo4j:
Select the correct answers.

[] JavaScript
[X] Java
[] Python
[X] C#

Question 3 - What programming languages support synchronous calls to Neo4j:
Select the correct answers.

[X] JavaScript
[X] Java
[X] Python
[X] C#

### Using the Neo4j ETL Tool for Import

## Resources

Here are some resources you will use as you learn about Neo4j and Cypher for this course:

- [Neo4j Cypher Manual](https://neo4j.com/docs/cypher-manual/4.0/)
- [Neo4j Cypher Refcard](https://neo4j.com/docs/cypher-refcard/current/)
- [APOC Documentation](https://neo4j.com/docs/labs/apoc/current/)
- [Neo4j Drivers Manual](https://neo4j.com/docs/driver-manual/current/)
- [Neo4j Community Site](https://community.neo4j.com/c/general/online-training?_ga=2.145081532.928352283.1602607035-1250704953.1598219608)
