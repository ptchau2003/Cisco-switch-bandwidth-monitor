# bandwidthmonitor
Using Grafana/Prometheus/snmpexporter to monitor CPU/bandwidth of Cisco interfaces
1.  Download and install Premetheus From https://prometheus.io/download/#prometheus

root@chauphan-virtual-machine:~# wget https://github.com/prometheus/prometheus/releases/download/v2.15.2/prometheus-2.15.2.linux-amd64.tar.gz
--2020-01-10 10:01:59--  https://github.com/prometheus/prometheus/releases/download/v2.15.2/prometheus-2.15.2.linux-amd64.tar.gz
Resolving github.com (github.com)... 192.30.253.112
Connecting to github.com (github.com)|192.30.253.112|:443... connected.
HTTP request sent, awaiting response... 302 Found

Connecting to github-production-release-asset-2e65be.s3.amazonaws.com (github-production-release-asset-2e65be.s3.amazonaws.com)|52.216.184.107|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 59204993 (56M) [application/octet-stream]
Saving to: ‘prometheus-2.15.2.linux-amd64.tar.gz’

prometheus-2.15.2.linux-amd64.tar.gz       100%[========================================================================================>]  56.46M   737KB/s    in 42s

2020-01-10 10:02:43 (1.34 MB/s) - ‘prometheus-2.15.2.linux-amd64.tar.gz’ saved [59204993/59204993]

root@chauphan-virtual-machine:~#
2. Untar the gz file
