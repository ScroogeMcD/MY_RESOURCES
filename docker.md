## Docker

### Docker Management Commands are in the form of **'docker \<noun\> \<verb\>'**    
* **docker --help**  
  * This prints 'container' as one of the option under ManagementCommands section  
* **docker container --help**   
  * This print 'ls' as one of the option   
* **docker container ls --help**. 

### Formatting docker output
Some of the docker commands provide an option to format their output using Go templates
* **docker container ls -a --format '{{json .}}' | jq .**
  * This list downs all the elements supported in the current template in json. jq prints it in a pretty-print format.
* **docker container ls -a --format 'table {{.ID}}\t{{.Image}}'**
  * this formats output in a table format  
* **docker container stats --no-stream --format 'table {{.CPUPerc}}\t{{.MemUsage}}\t{{.NetIO}}\t{{.Name}}'**
  * to print the CPU%, MemoryUsage, NetworkIO and Name of containers in tabular format

