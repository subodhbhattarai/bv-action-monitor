# bv-action-monitor
A realtime chat application using Springboot as backend, React as frontend, Kafka as MessageBroker, WebSockets as message protocol, in-built H2 database as datastore along with Maven and Tomcat.

Versions used:
  Kafka: 2.13-3.2.0

Before Running the Project

Start Zookeper
  <kafka_install_location>/zookeeper-server-start.sh config/zookeeper.properties

Optional (skip to Start backend if not checking Kafka working)
  Start Kafka
    <kafka_install_location>/kafka-server-start.sh config/server.properties

  Create a Topic
    <kafka_install_location>/kafka-topics.sh --create --topic exampleTopic --bootstrap-server localhost:9092

  Check the status of Topic
    <kafka_install_location>/kafka-topics.sh --describe --topic exampleTopic --bootstrap-server localhost:9092

  Create a producer
    <kafka_install_location>/kafka-console-producer.sh --topic exampleTopic --bootstrap-server localhost:9092

  Create a listener
    <kafka_install_location>/kafka-console-consumer.sh --topic exampleTopic --from-beginning --bootstrap-server localhost:9092

Start backend
  mvn spring-boot:run

After the backend is start, topic kafka-topic is defined from the application.

Check the status of kafka-topic:
  <kafka_install_location>/kafka-topics.sh --describe --topic kafka-topic --bootstrap-server localhost:9092

Create a listener for kafka-topic:
  <kafka_install_location>/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic kafka-topic

A rest endpoint (producer) is exposed to send message to the kafka-topic as below:
  curl --location --request POST 'localhost:8080/api/send' \
  --header 'Content-Type: application/json' \
  --data-raw '{
      "sender" : "John Doe",
      "content" : "test 1 2 3"
  }'

Output will look like this:
  {"sender":"John Doe","content":"test 1 2 3","timestamp":"2022-07-18T21:25:04.239397"}

WebSockets endpoint is exposed via:
  localhost:8080/action-monitor

Access H2 database via console:
  http://localhost:8080/h2-console
  Capture JDBC URL from mvn spring-boot logs to see data 
    (example: 2022-07-19T11:15:50.001+01:00  INFO 193 --- [  restartedMain] com.zaxxer.hikari.pool.HikariPool        : HikariPool-1 - Added connection conn0: url=jdbc:h2:mem:957bef0b-c845-490d-9062-0624649304d2 user=SA )

To-do items in the application:

  1. Create a React app (chat UI app) that consumes two websocket endpoints in the backend: /sendMessage and /addUser
    /sendMessage - To send message to all listeners
    /addUser - To initialize a new chat window with a name
    To create new chat window: localhost:3000 and initialize with your name and start sending messages to default Kafka topic (i.e. kafka-topic)
  
  2. Persist the message data on the kafka-topic to MySQL database by use of Kafka Connectors (JDBC Sink Connector for MySQL) that handles insert and upsert.
  
  3. Send log to websocket endpoint: localhost:8080/action-monitor as the message is sent to kafka-topic.
  
  4. Create unit tests for backend as well as frontend.
