# Simple-Server-Monitoring
SSM - Simple Server Monitoring

Grafana & Prometheus & Node Exporter with Docker

- Grafana - visualise all data
- Prometheus - all metrics collected here 
- Node Exporter - collecting metrics and sending to Prometheus


## Steps

## 1. Grafana install:
- grafana/docker-compose.yml
```
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
```

## 2. Prometheus install and setup:
- prometheus/docker-compose.yml
```
version: '3.8'
services:
  prometheus:
    image: prom/prometheus
    container_name: prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
    ports:
      - 3090:9090
    restart: unless-stopped
    volumes:
      - ./prometheus:/etc/prometheus
      - prom_data:/prometheus
volumes:
  prom_data:
```
- prometheus/prometheus/prometheus.yml
```
global:
  scrape_interval: 15s
  scrape_timeout: 10s
  evaluation_interval: 15s
alerting:
  alertmanagers:
    - static_configs:
      - targets: []
      scheme: http
      timeout: 10s
      api_version: v1
scrape_configs:
  - job_name: prometheus
    honor_timestamps: true
    scrape_interval: 15s
    scrape_timeout: 10s
    metrics_path: /metrics
    scheme: http
    static_configs:
      - targets:
        - localhost:9090
  - job_name: node # Change to whatever you like
    static_configs:
      - targets: ['109.120.133.122:9100'] # Change this to your server's IP, 9100 - Node Exporter port
```

## 3. Node Exporter install and setup:
 
#### Download Node Exporter
Begin by downloading Node Exporter using the wget command:
```
wget https://github.com/prometheus/node_exporter/releases/download/v1.8.1/node_exporter-1.8.1.linux-amd64.tar.gz
```
Note: Ensure you are using the latest version of Node Exporter and the correct architecture build for your server. The provided link is for amd64. For the latest releases, check here - Prometheus Node Exporter Releases

#### Extract the Contents
After downloading, extract the contents with the following command:

```
tar xvf node_exporter-1.7.0.linux-amd64.tar.gz
```
#### Move the Node Exporter Binary
Change to the directory and move the node_exporter binary to /usr/local/bin:

```
cd node_exporter-1.7.0.linux-amd64
sudo cp node_exporter /usr/local/bin
```

#### Create a Node Exporter User
Create a dedicated user for running Node Exporter:

```
sudo useradd --no-create-home --shell /bin/false node_exporter
```

#### Assign ownership permissions of the node_exporter binary to this user:
```
sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter
```
#### Configure the Service
To ensure Node Exporter automatically starts on server reboot, configure the systemd service:
```
sudo nano /etc/systemd/system/node_exporter.service
```
Then, paste the following configuration:
```
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter
Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target
```
Save and exit the editor.

Enable and Start the Service
#### Reload the systemd daemon

```
sudo systemctl daemon-reload
```
Enable the Node Exporter service:

```
sudo systemctl enable node_exporter
```

#### Start the service

```
sudo systemctl start node_exporter
```
To confirm the service is running properly, check its status:
```
sudo systemctl status node_exporter.service
```