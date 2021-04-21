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
### 2. generated classes for server side    
The proto definition above when run through *com.google.protobuf* plugin, generates a class **RouteGuideGrpc.java**. This RouteGuideGrpc class has a static abstract class **RouteGuideImplBase**, which needs to be extended on the server side, to implement the above declared methods. It has the following declarations for the methods declared in the proto service :     
 * ``` public void getFeature(Point request, StreamObserver<Feature> responseObserver) {...}          // Unary call```
 * ``` public void listFeatures(Rectangle request, StreamObserver<Feature> responseObserver) {...}    // server streaming```
 * ``` public StreamObserver<Point> recordRoute(StreamObserver<RouteSummary> responseObserver) {...}  // client streaming```
 * ``` public StreamObserver<RouteNote> routeChat(StreamObserver<RouteNote> responseObserver) {...}   // bi-di streaming```


### 3. generated classes for client side   
The same generated class **RouteGuideGrpc.java** has three more static final classes :   
* **RouteGuideStub**    
  * async stub
  * can be initialized as `RouteGuideGrpc.newStub(channel);`
* **RouteGuideBlockingStub**    
  * blocking stub
  * can be initialized as `RouteGuideGrpc.newBlockingStub(channel);`
* **RouteGuideFutureStub**    


