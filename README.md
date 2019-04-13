# Docker setup for simple monitoring stack of kafka message related metrics 
This repository contains a simple docker setup to quickly spin up a 
monitoring stack consisting of a grafana frontend, prometheus backend and a minimal kafka broker.
This is useful if you want to debug the implementation of your metrics logic in
a mircoservice locally before rolling it out to a test or production environment.

If the setup of your microservice infrastructure includes those technologies you can easily setup a debug environment 
locally using the configuration provided here.

## Usage
```
git clone https://github.com/oemergenc/dc-kafka-grafana-prometheus
cd dc-kafka-grafana-prometheus
docker-compose up
```
This will start multiple containers with each exposing its default port to the host machine.
* Kafka -> 127.0.0.1:9092
* Kafka-Topic-Ui -> 127.0.0.1:8000
* Zookeeper -> 127.0.0.1:2181
* Grafana -> 127.0.0.1:3000
* Prometheus -> 127.0.0.1:9090

### Kafka
There are several containers involved in the kafka setup to allow you to inspect the message published onto this broker.
The provided setup automatically start a web ui, which is available at the following url from your host machine,
```
http://127.0.0.1:8000
```
Besides this the configuration also allows external clients to connect to the broker. For this you could use the famous Kafka Tool
and point it to the default urls of zookeeper and kafka mentioned above to see the messages of the broker.

### Grafana
The grafana container ist preconfigured to automatically setup provisioned dashboards within the folder `dashboards`.

Simply oben your browser at `http://127.0.0.1:3000` and you can log into the grafana dashboard using the following credentials:
```
Username: admin
Password: secret
```
There you should find your provisioned dashboards.

### Prometheus
The prometheus container ist preconfigured to scrape every 15 seconds your host machine at the following endpoint:
```
http://127.0.0.1:3149/admin/metrics
```
Make sure your microservice exposes a corresponding prometheus metrics endpoint at this path.

Futhermore the container allows admin api access. Do not use this in any production environment. 
This allows to delete all data from prometheus using REST Api. For example to delete all which were aquired using the mentioned 
endpoint you could use the following shell command:
```
curl -X POST -g 'http://localhost:9090/api/v1/admin/tsdb/delete_series?match[]={job="local_service"}'
```

## Produce messages with kafkacat
In a local environment it can be useful to manually produce content in your kafka broker. 
[Kafkacat](https://github.com/edenhill/kafkacat) is useful tool for this. For example, 
after the docker environment is running, use the following command to produce a json message in the locally running broker:
```
kafkacat -b localhost:9092 -t mytopic -T -P myjson.json
```
Where the file `myjson.json` is a locally existing file with valid json data format.

Your microservice application typically spawns a kafka consumer which consumes this message and while doing so
produces metrics which will be scraped by prometheus on the aforementioned endpoint.

Enjoy.
