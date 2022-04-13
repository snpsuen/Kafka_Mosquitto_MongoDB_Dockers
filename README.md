# Kafka_Mosquitto_MongoDB_Dockers
Baedung came up with an excellent follow-along guide to set up and test out an Mosquitto/Kafka/MongoDB pipeline within an all-in-one docker enviroment, https://www.baeldung.com/kafka-connect-mqtt-mongodb#2-docker-compose-file.

This is a revision of the guide to update the docker image versions and modify the container settings, propertes where necessary to make the pipeline work. 
<p> Summary of key change logs:

<pre>
(1)  All docker images pulled are of the latest version as of 12 April.
confluentinc/cp-kafka-connect   latest              636808fa6198        6 days ago          1.45GB
confluentinc/cp-kafka           latest              5c24320a9696        6 days ago          783MB
eclipse-mosquitto               latest              58900513926f        6 days ago          11.8MB
zookeeper                       latest              3bfde2963555        12 days ago         279MB
efrecon/mqtt-client             latest              d035c182da36        6 months ago        6.46MB
mongoclient/mongoclient         latest              16ff4e68d176        18 months ago       1.18GB

(2)  Before deploying the cp-kafka container, need to create a new user and group, both named appuser (uid 1000, gid 1000), on the host, assign it to be the owner of the local directoty ./kafka/data, to be mounted as /var/lib/kafka/data in the container.
Otherwise, cp-kafka would complain /var/lib/kafka/data is not writable and fail to start.

(3)  Need to spell out the environment variable ZOO_SERVERS for zookeeper exactly as follows in docker-compose.yaml:
ZOO_SERVERS: server.1=zookeeper:2888:3888;2181
Otherwise, zookeeper would complain about an SASL authentication error and cp-kaffka would fail to start.

(4) MongoDB no longer supports the --smallfiles option. Need to remove it from the command attribute for mongodb in docker-compose.yaml:
 command: --bind_ip_all

(5) To deploy the eclipse-mosquitto MQTT broker container, need to enter the container and edit /mosquitto/config/mosquitto.conf to speicfy these settings explicitely:
listener 1883
allow_anonoymous true
Otherwise, the container would throw an error that no address is available and the the Confluent MQTT source connector would fail to connect to it.
Workaround is to commit the container to a new image, snpsuen/eclipse-mosquitto:2.0.14, after modifying mosquitto.conf and pull it to deploy afterward.

(6) Finally, the MongoDB-supplied sink connector, com.mongodb.kafka.connect.MongoSinkConnector, is adopted and should be configured at a minimum as follows in connect-mongodb-sink.json:
{
	"name": "mongo-sink",
	"config": {
		"connector.class": "com.mongodb.kafka.connect.MongoSinkConnector",
		"tasks.max": "1",
		"topics": "connect-custom",		
		"connection.uri": "mongodb://mongo-db:27017",
		"database": "test",
		"collection": "MyCollection",
		"key.converter": "org.apache.kafka.connect.json.JsonConverter",
		"key.converter.schemas.enable": false,
		"value.converter": "org.apache.kafka.connect.json.JsonConverter",
		"value.converter.schemas.enable": false
	}
}
