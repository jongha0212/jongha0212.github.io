---
title: "Wavefront & Prometheus & Filebeat"
date: 2022-04-20
categories: [Tanzu]
tags: [Tanzu, Tanzu Observability, Kubernetes, Wavefront, Prometheus, Filebeat]
description: Wavefront & Prometheus & Filebeat
toc: true
published: true
---

# Wavefront & Prometheus & Filebeat

고객사에서 Tanzu Observability를 활용하여 Kubernetes Cluster에 반영한 MSA 11개 apps 모니터링 요청.    

  * monitroing : tanzu observability
  * metric : prometheus
  * log : filebeat
  * sample app : MSA Sample Apps [Boutique](https://github.com/GoogleCloudPlatform/microservices-demo)

## Tanzu Observability & Wavefront

* TO에서 wavefront를 integration하여 k8s cluster에 설치할 수 있음.
* filebeat 연동을 해야하나, k8s에 설치되는 wavefront pod에는 filebeat 설정을 추가할 수 없음.
    * 자세한 설정에 대해서는 to팀 확인 필요.
* 따라서, wavefront는 vm에 설치

### Install Wavefront

* Wavefront 설치 방법은 다음과 같다.
* TMC에서 Intergration하는 방식은 제외.

#### Kubernetes Cluster

1. Helm Repo Add

    ```console
    $ helm repo add wavefront https://wavefronthq.github.io/helm/ && helm repo update
    ```
1. Install

  1. helm chart를 받아서 values.yaml을 수정
    ```console
    $ helm pull wavefront/wavefront
    ```
    * pull 받은 **wavefront-1.10.1.tgz** 파일 압축 해제 후 values.yaml 수정

        ```console
        $ kubectl create ns wavefront
        $ helm install wavefront -n wavefront -f ./values.yaml .
        ```
  1. online 설치

    ```console
    kubectl create namespace wavefront && helm install wavefront wavefront/wavefront \
      --set wavefront.url=https://<wavefront url> \
      --set wavefront.token=<wavefront token> \
      --set clusterName=<cluster name> --namespace wavefront \
      --set proxy.traceJaegerApplicationName="Boutique" \
      --set proxy.jaegerPort=30001 \
      --set proxy.replicas=3 \
      --set proxy.traceJaegerHttpListenerPort=30080 \
      --set proxy.replicas=3
    ```

#### VM or Baremetal

ubuntu 20.04 기준으로 작성되었으며, apt를 사용하여 설치.

1. Install

    ```console
    $ curl -s https://packagecloud.io/install/repositories/wavefront/proxy/script.deb.sh | sudo bash
    $ sudo apt-get install wavefront-proxy=11.0-1
    ```
    * 참고 : [wavefront](https://packagecloud.io/wavefront/proxy)
    * 설치하면 /etc/wavefront에 **wavefront-proxy** 디렉토리가 생성됨.

1. wavefront.conf 설정

  ```console
  $ vi /etc/wavefront/wavefront-proxy/wavefront.conf
  ...
  server=https://<wavefront url>/api/
  hostname=<hostname or kubernetes cluster name>
  token=<wavefront token>
  ...
  ...

  ###### LOGS TO METRICS SETTINGS #####
  filebeatPort=5044
  rawLogsPort=5045
  rawLogsMaxReceivedLength=4096
  rawLogsHttpBufferSize=16777216
  logsIngestionConfigFile=/etc/wavefront/wavefront-proxy/logsingestion.yaml
  ...
  ```
  * filebeat 연동을 위해 관련된 설정을 수정.

1. filebeat 연동을 위해 logingestion.yaml 생성

  ```yaml
  counters:
    - pattern: 'quantity=%{NUMBER:count}'
      valueLabel: 'count'
      metricName: 'cartService'
    - pattern: '%{IPORHOST:[clientip]} -  \[%{HTTPDATE:[timestamp]}\] \"%{WORD:[method]} %{DATA:[request]} HTTP/%{NUMBER:[http_version]}\" %{NUMBER:[response]} (?:%{NUMBER:[bytes]}|-)( \"%{DATA:[referrer]}\")( \"%{DATA:[agent]}\")'
      metricName: 'nginx_log_lines'
  gauges:
    - pattern: '%{IPORHOST:[clientip]} -  \[%{HTTPDATE:[timestamp]}\] \"%{WORD:[method]} %{DATA:[request]} HTTP/%{NUMBER:[http_version]}\" %{NUMBER:[response]} (?:%{NUMBER:[bytes]}|-)( \"%{DATA:[referrer]}\")( \"%{DATA:[agent]}\")'
      valueLabel: '[response]'
      metricName: 'nginxStatus'
    - pattern: 'quantity=%{NUMBER:count}'
      valueLabel: 'count'
      metricName: 'cartServiceGauge'
  histograms:
    - pattern: '%{IPORHOST:[clientip]} -  \[%{HTTPDATE:[timestamp]}\] \"%{WORD:[method]} %{DATA:[request]} HTTP/%{NUMBER:[http_version]}\" %{NUMBER:[response]} (?:%{NUMBER:[bytes]}|-)( \"%{DATA:[referrer]}\")( \"%{DATA:[agent]}\")'
      metricName: 'responseBytes'
      valueLabel: '[bytes]'
    - pattern: 'quantity=%{NUMBER:count}'
      valueLabel: 'count'
      metricName: 'cartServiceHistogram'
  ```
  * nginx ingress와 boutique 정보를 가져오기 위해 2종류의 pattern 설정

1. wavefront start & stop

    ```console
    $ service wavefront-proxy start
    $ tail -f /var/log/wavefront/wavefront.log
    2022-04-20 14:36:03,240 INFO  [proxy:parseArguments] Wavefront Proxy version 11.0 (pkg:linux_rpm_deb), runtime: OpenJDK Runtime Environment (Azul Systems, Inc.) 11.0.9
    2022-04-20 14:36:03,245 INFO  [proxy:parseArguments] Arguments: -f /etc/wavefront/wavefront-proxy/wavefront.conf
    2022-04-20 14:36:03,264 INFO  [ProxyConfig:verifyAndInit] Loaded configuration file /etc/wavefront/wavefront-proxy/wavefront.conf
    2022-04-20 14:36:03,280 INFO  [proxy:getOrCreateProxyId] Ephemeral proxy id created: e5f9a97a-df4d-48fe-ba59-b8d4dae1d6be
    2022-04-20 14:36:03,672 INFO  [proxy:checkin] Checking in tenants: https://sentosa.wavefront.com/api/
    2022-04-20 14:36:04,530 INFO  [proxy:<init>] initial configuration is available, setting up proxy
    2022-04-20 14:36:04,636 INFO  [proxy:scheduleCheckins] scheduling regular check-ins
    2022-04-20 14:36:04,839 INFO  [proxy:lambda$startListeners$7] listening on port: 2878 for Wavefront metrics
    2022-04-20 14:36:04,869 INFO  [proxy:startJsonListener] listening on port: 3878 for JSON metrics data
    2022-04-20 14:36:04,898 INFO  [proxy:startWriteHttpJsonListener] listening on port: 4878 for write_http data
    2022-04-20 14:36:04,934 INFO  [proxy:startListeners] Initializing logs ingestion
    2022-04-20 14:36:05,061 INFO  [proxy:startLogsIngestionListener] listening on port: 5044 for Filebeat logs
    2022-04-20 14:36:05,065 INFO  [proxy:startRawLogsIngestionListener] listening on port: 5045 for raw logs
    2022-04-20 14:36:05,071 INFO  [Server:listen] Starting server on port: 5044
    2022-04-20 14:36:08,051 INFO  [LogsIngester:onNewConnection] New filebeat connection.
    2022-04-20 14:36:08,057 INFO  [LogsIngester:onNewConnection] New filebeat connection.
    2022-04-20 14:36:08,058 INFO  [LogsIngester:onNewConnection] New filebeat connection.
    2022-04-20 14:36:08,060 INFO  [LogsIngester:onNewConnection] New filebeat connection.
    2022-04-20 14:36:08,081 INFO  [LogsIngester:onConnectionClose] Filebeat connection closed.
    2022-04-20 14:36:08,081 INFO  [LogsIngester:onConnectionClose] Filebeat connection closed.
    2022-04-20 14:36:08,081 INFO  [LogsIngester:onConnectionClose] Filebeat connection closed.
    2022-04-20 14:36:08,083 INFO  [LogsIngester:onConnectionClose] Filebeat connection closed.
    ...
    ...

    $ service wavefront-proxy stop
    $ tail -f /var/log/wavefront/wavefront.log
    2022-04-20 14:34:43,401 INFO  [AbstractReportableEntityHandler:printStats] [2878] Points received rate: 0 pps (1 min), 0 pps (5 min), 0 pps (current).
    2022-04-20 14:34:43,461 INFO  [AbstractReportableEntityHandler:printStats] [3878] Points received rate: 0 pps (1 min), 0 pps (5 min), 0 pps (current).
    2022-04-20 14:34:43,482 INFO  [AbstractReportableEntityHandler:printStats] [4878] Points received rate: 0 pps (1 min), 0 pps (5 min), 0 pps (current).
    2022-04-20 14:34:43,668 INFO  [AbstractReportableEntityHandler:printTotal] [logs-ingester] Points processed since start: 0; blocked: 0
    2022-04-20 14:34:43,668 INFO  [AbstractReportableEntityHandler:printStats] [logs-ingester] Points received rate: 0 pps (1 min), 0 pps (5 min), 0 pps (current).
    2022-04-20 14:34:47,947 INFO  [LogsIngester:onNewConnection] New filebeat connection.
    2022-04-20 14:34:47,947 INFO  [LogsIngester:onNewConnection] New filebeat connection.
    2022-04-20 14:34:47,947 INFO  [LogsIngester:onNewConnection] New filebeat connection.
    2022-04-20 14:34:47,948 INFO  [LogsIngester:onNewConnection] New filebeat connection.
    2022-04-20 14:34:47,951 INFO  [LogsIngester:onConnectionClose] Filebeat connection closed.
    2022-04-20 14:34:47,951 INFO  [LogsIngester:onConnectionClose] Filebeat connection closed.
    2022-04-20 14:34:47,952 INFO  [LogsIngester:onConnectionClose] Filebeat connection closed.
    2022-04-20 14:34:47,953 INFO  [LogsIngester:onConnectionClose] Filebeat connection closed.
    2022-04-20 14:34:52,992 INFO  [proxy:shutdown] Shutting down the proxy...
    2022-04-20 14:34:53,027 INFO  [TcpIngester:run] Listener on port 4878 shut down
    2022-04-20 14:34:53,037 INFO  [TcpIngester:run] Listener on port 5045 shut down
    2022-04-20 14:34:53,044 INFO  [TcpIngester:run] Listener on port 2878 shut down
    2022-04-20 14:34:53,072 INFO  [TcpIngester:run] Listener on port 3878 shut down
    2022-04-20 14:34:53,101 INFO  [LogsIngester:onConnectionClose] Filebeat connection closed.
    2022-04-20 14:34:53,106 INFO  [LogsIngester:onConnectionClose] Filebeat connection closed.
    2022-04-20 14:34:55,217 INFO  [proxy:lambda$startLogsIngestionListener$57] Filebeat server on port 5044 shut down
    ...
    ...
    ```

## Filebeat
