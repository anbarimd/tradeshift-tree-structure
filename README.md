# tradeshift-tree-structure

## What is the problem ?

We in Amazing Co need to model how our company is structured so we can do awesome stuff.

We have a root node (only one) and several children nodes,each one with its own children as well. It's a tree-based structure. 

Something like:

         root
        /    \
       a      b
       |
       c

We need two HTTP APIs that will serve the two basic operations:

1) Get all children nodes of a given node (the given node can be anyone in the tree structure).
2) Change the parent node of a given node (the given node can be anyone in the tree structure).
 
They need to answer quickly, even with tons of nodes. Also,we can't afford to lose this information, so some sort of persistence isrequired.

Each node should have the following info:

1) node identification
2) who is the parent node
3) who is the root node
4) the height of the node. In the above example,height(root) = 0 and height(a) == 1.

# My Solution

I have modeled the Co tree structure as a Graph. A Tree is just a restricted form of a Graph.
Trees have direction (parent / child relationships) and don't contain cycles. They fit with in the category of Directed Acyclic Graphs (or a DAG).
So Trees are DAGs with the restriction that a child can only have one parent.

## Why choosing Graph Database ?

One thing that is important to point out :
Graph databases work by storing the relationships along with the data.
Because related nodes are physically linked in the database, accessing those relationships is as immediate as accessing the data itself.
In other words, instead of calculating the relationship as relational databases must do, graph databases simply read the relationship from storage. Satisfying queries is a simple matter of walking, or “traversing,” the graph. 
Graph databases are much faster than relational databases for connected data. A consequence of this is that query latency in a graph database is proportional to how much of the graph you choose to explore in a query, and is not proportional to the amount of data stored
#Why Relational Databases No Longer Measure Up:

Ironically, legacy relational database management systems (RDBMS) are poor at handling relationships between data points. Their tabular data models and rigid schemas make it difficult to add new or different kinds of connections

## Neo4j

According to my resarch and neo4j site :

*  Neo4j delivers the lightning-fast read and write performance you need
*  It is the only enterprise-strength graph database that combines native graph storage, scalable architecture optimized for speed
*  ACID compliance to ensure predictability of relationship-based queries.
*  Index-free adjacency shortens read time and gets even better as data complexity grows. 
*  Using Cypher, the world’s most powerful and productive graph query language.

## The Stack

These are the components of our Web Application:

* Application Type: Spring-Boot Java Web Application with Embedded Tomcat.

* Web framework: Spring-Boot enabled Spring-WebMVC, Using Spring Rest Conntrollers.

* Persistence Access: Spring-Data-Neo4j ( using Neo4j-OGM under the hood ).

* Database: Neo4j-Server 3.5.1

* Build tools and dependency managment : Maven

* Deployment : Using Dockerfile and Docker-compose to deploy the project


## Spring Boot and Spring Data Neo4j

Spring Data Neo4j is core part of the Spring Data project which aims to provide convenient data access for NoSQL databases.
It uses Neo4j-OGM (ike Spring Data JPA uses JPA) under the hood and provides functionality known from the Spring Data world, like repositories, derived finders or auditing.
it offers advanced features to map annotated entity classes to the Neo4j Graph Database.

I have written some code to populate db, this function generate random Binary.

## Build

### Step 1 (Prerequisite) 
Installed: Docker, Docker Compose, Maven 3.x

### Step 2 (Config files) 
### application.properties ( for setting neo4j uri and username and password of neo4j database server )

```
spring.data.neo4j.uri=bolt://neo4j:7687
spring.data.neo4j.username=neo4j
spring.data.neo4j.password=123

```
### docker-compose.yml ( for setting expose ports and link the containers)
```
version: '2.0'

services:
  springboot:
    build: .   
    container_name: springboot
    ports:
      - 8085:8080
    links:
      - neo4j
  neo4j:
    image: neo4j:latest
    container_name: neo4j
    ports:
      - 7474:7474
      - 7687:7687
    
```
### 

### Step 3 (Build the project)

for creating jar file run the following command :
```
mvn clean package -X

```
for Skiping test run and creating jar file run the following command
```
mvn clean package -Dmaven.test.skip=true -X

```

### Step 4 (Deploy and Start the project)
```
docker-compose up --build

```
### Step 5 (check the status of each container)
```
docker ps

```
### result running docker ps:
```
CONTAINER ID        IMAGE                                          PORTS                                                      
bf98f6ab60e8        tradeshift-tree-structure_springboot           0.0.0.0:8085->8080/tcp                                     
38343d4a6bbf        neo4j:latest                           	   0.0.0.0:7474->7474/tcp, 7473/tcp, 0.0.0.0:7687->7687/tcp  

```                  

## Endpoints
We have some GET api for doing actions:





