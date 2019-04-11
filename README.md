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
This will start 4 Containers with each exposing its default port to the host machine.
* Kafka -> 127.0.0.1:9092
* Zookeeper -> 127.0.0.1:2181
* Grafana -> 127.0.0.1:3000
* Prometheus -> 127.0.0.1:9090

Simply oben your browser at `http://127.0.0.1:3000` and you can log into the grafana dashboard using the following credentials:
```
Username: admin
Password: secret
```
The prometheus instance will scrape every 15 seconds the host machine at the following endpoint:
```
http://127.0.0.1:3149/admin/metrics
```
Make sure your microservice exposes a corresponding prometheus metrics endpoint at this path.

## Produce messages with kafakcat
In a local environment it can be useful to manually produce content in your kafka broker. 
[Kafkacat](https://github.com/edenhill/kafkacat) is useful tool for this. For example, 
after the docker environment is running, use the following command to produce a json message in the locally running broker:
```
kafkacat -b localhost:9092 -t orders -T -P myjson.json
```
Where the file `myjson.json` is a locally existing file with valid json data format.

Enjoy.
