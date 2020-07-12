---
title: 設定 Azure API 管理自我裝載閘道的本機計量和記錄 |Microsoft Docs
description: 瞭解如何設定 Azure API 管理自我裝載閘道的本機計量和記錄
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/30/2020
ms.author: apimpm
ms.openlocfilehash: ac147863fe54be3343eda653fc863ebd08dac54d
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86254498"
---
# <a name="configure-local-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>設定 Azure API 管理自我裝載閘道的本機計量和記錄

本文提供設定[自我裝載閘道](./self-hosted-gateway-overview.md)之本機計量和記錄的詳細資料。 如需設定雲端計量和記錄，請參閱[這篇文章](how-to-configure-cloud-metrics-logs.md)。 

## <a name="metrics"></a>計量
自我裝載閘道支援[StatsD](https://github.com/statsd/statsd)，其已成為計量集合和匯總的統一通訊協定。 本節將逐步引導您將 StatsD 部署至 Kubernetes、設定閘道透過 StatsD 發出計量，以及使用[Prometheus](https://prometheus.io/)來監視計量。 

### <a name="deploy-statsd-and-prometheus-to-the-cluster"></a>將 StatsD 和 Prometheus 部署到叢集

以下是將 StatsD 和 Prometheus 部署至部署自我裝載閘道之 Kubernetes 叢集的範例 YAML 設定。 它也會為每個建立[服務](https://kubernetes.io/docs/concepts/services-networking/service/)。 自我裝載閘道會將計量發佈至 StatsD 服務。 我們將透過其服務存取 Prometheus 儀表板。   

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: sputnik-metrics-config
data:
  statsd.yaml: ""
  prometheus.yaml: |
    global:
      scrape_interval:     3s
      evaluation_interval: 3s
    scrape_configs:
      - job_name: 'prometheus'
        static_configs:
          - targets: ['localhost:9090']
      - job_name: 'test_metrics'
        static_configs:
          - targets: ['localhost:9102']
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sputnik-metrics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sputnik-metrics
  template:
    metadata:
      labels:
        app: sputnik-metrics
    spec:
      containers:
      - name: sputnik-metrics-statsd
        image: prom/statsd-exporter
        ports:
        - name: tcp
          containerPort: 9102
        - name: udp
          containerPort: 8125
          protocol: UDP
        args:
          - --statsd.mapping-config=/tmp/statsd.yaml
          - --statsd.listen-udp=:8125
          - --web.listen-address=:9102
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      - name: sputnik-metrics-prometheus
        image: prom/prometheus
        ports:
        - name: tcp
          containerPort: 9090
        args:
          - --config.file=/tmp/prometheus.yaml
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      volumes:
        - name: sputnik-metrics-config-files
          configMap:
            name: sputnik-metrics-config
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-statsd
spec:
  type: NodePort
  ports:
  - name: udp
    port: 8125
    targetPort: 8125
    protocol: UDP
  selector:
    app: sputnik-metrics
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-prometheus
spec:
  type: LoadBalancer
  ports:
  - name: http
    port: 9090
    targetPort: 9090
  selector:
    app: sputnik-metrics
```

將設定儲存至名為的檔案 `metrics.yaml` ，並使用下列命令將所有專案部署至叢集：

```console
kubectl apply -f metrics.yaml
```

部署完成後，請執行下列命令以檢查 pod 是否正在執行。 請注意，您的 pod 名稱會不同。 

```console
kubectl get pods
NAME                                   READY   STATUS    RESTARTS   AGE
sputnik-metrics-f6d97548f-4xnb7        2/2     Running   0          1m
```

執行下列命令，以檢查服務是否正在執行。 記下 `CLUSTER-IP` StatsD 服務的和 `PORT` ，稍後我們將會用到它。 您可以使用和來流覽 Prometheus 儀錶 `EXTERNAL-IP` 板 `PORT` 。

```console
kubectl get services
NAME                         TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE
sputnik-metrics-prometheus   LoadBalancer   10.0.252.72   13.89.141.90    9090:32663/TCP               18h
sputnik-metrics-statsd       NodePort       10.0.41.179   <none>          8125:32733/UDP               18h
```

### <a name="configure-the-self-hosted-gateway-to-emit-metrics"></a>設定自我裝載閘道以發出計量

現在已部署 StatsD 和 Prometheus，我們可以更新自我裝載閘道的設定，以開始透過 StatsD 發出計量。 您可以在自我裝載閘道部署的 ConfigMap 中，使用金鑰來啟用或停用此功能 `telemetry.metrics.local` ，並提供其他選項。 以下是可用選項的明細：

| 欄位  | 預設 | 描述 |
| ------------- | ------------- | ------------- |
| 遙測資料。本機  | `none` | 透過 StatsD 啟用記錄。 值可以是 `none` 、 `statsd` 。 |
| statsd 端點的值  | n/a | 指定 StatsD 端點。 |
| ，statsd。取樣  | n/a | 指定計量取樣率。 值可以介於0到1之間。 例如`0.5`|
| statsd。標記-格式  | n/a | StatsD 匯出程式[標記格式](https://github.com/prometheus/statsd_exporter#tagging-extensions)。 值可以是 `none` 、 `librato` 、 `dogStatsD` 、 `influxDB` 。 |

以下是範例設定：

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.metrics.local: "statsd"
        telemetry.metrics.local.statsd.endpoint: "10.0.41.179:8125"
        telemetry.metrics.local.statsd.sampling: "1"
        telemetry.metrics.local.statsd.tag-format: "dogStatsD"
```

使用上述設定更新自我裝載閘道部署的 YAML 檔案，並使用下列命令套用變更： 

```console
kubectl apply -f <file-name>.yaml
 ```

若要選取最新的設定變更，請使用下列命令重新開機閘道部署：

```console
kubectl rollout restart deployment/<deployment-name>
```

### <a name="view-the-metrics"></a>查看計量

現在我們已部署並設定所有專案，自我裝載閘道應該會透過 StatsD 報告計量。 Prometheus 會從 StatsD 中挑選計量。 使用 `EXTERNAL-IP` Prometheus 服務的和，移至 Prometheus 儀表板 `PORT` 。 

透過自我裝載閘道進行一些 API 呼叫，如果所有專案都已正確設定，您應該能夠查看下列計量：

| 計量  | 描述 |
| ------------- | ------------- |
| Requests  | 期間內的 API 要求數目 |
| DurationInMS | 從閘道收到要求直到傳入完整回應時的毫秒數 |
| BackendDurationInMS | 整體後端 IO (連線、傳送及接收位元組) 所耗費的毫秒數  |
| ClientDurationInMS | 整體用戶端 IO (連線、傳送及接收位元組) 所耗費的毫秒數  |

## <a name="logs"></a>記錄

自我裝載閘道預設會將記錄輸出到 `stdout` 和 `stderr` 。 您可以使用下列命令輕鬆地查看記錄：

```console
kubectl logs <pod-name>
```

如果您的自我裝載閘道是部署在 Azure Kubernetes Service 中，您可以讓[容器 Azure 監視器](../azure-monitor/insights/container-insights-overview.md)收集 `stdout` `stderr` 工作負載，並在 Log Analytics 中查看記錄。 

自我裝載閘道也支援數種通訊協定，包括 `localsyslog` 、 `rfc5424` 和 `journal` 。 下表摘要說明所有支援的選項。 

| 欄位  | 預設 | 描述 |
| ------------- | ------------- | ------------- |
| 遙測. std  | `text` | 啟用記錄至標準資料流程。 值可以是 `none` 、 `text` 、`json` |
| 遙測. 本機  | `none` | 啟用本機記錄。 值可以是 `none` 、 `auto` 、 `localsyslog` 、 `rfc5424` 、`journal`  |
| localsyslog. endpoint。  | n/a | 指定 localsyslog 端點。  |
| localsyslog. 的功能  | n/a | 指定 localsyslog[設施程式碼](https://en.wikipedia.org/wiki/Syslog#Facility)。 例如`7` 
| rfc5424. endpoint。  | n/a | 指定 rfc5424 端點。  |
| rfc5424. 的功能  | n/a | 指定每個[rfc5424](https://tools.ietf.org/html/rfc5424)的設施代碼。 例如`7`  |
| [遙測]。  | n/a | 指定日誌端點。  |

以下是本機記錄的範例設定：

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.logs.std: "text"
        telemetry.logs.local.localsyslog.endpoint: "/dev/log"
        telemetry.logs.local.localsyslog.facility: "7"
```
 
## <a name="next-steps"></a>後續步驟

* 若要深入瞭解自我裝載閘道，請參閱[AZURE API 管理自我裝載閘道總覽](self-hosted-gateway-overview.md)
* 瞭解如何[在雲端中設定和保存記錄](how-to-configure-local-metrics-logs.md)
