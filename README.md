`https://objectpartners.com/2014/05/06/setting-up-your-own-apache-kafka-cluster-with-vagrant-step-by-step/`

# Apache Kafka cluster with zookeeper dependency.

# Vagrant — Centos
`vagrant up` : Bring the virtualbox up
`vagrant ssh` : logs into the virtualbox instance.

# Install Java in Centos
`sudo yum install java-11-openjdk`

# Download Kafka
`mkdir /usr/local/kafka
cd /usr/local/kafka
wget http://apache.claz.org/kafka/2.3.0/kafka_2.12-2.3.0.tgz
tar -zxvf kafka_2.12-2.3.0.tgz
cd kafka_2.11-0.10.2.0`

# Vagrant Clusters
* 192.168.33.100

# Download Zookeeper
`mkdir /usr/local/zookeeper
cd /usr/local/zookeeper
wget https://www-us.apache.org/dist/zookeeper/zookeeper-3.5.6/apache-zookeeper-3.5.6.tar.gz 
tar -zxvf apache-zookeeper-3.5.6.tar.gz 
cd apache-zookeeper-3.5.6/conf/
cp zoo_sample.cfg zoo.cfg
mkdir -p /var/zookeeper/data
vim /usr/local/zookeeper/zookeeper-3.4.9/conf/zoo.cfg`
* Add `server.1=192.168.33.100:2888:3888`
echo "server.1=192.168.33.100:2888:3888" | cat /usr/local/zookeeper/apache-zookeeper-3.5.6/conf/zoo_sample.cfg - > temp && mv temp /usr/local/zookeeper/apache-zookeeper-3.5.6/conf/zoo_sample.cfg

# Create 'myid' file for zookeeper
`touch /var/zookeeper/data/myid
echo "1" > /var/zookeeper/data/myid`

# Update Kafka server.properties
`vim /usr/local/kafka/kafka_2.11-0.10.2.0/config/server.properties`
* change `broker.id=0` to `broker.id=1`
* sed -i 's/broker.id=0/broker.id=1/gI'  /usr/local/kafka/kafka_2.12-2.3.0/config/server.properties
* change `zookeeper.connect=localhost:2181` to `zookeeper.connect=192.168.33.100:2181`
* sed -i 's/localhost:2181/192.168.33.100:2181/gI'  /usr/local/kafka/kafka_2.12-2.3.0/config/server.properties

# Update base_profile
```bash
export ZK_HOME=/usr/local/zookeeper/apache-zookeeper-3.5.6/
echo "export ZK_HOME=/usr/local/zookeeper/apache-zookeeper-3.5.6/" | cat - ~/.bash_profile > temp && mv temp ~/.bash_profile
export KAFKA_HOME=/usr/local/kafka/kafka_2.12-2.3.0/
echo "export KAFKA_HOME=/usr/local/kafka/kafka_2.12-2.3.0/" | cat - ~/.bash_profile > temp && mv temp ~/.bash_profile

sed -i 's/\$HOME\/bin/\$HOME\/bin:\$ZK_HOME\/bin:\$KAFKA_HOME\/bin/gI' ~/.bash_profile
PATH=$PATH:$HOME/.local/bin:$HOME/bin:$ZK_HOME/bin:$KAFKA_HOME/bin
export PATH
```

# Starting zookeeper
```bash
sudo $ZK_HOME/bin/zkServer.sh start
```
# Starting kafka
```bash
sudo $KAFKA_HOME/bin/kafka-server-start.sh $KAFKA_HOME/config/server.properties &
```

# Testing list of topics
`$KAFKA_HOME/bin/kafka-topics.sh --zookeeper 192.168.33.100:2181 --list` // Should be blank

# Create a new Kafka topic
```bash
$KAFKA_HOME/bin/kafka-topics.sh --zookeeper 192.168.33.100:2181 --create --replication-factor 1 --partitions 1 --topic topic-1
```

# Push messages to Kafka via console
```bash
$KAFKA_HOME/bin/kafka-console-producer.sh --broker-list 192.168.33.100:9092 --topic topic-1
hello
world!
```

# Read message pushed to Kafka
```bash
$KAFKA_HOME/bin/kafka-console-consumer.sh --zookeeper 192.168.33.100:2181 --topic topic-1 --from-beginning
```

# Clear out iptables for Centos
`iptables -F`


## To run as root$
`su -`
pw: vagrant

## Basic cassandra commands
`service cassandra status` : check cassandra’s status (is it up/down?)
`service cassandra stop` : stops cassandra
`service cassandra start` : starts cassandra

# Interfacing with cassandra
* bash$ cqlsh : brings up the cassandra shell
* `SELECT cluster_name, listen_address FROM system.local;`  — returns clusters
* Creating Keyspace — http://docs.datastax.com/en/cql/3.1/cql/cql_reference/create_keyspace_r.html “creates a top-level namespace”
    * `CREATE KEYSPACE haselton_keyspace_1 WITH replication = {'class': 'SimpleStrategy', 'replication_factor' : 3};`
* A keyspace must be specified before querying cassandra
    * `use haselton_keyspace_1;`
* Running queries can be executed now - http://cassandra.apache.org/doc/latest/cql/dml.html
		* `CREATE TABLE users (username text PRIMARY KEY, first_name text, last_name text) with clustering order by (first_name desc);`
		* `select * from users;`
		* `INSERT INTO users (username, first_name, last_name) VALUES ('shaselton', 'scott', 'haselton');`


# Vagrant — Centos
`vagrant up` : Bring the virtualbox up
`vagrant ssh` : logs into the virtualbox instance.

## To run as root$
`su -`
pw: vagrant

# Installing Cassandra: 
`https://www.liquidweb.com/kb/how-to-install-cassandra-on-centos-7/`

## Basic cassandra commands
`service cassandra status` : check cassandra’s status (is it up/down?)
`service cassandra stop` : stops cassandra
`service cassandra start` : starts cassandra

# Interfacing with cassandra
* bash$ cqlsh : brings up the cassandra shell
* `SELECT cluster_name, listen_address FROM system.local;`  — returns clusters
* Creating Keyspace — http://docs.datastax.com/en/cql/3.1/cql/cql_reference/create_keyspace_r.html “creates a top-level namespace”
    * `CREATE KEYSPACE haselton_keyspace_1 WITH replication = {'class': 'SimpleStrategy', 'replication_factor' : 3};`
* A keyspace must be specified before querying cassandra
    * `use haselton_keyspace_1;`
* 

