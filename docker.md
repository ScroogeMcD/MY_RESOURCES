## Docker

### Docker Management Commands are in the form of **'docker \<noun\> \<verb\>'**    
* ```docker --help```
  * This prints 'container' as one of the option under ManagementCommands section  
* ```docker container --help```   
  * This print 'ls' as one of the option   
* ```docker container ls --help```

### Formatting docker output
Some of the docker commands provide an option to format their output using Go templates
* ```docker container ls -a --format '{{json .}}' | jq .```
  * This list downs all the elements supported in the current template in json. jq prints it in a pretty-print format.
* ```docker container ls -a --format 'table {{.ID}}\t{{.Image}}'```
  * this formats output in a table format  
* ```docker container stats --no-stream --format 'table {{.CPUPerc}}\t{{.MemUsage}}\t{{.NetIO}}\t{{.Name}}'```
  * to print the CPU%, MemoryUsage, NetworkIO and Name of containers in tabular format
  
 
### Dockerfile HelloWorld in Java   
STEP 01 : Create a simple HelloWorld.java file in an empty directory
```java
class HelloWorld{
  public static void main(String[] args){
    System.out.println("Hi there from Docker.");
  }
}
```

STEP 02 : Create a file named 'Dockerfile' in the same directory as that of HelloWorld.java created above   
```
FROM openjdk:8-alpine

#settting the work directory to be /usr/src/myapp in the docker container
WORKDIR /usr/src/myapp

# copying HelloWorld.java from host file system to docker file system
COPY HelloWorld.java  /usr/src/myapp/

#compile the program
RUN javac HelloWorld.java

#execute
CMD ["java","HelloWorld"]
```

STEP 03 : from the directory containing the above two files, run the following in command line:   
``` 
docker build -t test/hello-world .
docker container run test/hello-world
```
