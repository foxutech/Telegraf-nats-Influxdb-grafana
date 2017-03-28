**KING-T**

kapacitor, InfluxDB, NATS server, Grafana - Telegraf


**NOTE: **: Telegraf.conf
Provide NATSERVER details and subject name in telegraf.conf. (Line 19 & 20)
If you have nats server SSL certificates provide certificates path on telegraf.conf.

**Note** Telegraf-backend.conf
Please change influxdb and NATS server IP in appropriate place. (Line 19 & 28) & 
Modify db name and user credentials (Line 20, 24 & 25). Also modify hostname (line 15)

**How to Run**

To run use following Commands, 

$ docker-compose up -d  (before run please confirm with log forwarder you gonna use either nats or kafka, remove unused details in docker-compose.yml file)

once the containers build successfully, you can see the status using

$ docker ps

if any container is not running, please check the logs using to troubleshoot the issue. 

$ docker logs Container-name/container-ID

To check NATS server monitoring interface, use

$ curl http://NATS_SERVER_IP:8222/connz?subs=1 

(Change you host/container IP)

If we are using Kafka (as a producer) instead of NATS please replace following output plugin code with nats in telegraf.conf

```
[[outputs.kafka]]
  brokers = ["localhost:9092"]
  topic = "telegraf"
  routing_tag = "host"
  compression_codec = 0
  required_acks = -1
  max_retry = 3
  data_format = "influx"
```
  
For kafka Consumer in telegraf-backend.conf (replace with nats input)

```
[[inputs.kafka_consumer]]
  topics = ["telegraf"]
  zookeeper_peers = ["localhost:2181"]
  consumer_group = "telegraf_metrics_consumers"
  metric_buffer = 100000
  offset = "oldest"
  data_format = "influx"
```
