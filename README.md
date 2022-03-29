# Kafka the slow way

## Startup Zookeeper & Kafka

````
./zookeeper-server-start.sh config/zookeeper.properties
./kafka-server-start.sh config/server.properties
````

## Startup Schema Registry

````
./schema-registry-start schema-registry.properties
````
## Create a new Topic

````
./kafka-topics --bootstrap-server localhost:9092 --topic test-ju --create
````

## Register a new Subject

````
curl http://localhost:8080/subjects/test-ju-value/versions \
-X POST -H "Content-type: application/json" -d @schema.avsc
````
Note down curl response : `{"id": 1}`

## Produce data into the topic

````
./kafka-avro-console-producer --bootstrap-server localhost:9092 --topic test-ju \
--property schema.registry.url=http://localhost:8080 \
--property value.schema.id=1
>
````
Paste this  in the command line :
````json
{
  "orderId" : 1234,
  "orderLines" : [ {
    "itemId" : "Paracetamol",
    "quantity" : 2
  }, {
    "itemId" : "Fatigue",
    "quantity" : 9999
  }, {
    "itemId" : "Sense Of Achievement",
    "quantity" : 0
  } ]
}
````
CTRL+C to stop the Producer
## Consume the data from the topic
````
./kafka-avro-console-consumer --bootstrap-server localhost:9092 --topic test-ju \
--property schema.registry.url=http://localhost:8080 --from-beginning

{
  "orderId" : 1234,
  "orderLines" : [ {
    "itemId" : "Paracetamol",
    "quantity" : 2
  }, {
    "itemId" : "Fatigue",
    "quantity" : 9999
  }, {
    "itemId" : "Sense Of Achievement",
    "quantity" : 0
  } ]
}

Processed a total of 1 messages
````