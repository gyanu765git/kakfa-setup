# kakfa-setup
kafka with zookeeper and UI kafdrop


install kafka with zookeeper in ubuntu 24.04

1. Install Java
sudo apt update
sudo apt install openjdk-17-jdk -y
java -version

2. Create a Kafka User (Optional but Recommended)
sudo adduser kafka
sudo usermod -aG sudo kafka
su - kafka

3. Download Kafka (latest stable version)
Go to https://kafka.apache.org/downloads and copy the latest Scala 2.13 binary link.
extract it and and move it to kafka folder with follwing command:
mv /home/gyanu/Downloads/kafka_2.13-3.7.0 kafka
cd kafka


4. Start Zookeeper and Kafka Server: note: you must be inside kafka folder
# Start Zookeeper (default port 2181)
bin/zookeeper-server-start.sh config/zookeeper.properties

# Start Kafka (default port 9092)
bin/kafka-server-start.sh config/server.properties

5. Create systemd Services (Recommended for Production)

sudo nano /etc/systemd/system/zookeeper.service

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

sudo nano /etc/systemd/system/kafka.service

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


6. Then enable and start:

sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable zookeeper
sudo systemctl enable kafka
sudo systemctl start zookeeper
sudo systemctl start kafka

sudo systemctl status kafka
sudo systemctl status zookeeper



bonus: intall UI(kafdrop) for it: 

sudo docker run -d \
  --name kafdrop \
  --network host \
  -e KAFKA_BROKERCONNECT=localhost:9092 \
  obsidiandynamics/kafdrop
  
  
  
sudo netstat -tulnp | grep 9092
telnet localhost 9092
