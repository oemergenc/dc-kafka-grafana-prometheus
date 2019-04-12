FROM prom/prometheus:v2.8.1
ADD ./config.yml /etc/prometheus/config.yml
CMD [ "--config.file=/etc/prometheus/config.yml","--web.enable-admin-api" ]
