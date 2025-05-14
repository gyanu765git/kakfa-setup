# kafka with zookeeper and UI kafdrop in ubuntu 24.04

# Install Java
	1. sudo apt update
	2. sudo apt install openjdk-17-jdk -y
	3. java -version

# Create a Kafka User (Optional but Recommended)
	1. sudo adduser kafka
	2. sudo usermod -aG sudo kafka
	3. su - kafka

# Download Kafka (latest stable version)
	1. Go to https://kafka.apache.org/downloads and copy the latest Scala 2.13 binary link.
	2. extract it and and move it to kafka folder with follwing command:
	3. mv /home/gyanu/Downloads/kafka_2.13-3.7.0 kafka
	4. cd kafka


# Start Zookeeper and Kafka Server: note: you must be inside kafka folder
	# Start Zookeeper (default port 2181)
	1. bin/zookeeper-server-start.sh config/zookeeper.properties

	# Start Kafka (default port 9092)
	2. bin/kafka-server-start.sh config/server.properties

# Create systemd Services (Recommended for Production)

	1. sudo nano /etc/systemd/system/zookeeper.service

	[Unit]
	Description=Apache Zookeeper server
	After=network.target

	[Service]
	Type=simple
	User=kafka
	ExecStart=/home/kafka/kafka/bin/zookeeper-server-start.sh /home/kafka/kafka/config/zookeeper.properties
	ExecStop=/home/kafka/kafka/bin/zookeeper-server-stop.sh
	Restart=on-abnormal

	[Install]
	WantedBy=multi-user.target
	
	2. sudo nano /etc/systemd/system/kafka.service
	
	[Unit]
	Description=Apache Kafka Server
	After=zookeeper.service

	[Service]
	Type=simple
	User=kafka
	ExecStart=/home/kafka/kafka/bin/kafka-server-start.sh /home/kafka/kafka/config/server.properties
	ExecStop=/home/kafka/kafka/bin/kafka-server-stop.sh
	Restart=on-abnormal

	[Install]
	WantedBy=multi-user.target


# Then enable and start:

	1. sudo systemctl daemon-reexec
	2. sudo systemctl daemon-reload
	3. sudo systemctl enable zookeeper
	4. sudo systemctl enable kafka
	5. sudo systemctl start zookeeper
	6. sudo systemctl start kafka

	7. sudo systemctl status kafka
	8. sudo systemctl status zookeeper



# bonus: intall UI(kafdrop) for it: 

sudo docker run -d \
  --name kafdrop \
  --network host \
  -e KAFKA_BROKERCONNECT=localhost:9092 \
  obsidiandynamics/kafdrop
  
  
  
1. sudo netstat -tulnp | grep 9092
2. telnet localhost 9092
