## 安装kafka

> **NOTE: Your local environment must have Java 8+ installed.**

[官方教程](https://kafka.apache.org/34/documentation.html)

```sh
#  install jdk 
sudo apt install openjdk-8-jdk-headless

cd ~

wget https://archive.apache.org/dist/kafka/3.4.0/kafka_2.13-3.4.0.tgz

tar -xzf kafka_2.13-3.4.0.tgz

cd kafka_2.13-3.4.0

KAFKA_CLUSTER_ID="$(bin/kafka-storage.sh random-uuid)"

# 格式化日志
bin/kafka-storage.sh format -t $KAFKA_CLUSTER_ID -c config/kraft/server.properties

# 启动kafka
bin/kafka-server-start.sh config/kraft/server.properties

# 创建topic - 后续有用上
bin/kafka-topics.sh --create --topic alarm-events --bootstrap-server localhost:9092

# 查看topic 
bin/kafka-topics.sh --describe --topic alarm-events --bootstrap-server localhost:9092

bin/kafka-topics.sh --list --bootstrap-server localhost:9092


#---以下是测试---

# 进入生产者交互窗口
bin/kafka-console-producer.sh --topic alarm-events-new --bootstrap-server localhost:9092

# 进入消费者交互窗口
bin/kafka-console-consumer.sh --topic alarm-events-new --from-beginning --bootstrap-server localhost:9092

# 查看消费者组 - 后续有用上
bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --list


# 一个简单的web UI监控服务
docker run --net=host \
	-e KAFKA_CLUSTERS_0_NAME=local \
	-e KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS=localhost:9092 \
	-d provectuslabs/kafka-ui:latest
```

## librdkafka - Apache Kafka C/C++ client library

**不要用apt安装命令安装，要用编译源码安装**

[librdkafka](https://github.com/confluentinc/librdkafka)

```sh

cd ~
git clone https://github.com/confluentinc/librdkafka.git
cd librdkafka
./configure
make
sudo make install

```

## 生产者 消费者封装

```c

int producer(const char *brokers,const char *topic,char *buf, size_t buf_size);

int consumer(const char *brokers, const char *groupid, char **topics, int topic_cnt);

```

## 部署配置

> hack中的脚本存放位置

```sh
cd kafka_install
tree -L 2

├── install.sh # 拷贝此处
├── kafka_2.13-3.5.0
│   ├── bin
│   ├── config
│   ├── kafka.service # 拷贝此处
│   ├── libs
│   ├── LICENSE
│   ├── licenses
│   ├── logs
│   ├── NOTICE
│   ├── site-docs
│   ├── start_kafka.sh # 拷贝此处
│   ├── stop_kafka.sh # 拷贝此处
├── kafka_client
│   ├── alarm_send.service # 拷贝此处
│   ├── build
│   ├── client.c
│   ├── CMakeLists.txt
│   ├── http.c
│   ├── main.c
│   ├── README.MD
│   ├── soc.c
│   └── soc.h
├── librdkafka
└── uninstall.sh # 拷贝此处

```