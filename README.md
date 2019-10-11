# grafana
quick guide to setup grafana for a live site with live sql widgets for rdm, and other stuff! :)


Welcome to the guide!

`cd ~` or where ever you want to set this up

`mkdir grafana && cd grafana`

`sudo nano docker-compose.yml` or whatever text editor you prefer

copy this into it and save it.

```yml
version: "3"
services:
  grafana:
    image: grafana/grafana
    container_name: grafana
    restart: always
    ports:
      - 3000:3000
    networks:
      - monitoring
    volumes:
      - grafana-volume:/var/lib/grafana
  influxdb:
    image: influxdb
    container_name: influxdb
    restart: always
    ports:
      - 8086:8086
    networks:
      - monitoring
    volumes:
      - influxdb-volume:/var/lib/influxdb
networks:
  monitoring:
volumes:
  grafana-volume:
    external: true
  influxdb-volume:
    external: true
```

`docker network create monitoring`
`docker volume create grafana-volume`
`docker volume create influxdb-volume`

now we need to prepare the Influxdb parameters, for this we’ll run the container with some environment variables for creating database and users:


```
server1$ docker run --rm \
  -e INFLUXDB_DB=telegraf -e INFLUXDB_ADMIN_ENABLED=true \
  -e INFLUXDB_ADMIN_USER=admin \
  -e INFLUXDB_ADMIN_PASSWORD=supersecretpassword \
  -e INFLUXDB_USER=telegraf -e INFLUXDB_USER_PASSWORD=secretpassword \
  -v influxdb-volume:/var/lib/influxdb \
  influxdb /init-influxdb.sh
```

We run this container with –rm key, this will only create configs and remove the container after.
Well all preparations are done, and we ready to start our new monitoring system, will do it by using docker-compose, go to the grafana Dir and run:

`docker-compose up -d`
