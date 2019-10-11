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

1. `docker network create monitoring`

2. `docker volume create grafana-volume`

3. `docker volume create influxdb-volume`

now we need to prepare the Influxdb parameters, for this we’ll run the container with some environment variables for creating database and users:

change `supersecretpassword` to your prefered password


```
server1$ docker run --rm \
  -e INFLUXDB_DB=telegraf -e INFLUXDB_ADMIN_ENABLED=true \
  -e INFLUXDB_ADMIN_USER=admin \
  -e INFLUXDB_ADMIN_PASSWORD=supersecretpassword \
  -e INFLUXDB_USER=telegraf -e INFLUXDB_USER_PASSWORD=secretpassword \
  -v influxdb-volume:/var/lib/influxdb \
  influxdb /init-influxdb.sh
```

We ran this container with –rm key, this will only create configs and remove the container after.


With all preparations are done, and we ready to start our new monitoring system, 

we will do it by using docker-compose, in the grafana directory run:

`docker-compose up -d`

```bash
Creating network "monitoring_monitoring" with the default driver
Creating grafana
Creating influxdb

server1$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
8128b72bdf44        grafana/grafana     "/run.sh"                23 seconds ago      Up 20 seconds       0.0.0.0:3000->3000/tcp   grafana
c00416d0d170        influxdb            "/entrypoint.sh infl…"   23 seconds ago      Up 21 seconds       0.0.0.0:8086->8086/tcp   
```

OK, all containers were created and started, so our monitoring system is ready to serve incoming requests. We expose few ports, as you can see in docker-compose file, the 8086 HTTP API port for Influxdb data and port 3000 for Grafana web UI.
And we almost done with our new monitoring system, it’s really quick and easy using Docker. To fully complete we only need to configure Grafana a bit, create a dashboard and new data source for Influxdb.
For this will go to localhost:3000 in a browser, and login to the Grafana web UI for very first time using:

login: admin
password:admin

Then Grafana will ask you to change password, Congrats! now you need to give it some mysql queries and make some widgets! :)
