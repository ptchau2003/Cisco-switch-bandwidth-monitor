# bandwidthmonitor
Using Grafana/Prometheus/snmpexporter to monitor CPU/bandwidth of Cisco interfaces
1. Download Premetheus 
https://prometheus.io/download/#prometheus

root@chauphan-virtual-machine:~# wget https://github.com/prometheus/prometheus/releases/download/v2.15.2/prometheus-2.15.2.linux-amd64.tar.gz
--2020-01-10 10:01:59--  https://github.com/prometheus/prometheus/releases/download/v2.15.2/prometheus-2.15.2.linux-amd64.tar.gz
Resolving github.com (github.com)... 192.30.253.112
Connecting to github.com (github.com)|192.30.253.112|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://github-production-release-asset-2e65be.s3.amazonaws.com/6838921/19fbe400-30df-11ea-9f8f-65d72355462b?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20200110%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20200110T080200Z&X-Amz-Expires=300&X-Amz-Signature=45b0df9fb14cc8ebbf2652788a4d316b0eb16d00f7a7b52e0954868c57063e08&X-Amz-SignedHeaders=host&actor_id=0&response-content-disposition=attachment%3B%20filename%3Dprometheus-2.15.2.linux-amd64.tar.gz&response-content-type=application%2Foctet-stream [following]
--2020-01-10 10:02:00--  https://github-production-release-asset-2e65be.s3.amazonaws.com/6838921/19fbe400-30df-11ea-9f8f-65d72355462b?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20200110%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20200110T080200Z&X-Amz-Expires=300&X-Amz-Signature=45b0df9fb14cc8ebbf2652788a4d316b0eb16d00f7a7b52e0954868c57063e08&X-Amz-SignedHeaders=host&actor_id=0&response-content-disposition=attachment%3B%20filename%3Dprometheus-2.15.2.linux-amd64.tar.gz&response-content-type=application%2Foctet-stream
Resolving github-production-release-asset-2e65be.s3.amazonaws.com (github-production-release-asset-2e65be.s3.amazonaws.com)... 52.216.184.107
Connecting to github-production-release-asset-2e65be.s3.amazonaws.com (github-production-release-asset-2e65be.s3.amazonaws.com)|52.216.184.107|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 59204993 (56M) [application/octet-stream]
Saving to: ‘prometheus-2.15.2.linux-amd64.tar.gz’

prometheus-2.15.2.linux-amd64.tar.gz       100%[========================================================================================>]  56.46M   737KB/s    in 42s

2020-01-10 10:02:43 (1.34 MB/s) - ‘prometheus-2.15.2.linux-amd64.tar.gz’ saved [59204993/59204993]

root@chauphan-virtual-machine:~#
