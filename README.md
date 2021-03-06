# EntregaTaller3-Grupo3

## Contexto
Este taller consiste en implementar una arquitectura IoT con 5 tópicos previamente definidos. Para lograr dicha interacción, es necesario contar con las siguientes herramientas:

* Arduino
* Library Firmata
* Library Johnny-Five
* Nodejs
* MQTT (mosquitto)
* Kafka
* Zookeeper
* Redis

## Configuración y Responsabilidades
A continuación, se detalla la configuración de tópicos seleccionada, los responsables de su desarrollo y la correspondencia con el código fuente publicado:
* TÓPICO - RESPONSABLE - DIRECTORIO
* Temperatura - Juvenal - temperatura
* Corriente - Carlos - corriente
* Luz - Martin - lumens
* Voltaje - Cristian - voltaje
* Ubicación - David - adjust_lighting

## Paso a paso Implementación
Para poner en ejecución la aplicación junto con cada uno de sstópicos, se recomienda efectuar los siguientes pasos sobre una máquina virtual en EC2 de AWS:

### MOSQUITTO
Nos suscribimos a un nuevo tópico con el nombre numbers
```sh
mosquitto_sub -d -t numbers
```
### KAFKA
Se deja en ejecución nuestro bridge de mosquitto a kafka. Primero nos ubicamos en la carpeta de kafka:
```sh
cd kafka_2.11-1.1.0/
--Ejecutamos zookeeper y kafka en background

nohup bin/zookeeper-server-start.sh config/zookeeper.properties & 

nohup bin/kafka-server-start.sh config/server.properties &
```
Creamos un nuevo tópico con el nombre "nombre_del_tópico"
```sh
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic numbers
```
Se ejecuta:
```sh
nodejs bridge_kafka_mosquitto.js
```
### REDIS - KAFKA Consumer
Vamos a ejecutar una aplicación que estará escuchando de kafka y almacenará el dato que llegue en un servidor de redis en nuestra instancia. Antes que nada, ubíquese sobre la carpeta de redis e instale el servidor
```sh
cd redis-stable/utils

sudo ./install_server.sh
```
Redis es una base de datos en memoria que funciona con el esquema llave-valor, por lo que almacenará el último dato que venga de kafka siguiendo el patrón publish-suscribe.

Ejecutamos la aplicación que consumirá de kafka:
```sh
nodejs kafka_consumer.js
```
### SocketIO
Ejecutaremos nuestro servidor de Socket IO. La aplicación que se ejecutará se conectará a redis en modo de suscriptor y publicará a todos los clientes conectados.
```sh
nodejs index.js
```

## Referencias
* Arduino (https://www.arduino.cc/en/Main/Software)
* Johnny-Five (http://johnny-five.io/)
* Mosquitto (http://mosquitto.org/)
* Kafka (http://kafka.apache.org/)
* REDIS (https://redis.io/)
* SocketIO (https://socket.io/)
* Spark Streaming (http://spark.apache.org/docs/latest/streaming-programming-guide.html)
