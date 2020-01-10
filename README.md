# Cisco/Juniper bandwidth monitor
Using Grafana/Prometheus/snmpexporter to monitor CPU/bandwidth of Cisco interfaces
## Download and install Prometheus  
```
wget https://github.com/prometheus/prometheus/releases/download/v2.15.2/prometheus-2.15.2.linux-amd64.tar.gz
```
## Untar the gz file
```
mkdir prometheus
tar -xvf prometheus-2.15.2.linux-amd64.tar.gz
```
## Download snmpexporter
```
wget https://github.com/prometheus/snmp_exporter/releases/download/v0.16.1/snmp_exporter-0.16.1.linux-amd64.tar.gz
```
## Untar the snmpexporter file
```
tar -xvf snmp_exporter-0.16.1.linux-amd64.tar.gz
```
