# CIG

> CAdvisor+ InfluxDB + Granfana

## setting

```yml
version: '3.1'

volumes:
  grafana_data: {}

services:
  influxdb:
    image: tutum/influxdb:0.9
    restart: always
    environment:
      - PRE_CREATE_DB=cadvisor
    ports:
      - '8083:8083'
      - '8086:8086'
    volumes:
      - ./data/influxdb:/data

  cadvisor:
    image: google/cadvisor:v0.32.0
    links:
      - influxdb:influxsrv
    command:
      - -storage_driver=influxdb
      - -storage_driver_db=cadvisor
      - -storage_driver_host=influxsrv:8086
    restart: always
    ports:
      - '8080:8080'
    volumes:
      - /:/rootfs:ro
      - /var/run:/var/run:rw
      - /sys:/sys:ro
      - /var/lib/docker/:/var/lib/docker:ro

  grafana:
    image: grafana/grafana:8.5.2
    user: '104'
    restart: always
    links:
      - influxdb:influxsrv
    ports:
      - '3000:3000'
    volumes:
      - grafana_data:/var/lib/grafana
    environment:
      - HTTP_USER=admin
      - HTTP_PASS=admin
      - INFLUXDB_HOST=influxsrv
      - INFLUXDB_PORT=8086
```

## check syntax

```bash
docker-compose config -q
```

## run

```bash
docker-compose up -d
```

## view

- [InfluxDB](http://localhost:8083) root/root
- [CAdvisor](http://localhost:8080)
- [Grafana](http://localhost:3000) admin/admin
