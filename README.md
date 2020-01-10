# Cisco/Juniper bandwidth monitor
Using Grafana/Prometheus/snmpexporter to monitor CPU/bandwidth of Cisco interfaces

The SNMP exporter will collect the SNMP information from Cisco/Juniper switch by **pull** method and store in Prometheus

The Grafana will dislay the information connecting to Prometheus

## Download and install Prometheus  
```
wget https://github.com/prometheus/prometheus/releases/download/v2.15.2/prometheus-2.15.2.linux-amd64.tar.gz
```
## Untar the gz file
```
mkdir /home/prometheus
tar -xvf prometheus-2.15.2.linux-amd64.tar.gz
```
## Download snmpexporter
```
wget https://github.com/prometheus/snmp_exporter/releases/download/v0.16.1/snmp_exporter-0.16.1.linux-amd64.tar.gz
```
## Untar the snmpexporter file
```
mkdir /home/prometheus/snmp_exporter
tar -xvf snmp_exporter-0.16.1.linux-amd64.tar.gz
```
## Configure the prometheus.yml file
We use 02 jobs: one snmp for Cisco switch and snmpj for Juniper one.
snmp will use module ciscosw (SNMP exporter) snmpj use junipersw (SNMP expoter module)

The **- targets:** describes the host list of the monitor switches in your local /etc/hosts

**replacement locahost:9116** is the IP and port you will connect by http. You can replace localhost by your dedicated IP address

```
# Global config
global:
  scrape_interval:     240s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 240s # Evaluate rules every 15 seconds. The default is every 1 minute.
  scrape_timeout: 120s  # scrape_timeout is set to the global default (10s).

# A scrape configuration containing exactly one endpoint to scrape:# Here it's Prometheus itself.
scrape_configs:
  - job_name: 'snmp'
    static_configs:
    - targets: ['cisco1']
    - targets: ['cisco2']
    metrics_path: /snmp
    params:
      module: ['ciscosw']
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: localhost:9116
  - job_name: 'snmpj'
    static_configs:
    - targets: ['juniper1']
    metrics_path: /snmp
    params:
      module: ['junipersw']
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: localhost:9116
```
## Run prometheus
```
./prometheus --config.file prometheus.yml &
```
## Configure the snmp.yml file
Add the ciscosw and junipersw part for Input and Output and CPU bandwidth monitor. For the others, keep it for the same. They are SNMP walk for other products.

Please note that you have to create a SNMP community at Cisco/Juniper switch. As below the community added is **audit**

Cisco uses SNMP OID 1.3.6.1.2.1.31.1.1.1.6 to return a Input bandwidth in octets of an interface. Meanwhile, Juniper uses SNMP OID 1.3.6.1.2.1.2.2.1.10 to return a Input bandwidth in octets of an interface

