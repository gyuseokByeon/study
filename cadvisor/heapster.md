Overview
========

- heapster는 resource usage metrics를 계산하기 위해 cAdvisor를 사용해 container cluster 모니터링을 가능하게 함
- kube 와 coreos 지원 (확대할 예정)

Running Heapster on Kubernetes
==============================

- pluggable storage backend 지원 (influxdb with Grafana, Google Cloud Monitoring, Google Cloud Logging)

Metrics
========

| Metric Name        | Description                                                                                        | Type       | Units       | Supported Since |
|--------------------|----------------------------------------------------------------------------------------------------|------------|-------------|-----------------|
| uptime             | Number of millisecond since the container was started                                             | Cumulative | Milliseconds | v0.9            |
| cpu/usage          | Cumulative CPU usage on all cores                                                                  | Cumulative | Nanoseconds       | v0.9            |
| memory/usage       | Total memory usage                                                                                 | Gauge      | Bytes       | v0.9            |
| memory/page_faults | Number of major page faults                                                                        | Cumulative      | Count       | v0.9            |
| memory/working_set | Total working set usage. Working set is the memory being used and not easily dropped by the Kernel | Gauge      | Bytes       | v0.9            |
| network/rx         | Cumulative number of bytes received over the network                                               | Cumulative | Bytes       | v0.9            |
| network/rx_errors  | Cumulative number of errors while receiving over the network                                       | Cumulative | Count       | v0.9            |
| network/tx         | Cumulative number of bytes sent over the network                                                   | Cumulative | Bytes       | v0.9            |
| network/tx_errors  | Cumulative number of errors while sending over the network                                         | Cumulative | Count       | v0.9            |
| filesystem/usage   | Total number of bytes used on a filesystem identified by label 'resource_id'                       | Gauge      | Bytes       | v0.11.0            |


Lables
=======

| Label Name     | Description                                                                   | Supported Since | Kubernetes specific |
|----------------|-------------------------------------------------------------------------------|-----------------|---------------------|
| pod_id         | Unique ID of a Pod                                                            | v0.9            | Yes                 |
| pod_name       | User-provided name of a Pod                                                   | HEAD            | Yes                 |
| pod_namespace  | The namespace of a Pod                                                        | v0.10           | Yes                 |
| container_name | User-provided name of the container or full cgroup name for system containers | v0.9            | No                  |
| labels         | Comma-separated list of user-provided labels. Format is 'key:value'           | v0.9            | Yes                 |
| hostname       | Hostname where the container ran                                              | v0.9            | No                  |
| resource_id    | An unique identifier used to differentiate multiple metrics of the same type. e.x. Fs partitions under filesystem/usage | v0.11.0 | No |

## Storage Schema

### InfluxDB

각 metric 은 influxDB의 series로 변경되고 Label은 칼럼이 추가되어 저장됩니다. 
series 명칭은 "metric name" + "units" + "type" 형태로 지정되며 units이 count일 경우 생략됩니다. 

```
cpu/usage_ns_cumulative
filesystem/usage
memory/page_faults_gauge
memory/usage_bytes_gauge
memory/working_set_bytes_gauge
network/rx_bytes_cumulative
network/rx_errors_cumulative
network/tx_bytes_cumulative
network/tx_errors_cumulative
uptime_ms_cumulative
```

## Running Heapster on Kubernetes

![heapster](https://github.com/leeplay/study/blob/master/etc/heapster-overview.png?raw=true)

- cadvisor -> kubelet -> heapster -> influxdb -> grafana

1. ![cadvisor.md](https://github.com/leeplay/study/blob/master/cadvisor/cAdvisor.md) 
2. ![kubernetes/pkg/kubelet/cadvisor/cadvisor_linux.go](https://github.com/GoogleCloudPlatform/kubernetes/blob/e1a153e841421c6ba9f9db774864ff92a1cf7dbc/pkg/kubelet/cadvisor/cadvisor_linux.go) cadvisor에 접근해 정보 수집
3. 