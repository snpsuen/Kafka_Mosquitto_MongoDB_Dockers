# Kafka_Mosquitto_MongoDB_Dockers
Baedung came up with an excellent follow-along guide to set up and test out an Mosquitto/Kafka/MongoDB pipeline within an all-in-one docker enviroment, https://www.baeldung.com/kafka-connect-mqtt-mongodb#2-docker-compose-file.

This is a revision of the guide to update the docker image versions and modify the container settings, propertes where necessary to make the pipeline work. 
<p> Summary of key change logs:

<pre>
(1)  All docker images pulled are of the latest version as of 12 April <br>
confluentinc/cp-kafka-connect   latest              636808fa6198        6 days ago          1.45GB
confluentinc/cp-kafka           latest              5c24320a9696        6 days ago          783MB
eclipse-mosquitto               latest              58900513926f        6 days ago          11.8MB
zookeeper                       latest              3bfde2963555        12 days ago         279MB
efrecon/mqtt-client             latest              d035c182da36        6 months ago        6.46MB
mongoclient/mongoclient         latest              16ff4e68d176        18 months ago       1.18GB

(2)  For the cp-kafk

</pre>
