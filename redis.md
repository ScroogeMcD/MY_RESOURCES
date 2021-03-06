# Exploring REDIS

## Running redis in container
This assumes that we already have docker installed.

``` docker run --name my-redis-container -p 6379:6379 -d redis ```.   
Here *--name* assigns the name 'my-redis-container' to the container. The name of the image that we are pulling is *redis*, and *-d* runs it in detached mode.
*-p* exposes the container's port 6379 on which redis accepts connections, to the host port 6379. We will use this host port 6379 to connect to redis from our python redis client.

To verify that we actually have the container running     
``` docker ps -a```  
'-a' option shows all the containers (both running and stopped ones)

To open an interactive terminal inside the container  
``` docker exec -it my-redis-container /bin/bash ```

to verify that redis-cli is present in the container,  
``` whereis redis-cli ```

next, type redis-cli to interact with the redis command line interface
``` 
$ redis-cli
$ set key1 val1
$ get key1
```

## redis python client  
if redis python client is not already installed   
``` pip install redis ```

check that the redis client is working  
```py
$ python
$ import redis
$ conn = redis.Redis()
$ conn.set('key1', 'value1')
$ conn.get('key1')

```

In the experimentation set up below, redis server is running in top-left pane, monitor command in redis-cli is running in bottom-left pane, and python redis client is running in right pane.

![Screenshot 2020-09-21 at 9 40 03 PM](https://user-images.githubusercontent.com/13499858/93793439-75153d00-fc54-11ea-9c2f-bb3dfc2a2832.png)

