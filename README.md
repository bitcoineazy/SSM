# Simple-Server-Monitoring
SSM - Simple Server Monitoring

Grafana & Prometheus & Node Exporter with Docker

- Grafana - visualise all data
- Prometheus - all metrics collected here 
- Node Exporter - collecting metrics and sending to Prometheus


## Steps

1. Grafana install:
- grafana/docker-compose.yml
`
version: "3.8"
services:
  grafana:
    image: grafana/grafana
    container_name: grafana
    restart: unless-stopped
    ports:
     - '3001:3000'
    volumes:
      - grafana-storage:/var/lib/grafana
volumes:
  grafana-storage: {}
`

2. Prometheus install and setup:
- prometheus/docker-compose.yml
`
version: '3.8'
services:
  prometheus:
    image: prom/prometheus
    container_name: prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
    ports:
      - 9090:9090
    restart: unless-stopped
    volumes:
      - ./prometheus:/etc/prometheus
      - prom_data:/prometheus
volumes:
  prom_data:
`
