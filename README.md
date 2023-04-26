# study4tw-23
A simple demo for Apache APISIX sharing based on [https://github.com/api7/grpc_server_example](https://github.com/api7/grpc_server_example)


### 1. Create Namespace
~~~bash
$> kubectl apply -f namespace.yaml
~~~

### 2. Deploy Hello gRPC service
~~~bash
$> kubectl apply -f deployment-grpc-server.yaml -n grpc
$> kubectl apply -f grpc-service.yaml -n grpc
~~~

### 3. Export gRPC port and test it
~~~bash
$> kubectl port-forward service/grpc-hello-svc 50051:50051 -n grpc
$> grpcurl -d '{"name": "study4tw"}' -plaintext 127.0.0.1:50051 helloworld.Greeter.SayHello
~~~

### 4. Deploy Apache APISIX
~~~bash
$> helm repo add apisix https://charts.apiseven.com
$> helm repo update
$> helm install apisix apisix/apisix --create-namespace  --namespace apisix
~~~

### 5. Config Apache APISIX 

#### 5.1 Port-Forward for admin plane
~~~bash
$> kubectl port-forward service/apisix-admin 9180:9180 -n apisix
~~~

#### 5.2 add proto, consumer, and routes
~~~bash
$> curl http://127.0.0.1:9180/apisix/admin/protos/1 -H 'X-API-KEY: edd1c9f034335f136f87ad84b625c8f1' -X PUT --data-binary @proto.json | jq .
$> curl http://127.0.0.1:9180/apisix/admin/consumers -H 'X-API-KEY: edd1c9f034335f136f87ad84b625c8f1' -X PUT --data-binary @consumer.json | jq .
$> curl http://127.0.0.1:9180/apisix/admin/routes/1 -H 'X-API-KEY: edd1c9f034335f136f87ad84b625c8f1' -X PUT --data-binary @route.json | jq .
$>
$> curl -i -H 'apikey: auth-one' http://127.0.0.1:8080/grpctest\?name=world
~~~
o
