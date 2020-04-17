---
title: 為容器為「普羅米圖斯集成」配置 Azure 監視器 |微軟文件
description: 本文介紹如何為容器代理配置 Azure 監視器,以便使用 Kubernetes 群集從 Prometheus 中刮取指標。
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 7fcf52cceb69834f68f8e4ce7a2674972a6430fd
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537367"
---
# <a name="configure-scraping-of-prometheus-metrics-with-azure-monitor-for-containers"></a>使用 Azure 監視器為容器配置普羅米杜斯指標的刮擦

[普羅米索斯](https://prometheus.io/)是一個流行的開源指標監測解決方案,是[雲原生計算基金會的](https://www.cncf.io/)一部分。 容器的 Azure 監視器提供了無縫的載入體驗,用於收集 Prometheus 指標。 通常,要使用 Prometheus,您需要使用儲存設定和管理 Prometheus 伺服器。 通過與 Azure 監視器集成,不需要普羅米蘇斯伺服器。 您只需透過匯出器或窗格(應用程式)公開 Prometheus 指標終結點,並且容器 Azure 監視器的容器化代理可以為您刮掉指標。 

![普羅米圖斯的容器監控架構](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>用於刮取 Prometheus 指標的最小代理版本為 ciprod07092019 或更高版本,表中用於寫入配置`KubeMonAgentEvents`和代理錯誤的 代理版本為 ciprod10112019。 有關代理版本和每個版本中包含的內容的詳細資訊,請參閱[代理發行說明](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)。 要驗證代理版本,請從 **『節點』** 選項卡中選擇一個節點,並在 **「代理圖像標記**」屬性的屬性窗格註釋值中選擇。

在以下位置託管的 Kubernetes 叢集支援擦除 Prometheus 指標:

- Azure Kubernetes Service (AKS)
- Azure 堆疊或本地
- Azure Red Hat OpenShift

>[!NOTE]
>對於 Azure 紅帽 OpenShift,在*開放移位-azure 日誌記錄*命名空間中創建範本 ConfigMap 檔。 它未配置為主動從代理刮削指標或數據收集。
>

## <a name="azure-red-hat-openshift-prerequisites"></a>Azure 紅帽開啟移位先決條件

在開始之前,請確認您是 Azure 紅帽 OpenShift 叢集的客戶群集管理員角色的成員,以配置容器化代理和 Prometheus 刮擦設置。 要驗證您是*osa-客戶管理員*群組的成員,請執行以下命令:

``` bash
  oc get groups
```

輸出看起來會像下面這樣：

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

如果您是*osa-客戶管理員*群組的成員,您應該能夠使用以下命令`container-azm-ms-agentconfig`列出 ConfigMap:

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

輸出看起來會像下面這樣：

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="prometheus-scraping-settings"></a>普羅米圖斯刮擦設定

從兩個角度之一對普羅米塞烏斯的指標進行主動刮擦:

* 叢集範圍 - HTTP URL,並從服務列出的終結點發現目標。 例如,k8s 服務(如 kube-dns 和 kube 狀態度量)以及特定於應用程式的 pod 註釋。 在此上下文中收集的指標將在 ConfigMap 部分 *[Prometheus data_collection_settings.群集]* 中定義。
* 節點範圍 - HTTP URL,並從服務列出的終結點發現目標。 在此上下文中收集的指標將在 ConfigMap 部分 *[Prometheus_data_collection_settings.node]* 中定義。

| 端點 | 影響範圍 | 範例 |
|----------|-------|---------|
| Pod 註解 | 叢集範圍 | 註解: <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Kubernetes 服務 | 叢集範圍 | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| url/終結點 | 每個節點與/或叢集範圍 | `http://myurl:9101/metrics` |

指定 URL 時,容器的 Azure 監視器僅刮擦終結點。 指定 Kubernetes 服務後,服務名稱將與群集 DNS 伺服器解析,以獲取 IP 位址,然後擦除已解決的服務。

|影響範圍 | Key | 資料類型 | 值 | 描述 |
|------|-----|-----------|-------|-------------|
| 叢集範圍 | | | | 指定以下三種方法中的任何一種,以刮取指標的終結點。 |
| | `urls` | String | 逗號分隔陣列 | HTTP 終結點(指定的 IP 位址或有效 URL 路徑)。 例如： `urls=[$NODE_IP/metrics]` 。 ($NODE_IP 是容器參數的特定 Azure 監視器,可以使用代替節點 IP 位址。 必須是所有大寫。 |
| | `kubernetes_services` | String | 逗號分隔陣列 | Kubernetes 服務陣列,用於從kube州度量中刮取指標。 例如，`kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics",http://my-service-dns.my-namespace:9100/metrics]`。|
| | `monitor_kubernetes_pods` | Boolean | true 或 false | 在群集範圍`true`設置中設置為「Azure 監視器」時,容器代理的 Azure 監視器將在整個群集中刮取 Kubernetes pod,以進行以下 Prometheus 註釋:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Boolean | true 或 false | 啟用刮架。 `monitor_kubernetes_pods` 必須設為 `true`。 |
| | `prometheus.io/scheme` | String | http 或 https | 預設在 HTTP 上報廢。 如有必要,將設定為`https`。 | 
| | `prometheus.io/path` | String | 逗號分隔陣列 | 要從其獲取指標的 HTTP 資源路徑。 如果指標路徑不是`/metrics`,請使用此註釋定義它。 |
| | `prometheus.io/port` | String | 9102 | 指定要刮離的埠。 如果未設置埠,它將預設為 9102。 |
| | `monitor_kubernetes_pods_namespaces` | String | 逗號分隔陣列 | 允許命名空間清單從 Kubernets 窗格中刮取指標。<br> 例如， `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| 節點範圍 | `urls` | String | 逗號分隔陣列 | HTTP 終結點(指定的 IP 位址或有效 URL 路徑)。 例如： `urls=[$NODE_IP/metrics]` 。 ($NODE_IP 是容器參數的特定 Azure 監視器,可以使用代替節點 IP 位址。 必須是所有大寫。 |
| 節點範圍或叢集範圍 | `interval` | String | 60 年 代 | 收集間隔預設值為1分鐘(60秒)。 您可以將 *[prometheus_data_collection_settings.node]* 和/或 *[prometheus_data_collection_settings.cluster]* 的集合修改為時間單位,如 s、m、h。 |
| 節點範圍或叢集範圍 | `fieldpass`<br> `fielddrop`| String | 逗號分隔陣列 | 可以通過設定允許`fieldpass`( )`fielddrop`和不允許 ( ) 清單來指定要從終結點收集或不從終結點收集的某些指標。 必須首先設置允許清單。 |

ConfigMap 是一個全域清單,只能向代理應用一個 ConfigMap。 您不能讓另一個 ConfigMap 覆蓋集合。

## <a name="configure-and-deploy-configmaps"></a>設定並部署設定對應

執行以下步驟,為庫伯奈斯群集配置 ConfigMap 設定檔。

1. [下載](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml)樣本 ConfigMap yaml 檔並將其另存為容器-azm-ms-代理 config.yaml。

   >[!NOTE]
   >使用 Azure 紅帽 OpenShift 時不需要此步驟,因為群集上已存在 ConfigMap 範本。

2. 使用自訂項編輯 ConfigMap yaml 檔案以刮取 Prometheus 指標。 如果要為 Azure 紅帽 OpenShift 編輯 ConfigMap yaml 檔,`oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`則首先執行命令以在文字編輯器中打開該檔。

    >[!NOTE]
    >必須在`openshift.io/reconcile-protect: "true"`*容器-azm-ms-代理 ConfigMap*的中繼資料下添加以下註釋,以防止對帳。 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - 要收集庫伯奈斯跨群集服務,請使用以下範例配置 ConfigMap 檔。

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - 要設定從群集中特定 URL 擦除 Prometheus 指標,請使用以下範例配置 ConfigMap 檔。

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - 要設定從代理的守護程式(針對群組的每個節點)的 Prometheus 指標的刮擦,請在 ConfigMap 中設定以下內容:
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >$NODE_IP 是容器參數的特定 Azure 監視器,可以使用節點 IP 位址代替節點 IP 位址。 它必須是所有大寫。 

    - 要透過指定窗格註解來設定 Prometheus 指標的擦除,請執行以下步驟:

       1. 在 ConfigMap 中,指定以下內容:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. 為窗格註解指定以下設定:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          如果要將監視限制為具有註解的窗格的特定命名空間,例如僅包括專用於生產工作負載的窗格,請在 ConfigMap`monitor_kubernetes_pod`中`true`設定到 ,並添加指定要刮取的命名`monitor_kubernetes_pods_namespaces`空間篩選器 。 例如， `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. 對於 Azure 紅帽 OpenShift 以外的群集,執行`kubectl apply -f <configmap_yaml_file.yaml>`以下庫布克 特爾命令: 。
    
    範例： `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    對於 Azure 紅帽 OpenShift,請將更改保存在編輯器中。

配置更改可能需要幾分鐘才能完成才能生效,群集中的所有omsagentpod都將重新啟動。 重新啟動是所有 omsagent pod 的滾動重新啟動,並非所有重新啟動都同時重新啟動。 重新啟動完成後,會顯示類似於以下內容的訊息,並包含結果`configmap "container-azm-ms-agentconfig" created`: 。

您可以通過執行命令 來查看 Azure 紅帽 OpenShift`oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging`的更新配置映射。 

## <a name="applying-updated-configmap"></a>套用更新的 ConfigMap

如果已向群集部署了 ConfigMap,並且希望使用較新的配置更新它,則可以編輯以前使用的 ConfigMap 檔,然後使用與以前相同的命令應用。

對於 Azure 紅帽 OpenShift 以外的庫伯內斯`kubectl apply -f <configmap_yaml_file.yaml`群集,執行指令 。 

對於 Azure 紅帽 OpenShift`oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`群集, 執行命令,在預設編輯器中打開檔以修改並保存該檔。

配置更改可能需要幾分鐘才能完成才能生效,群集中的所有omsagentpod都將重新啟動。 重新啟動是所有 omsagent pod 的滾動重新啟動,並非所有重新啟動都同時重新啟動。 重新啟動完成後,會顯示類似於以下內容的訊息,並包含結果`configmap "container-azm-ms-agentconfig" updated`: 。

## <a name="verify-configuration"></a>驗證組態

要驗證設定已成功套用於叢集,請使用以下指令檢視來自代理窗格的紀錄: `kubectl logs omsagent-fdf58 -n=kube-system`。 

>[!NOTE]
>此命令不適用於 Azure 紅帽 OpenShift 群集。
> 

如果 omsagent pod 存在設定錯誤,則輸出將顯示類似於以下內容的錯誤:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

與應用配置更改相關的錯誤也可供查看。 以下選項可用於對設定變更和報廢 Prometheus 指標執行其他故障排除:

- 使用同`kubectl logs`一指令的代理 pod 紀錄 
    >[!NOTE]
    >此命令不適用於 Azure 紅帽 OpenShift 群集。
    > 

- 從實時數據(預覽)。 即時資料(預覽)紀錄顯示的錯誤類似於以下內容:

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- 從日誌分析工作區中的**KubeMonAgent 事件**表。 每小時發送一次數據,對刮擦錯誤和配置錯誤*的錯誤*嚴重性進行*警告*嚴重性。 如果沒有錯誤,表中的條目將具有嚴重性*信息*的數據,該數據不報告任何錯誤。 **"標記'** 屬性包含有關發生錯誤的 Pod 和容器 ID 的詳細資訊,以及發生錯誤的第一次發生、最後一次發生和計數。

- 對於 Azure 紅帽 OpenShift,通過搜索**容器日誌**表來檢查 omsagent 日誌,以驗證是否啟用了打開 Shift-azure 日誌記錄的日誌集合。

錯誤阻止omsagent分析檔,從而導致它重新啟動並使用預設配置。 在 Azure 紅帽 OpenShift 以外的群集上更正 ConfigMap 中的錯誤後,請儲存 yaml 檔,並透過執行命令應用於`kubectl apply -f <configmap_yaml_file.yaml`更新的 ConfigMap。 

對於 Azure 紅帽 OpenShift,請透過執行指令:編輯並`oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`保存更新的 ConfigMaps。

## <a name="query-prometheus-metrics-data"></a>查詢普羅米塞烏斯指標數據

要檢視 Azure 監視器刮擦的普羅米圖斯指標以及代理報告的任何設定/刮擦錯誤,請查看[查詢 Prometheus 指標資料和](container-insights-log-search.md#query-prometheus-metrics-data)[查詢設定或刮擦錯誤](container-insights-log-search.md#query-config-or-scraping-errors)。

## <a name="view-prometheus-metrics-in-grafana"></a>查看格拉法納的普羅米蘇斯指標

容器的 Azure 監視器支援查看存儲在 Grafana 儀表板中的日誌分析工作區中的指標。 我們提供了一個範本,您可以從 Grafana 的[儀表板儲存庫](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker)下載該範本,以便開始並參考,以説明您瞭解如何從受監視的群集中查詢其他數據,以便在自定義 Grafana 儀表板中可視化。 

## <a name="review-prometheus-data-usage"></a>檢視普羅米圖斯資料使用方式

要識別每個指標大小的引入量(以 GB 表示每天瞭解是否高),請提供以下查詢。

```
InsightsMetrics 
| where Namespace == "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
輸出顯示類似於以下內容的結果:

![紀錄資料引入卷的查詢結果](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

要估計每個指標的大小(以 GB)表示一個月才能瞭解工作區中接收的數據量是否高,請提供以下查詢。

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

輸出顯示類似於以下內容的結果:

![紀錄資料引入卷的查詢結果](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

有關如何監視數據使用方式和分析成本的詳細資訊,請造訪[Azure 監視器日誌的「管理使用方式和成本](../platform/manage-cost-storage.md)」 。

## <a name="next-steps"></a>後續步驟

此處瞭解有關設定容器工作負載中抖出、黏穩和環境變數的代理集合設定[的更多內容](container-insights-agent-config.md)。 
