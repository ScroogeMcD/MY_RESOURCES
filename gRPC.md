## gRPC - google Remote Procedure Call

### 1. service definitions   
Services are defined in a proto file. For ex. consider the following service definition :   
```proto
syntax = "proto3";

service RouteGuide {
  // unary call
  rpc getFeature (Point) returns (Feature) {}
  
  //server side streaming
  rpc listFeatures (Rectangle) returns (stream Feature) {}
  
  //client side streaming
  rpc RecordRoute (stream Point) returns (RouteSummary) {}
  
  //bi-directional streaming
  rpc RouteChat (stream RouteNote) returns (stream RouteNote) {}
}
```    
### 2. Server side (generated and other classes)
The proto definition above when run through *com.google.protobuf* plugin, generates a class **RouteGuideGrpc.java**. This RouteGuideGrpc class has a static abstract class **RouteGuideImplBase**, which needs to be extended on the server side, to implement the methods declared above in the proto service.

We will need to create a service class, which will override the above four methods :
```java
public class RouteGuideService extends RouteGuideGrpc.RouteGuideImplBase{
  public void getFeature(Point request, StreamObserver<Feature> responseObserver) {...}          // Unary call
  public void listFeatures(Rectangle request, StreamObserver<Feature> responseObserver) {...}    // server streaming
  public StreamObserver<Point> recordRoute(StreamObserver<RouteSummary> responseObserver) {...}  // client streaming
  public StreamObserver<RouteNote> routeChat(StreamObserver<RouteNote> responseObserver) {...}   // bi-di streaming
}
```

We then create and start a gRPC server, which supports the above declared service :
```java
public class RouteGuideServer {
  private final int port;
  private final Server server;
  
  public RouteGuideServer(int port){
    this.port = port;
    ServerBuilder.forPort(port)
                  .addService(new RouteGuideService())
                  .build();
  }
  
  public void start(){
    server.start();
  }
}
```


### 3. generated (and other) classes for client side   
The same generated class **RouteGuideGrpc.java** has three more static final classes :   
* **RouteGuideStub**    
  * async stub
  * can be initialized as `RouteGuideGrpc.newStub(channel);`
* **RouteGuideBlockingStub**    
  * blocking stub
  * can be initialized as `RouteGuideGrpc.newBlockingStub(channel);`
* **RouteGuideFutureStub** 

We need to use these stub, to invoke the exposed service, from a client that we create.
```java
public class RouteGuideClient{
  private RouteGuideStub asyncStub;
  private RoutGuideBlockingStub blockingStub;
  
  public RouteGuideClient(Channel channel){
    asyncStub = RouteGuideGrpc.newStub(channel);
    blockingStub = RouteGuideGrpc.newBlockingStub(channel);
  }
  
  public static void main(String[] args){
    ManagedChannel channel = ManagedChannelBuilder.forTarget("localhost:7070").usePlainText.build();
    RouteGuideClient client = new RouteGuideClient(channel);
    // invoke methods of RouteGuideClient now
  }
}
```


