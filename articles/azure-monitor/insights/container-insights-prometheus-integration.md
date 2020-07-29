---
title: 設定容器的 Azure 監視器 Prometheus 整合 |Microsoft Docs
description: 本文說明如何設定容器代理程式的 Azure 監視器，以抓取 Prometheus 與 Kubernetes 叢集的計量。
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: f7a43f00ce160829cc8e6ed3b6272ab14aaace66
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800455"
---
# <a name="configure-scraping-of-prometheus-metrics-with-azure-monitor-for-containers"></a>使用容器的 Azure 監視器設定 Prometheus 計量的抓取

[Prometheus](https://prometheus.io/)是受歡迎的開放原始碼計量監視解決方案，而且是[雲端原生計算基礎](https://www.cncf.io/)的一部分。 適用于容器的 Azure 監視器可提供順暢的上架體驗來收集 Prometheus 計量。 一般而言，若要使用 Prometheus，您必須設定和管理具有存放區的 Prometheus 伺服器。 藉由與 Azure 監視器整合，就不需要 Prometheus 伺服器。 您只需要透過匯出工具或 pod （應用程式）公開 Prometheus 計量端點，容器的容器化代理 Azure 監視器程式就可以為您抓取計量。 

![適用于 Prometheus 的容器監視架構](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>抓取 Prometheus 計量支援的最低代理程式版本是 ciprod07092019 或更新版本，而且在資料表中支援寫入設定和代理程式錯誤的代理程式版本 `KubeMonAgentEvents` 是 ciprod10112019。 適用于 Azure Red Hat OpenShift 和 Red Hat OpenShift v4，代理程式版本 ciprod04162020 或更高。 
>
>如需代理程式版本以及每個版本內含內容的詳細資訊，請參閱[代理程式版本](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)資訊。 
>若要驗證您的代理程式版本，請在 [**節點**] 索引標籤中選取節點，然後在 [屬性] 窗格的 [**代理程式映射**標籤] 屬性值。

Prometheus 計量的抓取支援裝載于上的 Kubernetes 叢集：

- Azure Kubernetes Service (AKS)
- Azure Stack 或內部部署
- Azure Red Hat OpenShift 3.x 版
- Azure Red Hat OpenShift 和 Red Hat OpenShift 4.x 版

### <a name="prometheus-scraping-settings"></a>Prometheus 抓取設定

來自 Prometheus 之計量的作用中抓取是從兩個觀點的其中一個來執行：

* 整個叢集的 HTTP URL，並從服務的列出端點探索目標。 例如，k8s 服務，例如 kube-dns 和 kube 狀態-計量，以及應用程式特定的 pod 附注。 在此內容中收集的計量會定義于 ConfigMap 區段 *[Prometheus data_collection_settings. cluster]*。
* 整個節點-HTTP URL，並從服務的列出端點探索目標。 在此內容中收集的計量會定義于 ConfigMap 區段 *[Prometheus_data_collection_settings. node]* 中。

| 端點 | 影響範圍 | 範例 |
|----------|-------|---------|
| Pod 注釋 | 全叢集 | 備註 <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Kubernetes 服務 | 全叢集 | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| url/端點 | 每個節點和/或全叢集的叢集 | `http://myurl:9101/metrics` |

指定 URL 時，容器的 Azure 監視器只會抓取端點。 當指定 Kubernetes 服務時，會使用叢集 DNS 伺服器解析服務名稱以取得 IP 位址，然後剪輯解析的服務。

|影響範圍 | 答案 | 資料類型 | 值 | 說明 |
|------|-----|-----------|-------|-------------|
| 全叢集 | | | | 指定下列三種方法中的任何一種，以抓取度量的端點。 |
| | `urls` | String | 以逗號分隔的陣列 | HTTP 端點（IP 位址或指定的有效 URL 路徑）。 例如： `urls=[$NODE_IP/metrics]` 。 （$NODE _IP 是容器參數的特定 Azure 監視器，可以用來取代節點 IP 位址。 必須全部大寫）。 |
| | `kubernetes_services` | String | 以逗號分隔的陣列 | Kubernetes 服務的陣列，可從 kube 狀態計量抓取計量。 例如，`kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics",http://my-service-dns.my-namespace:9100/metrics]`。|
| | `monitor_kubernetes_pods` | Boolean | true 或 false | 當設定為時 `true` ，在整個叢集的設定中，容器代理程式的 Azure 監視器將會針對下列 Prometheus 注釋，在整個叢集中抓取 Kubernetes pod：<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Boolean | true 或 false | 啟用 pod 的抓取。 `monitor_kubernetes_pods` 必須設為 `true`。 |
| | `prometheus.io/scheme` | String | http 或 https | 預設為透過 HTTP 的 scrapping。 如有必要，請將設定為 `https` 。 | 
| | `prometheus.io/path` | String | 以逗號分隔的陣列 | 從中提取計量的來源 HTTP 資源路徑。 如果計量路徑不是 `/metrics` ，請使用此注釋加以定義。 |
| | `prometheus.io/port` | String | 9102 | 指定要抓取的埠。 如果未設定埠，則會預設為9102。 |
| | `monitor_kubernetes_pods_namespaces` | String | 以逗號分隔的陣列 | 允許的命名空間清單，以從 Kubernetes pod 抓取計量。<br> 例如， `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| 全節點 | `urls` | String | 以逗號分隔的陣列 | HTTP 端點（IP 位址或指定的有效 URL 路徑）。 例如： `urls=[$NODE_IP/metrics]` 。 （$NODE _IP 是容器參數的特定 Azure 監視器，可以用來取代節點 IP 位址。 必須全部大寫）。 |
| 全節點或全叢集 | `interval` | String | 60s | 收集間隔的預設值為一分鐘（60秒）。 您可以將 *[prometheus_data_collection_settings]* 和/或 *[prometheus_data_collection_settings cluster]* 的集合修改為時間單位，例如 s、m、h。 |
| 全節點或全叢集 | `fieldpass`<br> `fielddrop`| String | 以逗號分隔的陣列 | 您可以藉由設定 allow （ `fieldpass` ）和不允許（）清單，指定要收集的特定計量，或不在端點上 `fielddrop` 。 您必須先設定允許清單。 |

ConfigMaps 是全域清單，而且只能有一個 ConfigMap 套用至代理程式。 您不能有另一個 ConfigMaps overruling 集合。

## <a name="configure-and-deploy-configmaps"></a>設定和部署 ConfigMaps

請執行下列步驟，為下列叢集設定 ConfigMap 設定檔：

* Azure Kubernetes Service (AKS)
* Azure Stack 或內部部署
* Azure Red Hat OpenShift 2.x 版和 Red Hat OpenShift 4.x 版

1. [下載](https://aka.ms/container-azm-ms-agentconfig)範本 ConfigMap yaml 檔，並將它儲存為 azm-ms-agentconfig. yaml。

   >[!NOTE]
   >使用 Azure Red Hat OpenShift 時，不需要執行此步驟，因為 ConfigMap 範本已存在於叢集上。

2. 使用您的自訂來編輯 ConfigMap yaml 檔案，以抓取 Prometheus 計量。

    >[!NOTE]
    >如果您要編輯 Azure Red Hat OpenShift 的 ConfigMap yaml 檔，請先執行命令， `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` 以在文字編輯器中開啟檔案。

    >[!NOTE]
    >您 `openshift.io/reconcile-protect: "true"` 必須在*azm-ms-agentconfig* ConfigMap 的中繼資料底下新增下列注釋，以防止進行調整。 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - 若要收集整個叢集的 Kubernetes 服務，請使用下列範例來設定 ConfigMap 檔案。

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - 若要設定從整個叢集的特定 URL 抓取 Prometheus 計量，請使用下列範例來設定 ConfigMap 檔案。

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - 若要從代理程式的 DaemonSet 針對叢集中的每個個別節點設定 Prometheus 計量的抓取，請在 ConfigMap 中設定下列各項：
    
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
        >$NODE _IP 是容器參數的特定 Azure 監視器，可以用來取代節點 IP 位址。 必須全部大寫。 

    - 若要藉由指定 pod 注釋來設定 Prometheus 計量的抓取，請執行下列步驟：

       1. 在 ConfigMap 中，指定下列各項：

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. 指定 pod 注釋的下列設定：

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          如果您想要將監視限制為具有批註之 pod 的特定命名空間，例如僅包含生產工作負載專用的 pod，請在 ConfigMap 中將設定 `monitor_kubernetes_pod` 為， `true` 並加入命名空間篩選， `monitor_kubernetes_pods_namespaces` 指定要從抓取的命名空間。 例如， `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. 執行下列 kubectl 命令： `kubectl apply -f <configmap_yaml_file.yaml>` 。
    
    範例： `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

設定變更可能需要幾分鐘的時間才會生效，且叢集中的所有 omsagent pod 都會重新開機。 重新開機是所有 omsagent pod 的輪流重新開機，不會同時全部重新開機。 當重新開機完成時，會顯示與下列類似的訊息，並包含結果： `configmap "container-azm-ms-agentconfig" created` 。

## <a name="configure-and-deploy-configmaps---azure-red-hat-openshift-v3"></a>設定和部署 ConfigMaps-Azure Red Hat OpenShift v3

本節包含成功設定 Azure Red Hat OpenShift v3. x 叢集之 ConfigMap 設定檔的需求和步驟。

>[!NOTE]
>針對 Azure Red Hat OpenShift v3. x，範本 ConfigMap 檔案會建立在*OpenShift-Azure 記錄*命名空間中。 它未設定為主動從代理程式抓取計量或資料收集。

### <a name="prerequisites"></a>必要條件

開始之前，請確認您是 Azure Red Hat OpenShift 叢集的客戶叢集系統管理員角色成員，以設定容器化代理程式和 Prometheus 抓取設定。 若要確認您是*osa-customer-admins*群組的成員，請執行下列命令：

``` bash
  oc get groups
```

輸出看起來會像下面這樣：

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

如果您是*osa-客戶-admins*群組的成員，您應該能夠 `container-azm-ms-agentconfig` 使用下列命令來列出 ConfigMap：

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

輸出看起來會像下面這樣：

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="enable-monitoring"></a>啟用監視

請執行下列步驟，為您的 Azure Red Hat OpenShift v3. x 叢集設定 ConfigMap 設定檔。

1. 使用您的自訂來編輯 ConfigMap yaml 檔案，以抓取 Prometheus 計量。 ConfigMap 範本已存在於 Red Hat OpenShift v3 叢集上。 執行命令 `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` ，以在文字編輯器中開啟檔案。

    >[!NOTE]
    >您 `openshift.io/reconcile-protect: "true"` 必須在*azm-ms-agentconfig* ConfigMap 的中繼資料底下新增下列注釋，以防止進行調整。 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - 若要收集整個叢集的 Kubernetes 服務，請使用下列範例來設定 ConfigMap 檔案。

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - 若要設定從整個叢集的特定 URL 抓取 Prometheus 計量，請使用下列範例來設定 ConfigMap 檔案。

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - 若要從代理程式的 DaemonSet 針對叢集中的每個個別節點設定 Prometheus 計量的抓取，請在 ConfigMap 中設定下列各項：
    
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
        >$NODE _IP 是容器參數的特定 Azure 監視器，可以用來取代節點 IP 位址。 必須全部大寫。 

    - 若要藉由指定 pod 注釋來設定 Prometheus 計量的抓取，請執行下列步驟：

       1. 在 ConfigMap 中，指定下列各項：

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. 指定 pod 注釋的下列設定：

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          如果您想要將監視限制為具有批註之 pod 的特定命名空間，例如僅包含生產工作負載專用的 pod，請在 ConfigMap 中將設定 `monitor_kubernetes_pod` 為， `true` 並加入命名空間篩選， `monitor_kubernetes_pods_namespaces` 指定要從抓取的命名空間。 例如， `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

2. 將您的變更儲存在編輯器中。

設定變更可能需要幾分鐘的時間才會生效，且叢集中的所有 omsagent pod 都會重新開機。 重新開機是所有 omsagent pod 的輪流重新開機，不會同時全部重新開機。 當重新開機完成時，會顯示與下列類似的訊息，並包含結果： `configmap "container-azm-ms-agentconfig" created` 。

您可以藉由執行命令來查看已更新的 ConfigMap `oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging` 。 

## <a name="applying-updated-configmap"></a>套用更新的 ConfigMap

如果您已將 ConfigMap 部署至叢集，而且想要以較新的設定更新它，您可以編輯先前使用的 ConfigMap 檔案，然後使用與之前相同的命令來套用。

針對下列 Kubernetes 環境：

- Azure Kubernetes Service (AKS)
- Azure Stack 或內部部署
- Azure Red Hat OpenShift 和 Red Hat OpenShift 4.x 版

執行命令 `kubectl apply -f <configmap_yaml_file.yaml` 。 

針對 Azure Red Hat OpenShift v3. x 叢集，執行命令， `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` 以在預設編輯器中開啟檔案以進行修改，然後加以儲存。

設定變更可能需要幾分鐘的時間才會生效，且叢集中的所有 omsagent pod 都會重新開機。 重新開機是所有 omsagent pod 的輪流重新開機，不會同時全部重新開機。 當重新開機完成時，會顯示與下列類似的訊息，並包含結果： `configmap "container-azm-ms-agentconfig" updated` 。

## <a name="verify-configuration"></a>驗證組態

若要確認設定已成功套用至叢集，請使用下列命令來檢查代理程式 pod 中的記錄： `kubectl logs omsagent-fdf58 -n=kube-system` 。 

>[!NOTE]
>此命令不適用於 Azure Red Hat OpenShift v3. x 叢集。
> 

如果 omsagent pod 有設定錯誤，輸出將會顯示類似下列的錯誤：

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

與套用設定變更相關的錯誤也可供審查。 下列選項可用於執行設定變更的其他疑難排解和 Prometheus 計量的抓取：

- 從代理程式 pod 記錄使用相同的 `kubectl logs` 命令 
    >[!NOTE]
    >此命令不適用於 Azure Red Hat OpenShift 叢集。
    > 

- 從即時資料（預覽）。 即時資料（預覽）記錄會顯示類似下列的錯誤：

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- 從 Log Analytics 工作區中的**KubeMonAgentEvents**資料表。 資料會每小時傳送一次，具有抓取錯誤的*警告*嚴重性和設定錯誤的*錯誤*嚴重性。 如果沒有任何錯誤，則資料表中的專案將會有具有嚴重性*資訊*的資料，而這不會報告任何錯誤。 **Tags**屬性包含發生錯誤之 pod 和容器識別碼的詳細資訊，以及最後一次出現的第一個發生次數、最後一次發生次數和計數。

- 針對 Azure Red Hat OpenShift v3. x 和 v4. x，藉由搜尋**ContainerLog**資料表來檢查 omsagent 記錄，以確認是否已啟用記錄收集 OpenShift-Azure 記錄。

錯誤會使 omsagent 無法剖析檔案，因而導致它重新開機並使用預設設定。 在 Azure Red Hat OpenShift v3. x 以外的叢集上更正 ConfigMap 中的錯誤之後，請儲存 yaml 檔案，並執行下列命令來套用更新的 ConfigMaps： `kubectl apply -f <configmap_yaml_file.yaml` 。 

針對 Azure Red Hat OpenShift v3. x，執行下列命令來編輯並儲存更新的 ConfigMaps： `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` 。

## <a name="query-prometheus-metrics-data"></a>查詢 Prometheus 計量資料

若要依 Azure 監視器以及代理程式回報的任何設定/抓取錯誤來查看 prometheus 計量剪輯，請參閱[查詢 prometheus 計量資料](container-insights-log-search.md#query-prometheus-metrics-data)和[查詢設定或抓取錯誤](container-insights-log-search.md#query-config-or-scraping-errors)。

## <a name="view-prometheus-metrics-in-grafana"></a>在 Grafana 中查看 Prometheus 計量

適用於容器的 Azure 監視器支援在 Grafana 儀表板中，檢視 Log Analytics 工作區中儲存的計量。 我們提供的範本可讓您從 Grafana 的[儀表板存放庫](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) \(英文\) 下載，以便讓您能夠開始使用並進行參考，可協助您了解如何從受監視的叢集中查詢其他資料，以在自訂 Grafana 儀表板中進行視覺化。 

## <a name="review-prometheus-data-usage"></a>審查 Prometheus 資料使用量

若要識別每日計量大小的內嵌磁片區（GB）以瞭解其是否為高，請提供下列查詢。

```
InsightsMetrics
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```

輸出會顯示類似下面的結果：

![資料內嵌磁片區的記錄查詢結果](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

若要預估每個計量大小（GB）為一個月，以瞭解工作區中所接收的資料內嵌量是否過高，會提供下列查詢。

```
InsightsMetrics
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

輸出會顯示類似下面的結果：

![資料內嵌磁片區的記錄查詢結果](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

如需有關如何監視資料使用量和分析成本的進一步資訊，請前往使用[Azure 監視器記錄來管理使用量和成本](../platform/manage-cost-storage.md)。

## <a name="next-steps"></a>後續步驟

從[這裡](container-insights-agent-config.md)的容器工作負載，深入瞭解如何設定 stdout、stderr 和環境變數的代理程式組件合設定。 
