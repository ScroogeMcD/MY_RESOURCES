# MY_RESOURCES
some resources that I have collated from the internet

#### Why is it difficult to create a distributed DBMS ? [Explanation link](https://dba.stackexchange.com/a/34896)
* **ACID** are the key invariants to be enforced for a transaction to be reliably implemented, without causing undesirable side effects.
* **A - Atomicity on distributed systems** : If the data updated by a transaction is spread across multiple nodes, the commit rollback of the nodes must be coordinated. This is less of an issue if the same disk is shared by all the nodes (shared-disk systems), but is a significant overhead on shared-nothing systems
* **C - Consistency on distributed systems** : Ensuring foreign key references across nodes is difficult (for cases where parent and child of a foreign key relationship reside on different nodes).
* **I - Isolation on distributed systems** : 
* **D - Durability** :

Guaranteeing ACID across distributed systems, with shared nothing architecture is very difficult.
Thus if you do not need ACID guarantees, then choosing a No-sql DB will help you grow horizontally with relative ease and cheap off-the-shelf hardware.

Also a lot of RDBMS are typically optimized for read-heavy workloads. And thus might not peform so well with write heavy workloads.

So far the market for cheap but ultra-high volume fully ACID RDBMS platform is dominated by MySQL, which supports sharding and multi-master replication. Sharding allows very high read throughputs, so this type of architecture scales well on read-heavy workloads.


#### Projects to code yourself
1. An embedded key-value store (How do you handle off-heap management)
2. A distributed cache (go through eh-cache code)
3. RAFT consensus algorithm
4. Heap memory manager and garbage collector

#### Must read papers
1. [ Map Reduce : Simplified Data Processing on Large Clusters ](https://static.googleusercontent.com/media/research.google.com/en//archive/mapreduce-osdi04.pdf)
2. [Dynamo : Amazon's key-value store](https://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf)
3. [Dataflow Model](https://research.google/pubs/pub43864/)
4. [MillWheel](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/41378.pdf)
5. [Spanner : Google distributed DB](https://www.usenix.org/system/files/conference/osdi12/osdi12-final-16.pdf)

#### JAVA MEMORY MODEL
1. https://shipilev.net/blog/2016/close-encounters-of-jmm-kind/

#### Interesting blogs :
1. http://vanillajava.blogspot.in/

#### Networks : 
1. Implementing a simple web server : https://users.cs.jmu.edu/bernstdh/web/common/lectures/summary_http-server-example_java.php 

#### Distributed Systems : 
1. Amazon's Dynamo DB paper : https://www.allthingsdistributed.com/2007/10/amazons_dynamo.html

#### Alternatives to Spring-boot :
1. Micronaut

#### Service Mesh

#### Service Discovery
1. Netflix Eureka

#### Distributed applications tracing

#### Kafka Internals

#### SQL Server query optimizer and query plan chooser

#### Datadog Design

#### Circuit Breakers
1. Hystrix

#### Zookeeper and recipes

#### Cache design
1. Redis
2. Hazelcast
  
#### Data collections : Logstash (ELK) vs FluentD(EFK) 

#### API gateways:

<details>
  <summary> Reverse Proxying vs Forward proxying :</summary>
  
  * Proxy means someone or something acting on behalf of someone else.	
  * **Forward proxy**
    * grant the client anonymity (think TOR). [Client knows both the proxy and the server. Server knows only the proxy]
    * A typical usage of forward proxies is grating internet access to internal clients of an organization, which is otherwise blocked by the organization.
  * **Reverse proxy** 
    * grant backend servers anonymity (think servers behind a DMZ) [Client knows only the reverse proxy. Server knows both reverse proxy as well as client]
    * A typical usage of a reverse proxy is to provide Internet users access to a server that is behind a firewall. Reverse proxies can also be used to balance load    among several back-end servers or to provide caching for a slower back-end server. In addition, reverse proxies can be used simply to bring several servers into the same URL space.
  * Reverse-proxy is also known as gateway.
</details>

#### Syslog:
The syslog protocol is used to transport messages from network devices to a logging server, typically known as a syslog server.

#### Maven
There are three main maven plugins that we use while packaging our application into a jar file, and these plugins follow different concepts while packaging our application with dependencies.
* **maven jar plugin** : This plugin provides the capability to build and sign jars. But it just compiles the java files under ```src/main java``` and ```src/test/java```. It doesn't include the dependencies JAR files.
* **maven assembly plugin** : This plugin extracts all dependency jars into raw classes, and group it together. It can also be used to build an executable jar by specifying the main class. It works in projects with few dependencies only. For large projects with many dependencies, it will cause Java class name conflict issue.
* **maven shade plugin** : It packages all dependencies into one uber-jar. It can also be used to build an executable jar by sepcifying the main class. This plugin is particularly useful as it merges contents of specific files, instead of overwriting them - by **Relocating Classes**. This is needed when there are resource files that have the same name across the jars and the plugin tries to package all the resource file. Two main benefits of shadow are :
  * Creating an executable JAR distribution
  * Bundling and relocating common dependencies in libraries to avoid classpath conflicts.


#### Some common tmux commands
* tmux : launches tmux terminal
* ~/.tmux.conf : file where the tmux related configurations are set. This is where we can change C-b to C-a
* ```C-a %``` : splits pane vertically
* ```C-a shift "``` : shifts pane horizontally
* ```C-a arrow keys``` : for navigation
* ```C-a : setw synchronize-panes on``` : propagates command typed in one pane into all others
* ```C-a : setw synchronize-panes off``` : tunrns off propagation to other windows


