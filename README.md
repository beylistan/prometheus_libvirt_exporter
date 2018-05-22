# Prometheus libvirt exporter

Prometheus libvirt exporter is tool for scraping metrics from libvirt daemon and exposing those metric in prometheus format.
This exporter connects to any libvirt daemon and exports per-domain metrics related to CPU, memory, disk and network usage.
Metrics are exposing on port 9177

List of metrics/labels are being exported:
```
libvirt_cpu_stats_system_time_nanosecs{domain=""}
libvirt_cpu_stats_user_time_nanosecs{domain=""}
libvirt_cpu_stats_cpu_time_nanosecs{domain=""}

libvirt_mem_stats_rss{domain=""}
libvirt_mem_stats_swap_in{domain=""}
libvirt_mem_stats_unused{domain=""}
libvirt_mem_stats_minor_fault{domain=""}
libvirt_mem_stats_last_update{domain=""}
libvirt_mem_stats_actual{domain=""}
libvirt_mem_stats_major_fault{domain=""}
libvirt_mem_stats_available{domain=""}

libvirt_block_stats_errors_number{domain="",target_device=""}
libvirt_block_stats_write_requests_issued{domain="",target_device=""}
libvirt_block_stats_write_bytes{domain="",target_device=""}
libvirt_block_stats_read_bytes{domain="",target_device=""}
libvirt_block_stats_read_requests_issued{domain="",target_device=""}

libvirt_interface_write_errors{domain="",target_device=""}
libvirt_interface_write_bytes{domain="",target_device=""}
libvirt_interface_write_packets{domain="",target_device=""}
libvirt_interface_write_drops{domain="-",target_device=""}

libvirt_interface_read_packets{domain="",target_device=""}
libvirt_interface_read_errors{domain="",target_device=""}
libvirt_interface_read_drops{domain="",target_device=""}
libvirt_interface_read_bytes{domain="-",target_device=""}

```

## Getting Started

These instructions will get you a copy of exporter and running on your local machine.

### Prerequisites

What things you need to install in case  

if you want to run exporter on host itself
```
python
libvirt-python==3.7.0
prometheus-client==0.0.21
```
if you want to run exporter in docker container
```
docker
docker-compose
```
### Installing

if you want to run exporter on host itself
```
git clone https://github.com/beylistan/prometheus_libvirt_exporter.git
pip install -r requirements.txt
```
if you want to run exporter in docker container
```
docker pull beylistan/prometheus_libvirt_exporter
```
### Running
if you want to run exporter on host itself
```
python libvirt_exporter.py -h
usage: libvirt_exporter.py [-h] [-si SCRAPE_INTERVAL]
                           [-uri UNIFORM_RESOURCE_IDENTIFIER]

libvirt_exporter scrapes domains metrics from libvirt daemon

optional arguments:
  -h, --help            show this help message and exit
  -si SCRAPE_INTERVAL, --scrape_interval SCRAPE_INTERVAL
                        scrape interval for metrics in seconds
  -uri UNIFORM_RESOURCE_IDENTIFIER, --uniform_resource_identifier UNIFORM_RESOURCE_IDENTIFIER
                        Libvirt Uniform Resource Identifier

```
if you want to run exporter in docker container
```
docker run --privileged -dp 9177:9177 \
    -v /var/run/libvirt/libvirt-sock:/var/run/libvirt/libvirt-sock \
    --name libvirt_exporter beylistan/libvirt_exporter \
    python3 libvirt_exporter.py [-si SCRAPE_INTERVAL] [-uri UNIFORM_RESOURCE_IDENTIFIER]
```

### Debian sysvinit install

Install libraries:

```
apt install python3-prometheus-client python3-libvirt
```

clone repo:

```
git clone https://github.com/beylistan/prometheus_libvirt_exporter.git
```

use the included `init/prometheus_libvirt_explorer`

```
cp init/prometheus_libvirt_explorer /etc/init.d/
chmod +x /etc/init.d/prometheus_libvirt_explorer
update-rc.d prometheus_libvirt_explorer defaults
```

start:

```
/etc/init.d/prometheus_libvirt_explorer start
```

test if running:

```
lsof -i :9177
```

connect to prometheus, edit `/etc/prometheus/prometheus.yml` and add:
```
  - job_name: libvirt
   # this collects libvirt stats from python3 libvirt_exporter.py 
    static_configs:
      - targets: ['localhost:9177']
```
