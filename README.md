## Steps to implement this project

### Getting started

1. Start EC2 instance & connect
2. _wget_ to download and unzip using _tar_, the **binary file** from Apache Kafka website **(version:3.3.1)**
3. Install java because jvm is needed to run kafka
   > sudo yum install java-1.8.0-openjdk

### Start Kafka Instance

1. _cd_ to kafka dir
2. Run command to start Zookeeper
   > bin/zookeeper-server-start.sh config/zookeeper.properties
3. In another terminal window run the following command to increase the memory capacity for the Kafka server and run Kafka server

   > export KAFKA_HEAP_OPTS="-Xmx256M -Xms128M"
   > bin/kafka-server-start.sh config/server.properties

4. Issue: The server is now running on the private DNS for EC2.
   Solution:
   > Stop zookeeper and kafka instances
   > Change the adverstised.listener to _public_ip_ of EC2; keep the same port no.
   > Restart zookeeper and kafka instances

### Start a new Kakfa topic

1. Another terminal window, another EC2 connection
2. Create a new topis using:

   > bin/kafka-topics.sh --create --topic **topicname** --bootstrap-server **Public IP of EC2**:9092 --replication-factor 1 --partitions 1

   bin/kafka-topics.sh --create --topic topicstk --bootstrap-server 54.147.241.225:9092 --replication-factor 1 --partitions 1

### Start a new Producer

1. Create a producer using:

   > bin/kafka-console-producer.sh --topic **topicname** --bootstrap-server **Public IP of EC2**:9092

   bin/kafka-console-producer.sh --topic topicstk --bootstrap-server 54.147.241.225:9092

### Start a new Consumer

1. Another terminal window, another EC2 connection
2. Create a consumer using:

   > bin/kafka-console-consumer.sh --topic demo_testing2 --bootstrap-server **Public IP of EC2**:9092

   bin/kafka-console-consumer.sh --topic topicstk --bootstrap-server 54.147.241.225:9092

## Simulate stock market data, feed through Kafka, store in an S3 bucket, run a Glue crawler

1. Create _Producer_ using a Python notebook (_python-stk-simulation > kafka_producer.ipynb_), this can be done on the basis of date, but for this simulation I'm randomly sampling rows to feed to the Kafka Producer.
2. Create _Consumer_ using a Python notebook (_python-stk-simulation > kafka_consumer.ipynb_), and write this to a .json file in S3 bucket.
   > Note: Setup IAM role to interact with AWS CLI on the local machine.
3. Stored results in the _AWS S3_ bucket: [link](https://github.com/aniket1899/Stock-Market-ingestion-using-Kafka-AWS-S3-Athena/blob/main/screenshots/AWSS3_data.png)
4. Create a _Crawler_ from _AWS Glue Data Catalog_ to access data in the S3 bucket. Create a new database in Glue to store results.
5. Access data using SQL in _AWS Athena_ by connecting to the Glue database previously created. Create temp bucket as well.
6. We can keep the Producer script running, and notice the results in real time in AWS Athena SQL console.
7. Testing in CLI in EC2: 
   ![link](https://github.com/aniket1899/Stock-Market-ingestion-using-Kafka-AWS-S3-Athena/blob/main/screenshots/test_producer_consumer_CLI.png)
8. Testing with local machine in Python: 
   ![producer](https://github.com/aniket1899/Stock-Market-ingestion-using-Kafka-AWS-S3-Athena/blob/main/screenshots/test_local_producer.png) 
   ![result](https://github.com/aniket1899/Stock-Market-ingestion-using-Kafka-AWS-S3-Athena/blob/main/screenshots/test_consumer_CLI_from_local_producer.png)
