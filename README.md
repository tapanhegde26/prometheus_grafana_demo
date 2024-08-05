# prometheus_grafana_demo
This repo contains steps and code to scrape python app using prometheus and display results on grafana dashboard

## Requirement
- Run prometheus and grafana on local as docker images (Promethes port = 9090, Grafana port = 3000)
- Develop sample python app which can be instrumented and scraped by prometheus
- Configure prometheus and grafana
- Develop dashborad in grafana to visualize data

## Pre-requisites
- Docker installed and up and running
- Python installed

## Steps
- Write sample python program which can be scarped by prometheus for instrumentation, which sends some metrics data

```
from prometheus_client import start_http_server, Summary, Counter
import random
import time

# Create a metric to track time spent and requests made
REQUEST_TIME = Summary('request_processing_seconds', 'Time spent processing request')
REQUEST_COUNT = Counter('request_count', 'Total count of requests')

# Decorate function with metric
@REQUEST_TIME.time()
def process_request(t):
    """A dummy function that takes some time."""
    REQUEST_COUNT.inc()
    time.sleep(t)

if __name__ == '__main__':
    # Start up the server to expose the metrics.
    start_http_server(8000)
    # Generate some requests.
    while True:
        process_request(random.random())
```
- Prepare prometheus.yml config file with following contents

```
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'python-app'
    static_configs:
      - targets: ['localhost:8000']
```
- Run prometheus and grafana docker images on 9090 and 3000 ports respectively

```
docker run -d   --name prometheus   -p 9090:9090   -v $(pwd)/prometheus.yml:/etc/prometheus/prometheus.yml   prom/prometheus
docker run -d -p 3000:3000 --name=grafana grafana/grafana-enterprise
```
- Validate if prometheus and grafana is up and running by accessing same in web
  ```
  I am using google cloud for this demo, you can use your local machine for this use-case. As I have used google cloud, I will be accessing prometheus and grafana on EXTERNAL_IP port
  Prometheus --> http://<EXTERNAL_IP>:9090/
  Grafana --> http://<EXTERNAL_IP>:3000/
  ```



 

