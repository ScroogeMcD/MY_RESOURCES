# MY_RESOURCES
some resources that I have collated from the internet

#### Projects to code yourself
1. An embedded key-value store (How do you handle off-heap management)
2. A distributed cache

#### Must read papers
1. [ Map Reduce : Simplified Data Processing on Large Clusters ](https://static.googleusercontent.com/media/research.google.com/en//archive/mapreduce-osdi04.pdf)
2. [Dynamo : Amazon's key-value store](https://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf)
3. [Dataflow Model](https://research.google/pubs/pub43864/)
4. [MillWheel](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/41378.pdf)

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
