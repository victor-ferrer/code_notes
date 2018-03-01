# Cloudera Quickstart docker container

*Pull the image*

```
# Pulls the image (4Gb!)
docker pull cloudera/quickstart
```

*Set a variable with all needed ports to be opened*
```
# Ports to be opened (most commonly used)
# - 8888      expose hue interface
# - 7180      expose cloudera manager
# - 80        expose cloudera examples
# - 8983      expose port of Web UI solr search 
# - 50070     expose name node web ui interface
# - 50090     expose secondary name node
# - 50075     expose data  node
# - 50030     expose job tracker
# - 50060     expose task trackers
# - 60010     expose hbase master status
# - 60030     expose hbase region server
# - 9095      expose hbase thrift server
# - 8020      expose hdfs port
# - 8088      expose job tracker port
# - 4040      expose port of spark
# - 18088     expose history server web interface
# ...
ports="-p 8888:8888 -p 7180:7180 -p 80:80 -p 4040:4040 -p 4041:4041 \
-p 4042:4042  -p 4043:4043 -p 9092:9092 -p 2181:2181 -p 8020:8020   \ 
-p 18088:18088 -p 10000:10000 -p 21050:21050 -p 50070:50070  -p 50075:50075 \
-p 50060:50060  -p 50030:50030 -p 50010:50010"
```

*Set a variable with a volume for file exchange with the container*
```
# Volume used to exchange stuff with the running container
localVolumeDir=/home/me/cloudera_exchange_dir
```

*Run the container and store its ID*
```
containerId=`docker run --hostname=quickstart.cloudera -d \
                -v $localVolumeDir:/volume
                --privileged=true -t -i $ports cloudera/quickstart /usr/bin/docker-quickstart`
```                
   
*Install and run the Kafka server*

Kafka does not seem to be installed, but you can quickly do it:


```
# See https://kafka.apache.org/quickstart

# Download the binaries and execute this command:
# > bin/kafka-server-start.sh config/server.properties

# If you want to access Kafka from outside the container, you need to change this line in server.properties
# zookeeper.connect=localhost:2181
# And replace it with the Docker host public IP address
```

*Optional: Run Cloudera Manager*

```
sudo su
cd /home/cloudera/
./cloudera-manager
```
  