```
ciscosw:
  version: 2
  auth:
    community: audit
  walk:
  - 1.3.6.1.2.1.2.2.1
  - 1.3.6.1.2.1.2.2.2
  - 1.3.6.1.2.1.2
  - 1.3.6.1.2.1.31.1
  - 1.3.6.1.2.1.31.1.1.1
  - 1.3.6.1.4.1.9.9.109.1.1.1.1
  metrics:
  - name: ifIndex
    oid: 1.3.6.1.2.1.2.2.1.1
    type: gauge
    help: A unique value, greater than zero, for each interface - 1.3.6.1.2.1.2.2.1.1
    indexes:
    - labelname: ifIndex
      type: gauge
  - name: ifDescr
    oid: 1.3.6.1.2.1.2.2.1.2
    type: DisplayString
    help: A textual string containing information about the interface - 1.3.6.1.2.1.2.2.1.2
    indexes:
    - labelname: ifIndex
      type: gauge

  - name: sysUpTime
    oid: 1.3.6.1.2.1.1.3
    type: gauge

  - name: BWInOctets
    oid: 1.3.6.1.2.1.31.1.1.1.6
    type: gauge
    indexes:
    - labelname: interface
      type: gauge

    lookups:
       - labels: [interface]
         oid: 1.3.6.1.2.1.2.2.1.2
         labelname: interface
         type: DisplayString
       - labels: [interface]
         oid: 1.3.6.1.2.1.31.1.1.1.18
         labelname: alias
         type: DisplayString

  - name: BWOutOctets
    oid: 1.3.6.1.2.1.31.1.1.1.10
    type: gauge
    indexes:
    - labelname: interface
      type: gauge

    lookups:
       - labels: [interface]
         oid: 1.3.6.1.2.1.2.2.1.2
         labelname: interface
         type: DisplayString
       - labels: [interface]
         oid: 1.3.6.1.2.1.31.1.1.1.18
         labelname: alias
         type: DisplayString
  - name: ifAlias
    oid: 1.3.6.1.2.1.31.1.1.1.18
    type: DisplayString
    help: This object is an 'alias' name for the interface as specified by a network
      manager, and provides a non-volatile 'handle' for the interface - 1.3.6.1.2.1.31.1.1.1.18
    indexes:
    - labelname: ifIndex
      type: gauge
  - name: cpmCPUTotal
    oid: 1.3.6.1.4.1.9.9.109.1.1.1.1.4.1
    type: gauge
    help: The overall CPU busy percentage
junipersw:
  version: 2
  auth:
    community: audit
  max_repetitions: 1  # How many objects to request with GET/GETBULK, defaults to 25.
                         # May need to be reduced for buggy devices.
  retries: 5   # How many times to retry a failed request, defaults to 3.
  timeout: 240s # Timeout for each walk, defaults to 10s.
  walk:
  - 1.3.6.1.2.1.2.2.1.1
  - 1.3.6.1.2.1.2.2.1.2
  - 1.3.6.1.2.1.2.2.1.10
  - 1.3.6.1.2.1.2.2.1.16
  - 1.3.6.1.2.1.31.1.1.1.18
  - 1.3.6.1.4.1.2636.3.1.13.1.8
  metrics:
  - name: ifIndex
    oid: 1.3.6.1.2.1.2.2.1.1
    type: gauge
    help: A unique value, greater than zero, for each interface - 1.3.6.1.2.1.2.2.1.1
    indexes:
    - labelname: ifIndex
      type: gauge
  - name: ifDescr
    oid: 1.3.6.1.2.1.2.2.1.2
    type: DisplayString
    help: A textual string containing information about the interface - 1.3.6.1.2.1.2.2.1.2
    indexes:
    - labelname: ifIndex
      type: gauge

  - name: JBWInOctets
    oid: 1.3.6.1.2.1.2.2.1.10
    type: gauge
    indexes:
    - labelname: interface
      type: gauge

    lookups:
       - labels: [interface]
         oid: 1.3.6.1.2.1.2.2.1.2
         labelname: interface
         type: DisplayString
       - labels: [interface]
         oid: 1.3.6.1.2.1.31.1.1.1.18
         labelname: alias
         type: DisplayString
  - name: JBWOutOctets
    oid: 1.3.6.1.2.1.2.2.1.16
    type: gauge
    indexes:
    - labelname: interface
      type: gauge

    lookups:
       - labels: [interface]
         oid: 1.3.6.1.2.1.2.2.1.2
         labelname: interface
         type: DisplayString
       - labels: [interface]
         oid: 1.3.6.1.2.1.31.1.1.1.18
         labelname: alias
         type: DisplayString
  - name: ifAlias
    oid: 1.3.6.1.2.1.31.1.1.1.18
    type: DisplayString
    help: This object is an 'alias' name for the interface as specified by a network
      manager, and provides a non-volatile 'handle' for the interface - 1.3.6.1.2.1.31.1.1.1.18
    indexes:
    - labelname: ifIndex
      type: gauge
  - name: cpmCPUTotal
    oid: 1.3.6.1.4.1.2636.3.1.13.1.8.9.1.0.0
    type: gauge
    help: The overall CPU busy percentage
```

## Run snmp exporter

```
./snmp_exporter &
```
## Verify your SNMP connection to Cisco/Juniper switches
The command below will return the InputBandwith in octets of each interface

**snmpbulkwalk -v 2c -c audit -On cisco1 1.3.6.1.2.1.31.1.1.1.6**

```
snmpbulkwalk -v 2c -c audit -On cisco1 1.3.6.1.2.1.31.1.1.1.6
.1.3.6.1.2.1.31.1.1.1.6.2 = Counter64: 221761800940631
.1.3.6.1.2.1.31.1.1.1.6.3 = Counter64: 240324462313013
.1.3.6.1.2.1.31.1.1.1.6.4 = Counter64: 201557378805616
.1.3.6.1.2.1.31.1.1.1.6.5 = Counter64: 177676708472093
.1.3.6.1.2.1.31.1.1.1.6.6 = Counter64: 0
.1.3.6.1.2.1.31.1.1.1.6.7 = Counter64: 0
.1.3.6.1.2.1.31.1.1.1.6.8 = Counter64: 0
.1.3.6.1.2.1.31.1.1.1.6.9 = Counter64: 44333199794
.1.3.6.1.2.1.31.1.1.1.6.10 = Counter64: 0
.1.3.6.1.2.1.31.1.1.1.6.11 = Counter64: 0
.1.3.6.1.2.1.31.1.1.1.6.12 = Counter64: 119898664568633
.1.3.6.1.2.1.31.1.1.1.6.13 = Counter64: 119780222545415
.1.3.6.1.2.1.31.1.1.1.6.14 = Counter64: 378268519621369
```
The command below will return the CPU of mentioned Cisco switches

**snmpbulkwalk -v 2c -c audit -On cisco1 1.3.6.1.4.1.9.9.109.1.1.1.1.4.1**
```
snmpbulkwalk -v 2c -c audit -On cisco1 1.3.6.1.4.1.9.9.109.1.1.1.1.4.1
.1.3.6.1.4.1.9.9.109.1.1.1.1.4.1 = Gauge32: 19 **
```
## Grafana download and install
```
wget https://dl.grafana.com/oss/release/grafana-6.0.0-beta2.x86_64.rpm
yum localinstall grafana-6.0.0-beta2.x86_64.rpm
```
## Grafana dashboard create and setting
### Add Prometheus source to Grafana
### Varibles setting
### Dashboard Setting
