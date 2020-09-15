---
title: 設定 Prometheus 整合的容器 Azure 監視器 |Microsoft Docs
description: 本文說明如何設定容器代理程式的 Azure 監視器，以從 Prometheus 與 Kubernetes 叢集抓取計量。
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 8c83d962a31150b31f5883150a2f7bd8d4b49183
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069419"
---
# <a name="configure-scraping-of-prometheus-metrics-with-azure-monitor-for-containers"></a>使用適用於容器的 Azure 監視器設定 Prometheus 計量抓取

[Prometheus](https://prometheus.io/) 是受歡迎的開放原始碼計量監視解決方案，屬於 [雲端原生計算基礎](https://www.cncf.io/)。 適用于容器的 Azure 監視器可提供順暢的上線體驗來收集 Prometheus 計量。 一般而言，若要使用 Prometheus，您必須使用存放區來設定及管理 Prometheus 伺服器。 藉由與 Azure 監視器整合，不需要 Prometheus 伺服器。 您只需要透過匯出工具或 pod (應用程式) 公開 Prometheus 計量端點，容器的容器化代理 Azure 監視器程式就可以為您抓取度量。 

![適用于 Prometheus 的容器監視架構](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>抓取 Prometheus 計量支援的最低代理程式版本為 ciprod07092019 或更新版本，且資料表中支援寫入設定和代理程式錯誤的代理程式版本 `KubeMonAgentEvents` 為 ciprod10112019。 針對 Azure Red Hat OpenShift 和 Red Hat OpenShift v4，代理程式版本 ciprod04162020 或更高版本。 
>
>如需代理程式版本及每個版本中所含內容的詳細資訊，請參閱 [代理程式](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)版本資訊。 
>若要驗證您的代理程式版本，請在 [ **節點** ] 索引標籤中選取節點，然後在 [ **代理程式映射** 標籤] 屬性的 [屬性] 窗格附注值。

Prometheus 計量的抓取支援的 Kubernetes 叢集位於：

- Azure Kubernetes Service (AKS)
- Azure Stack 或內部部署
- Azure Red Hat OpenShift 3.x 版
- Azure Red Hat OpenShift 和 Red Hat OpenShift 4.x 版

### <a name="prometheus-scraping-settings"></a>Prometheus 抓取設定

從 Prometheus 執行的計量的有效抓取是從下列兩個角度的其中一種來執行：

* 整個叢集中的 HTTP URL，並探索來自服務所列端點的目標。 例如，k8s 服務，例如 kube-dns 和 kube 狀態計量，以及應用程式專屬的 pod 附注。 在此內容中收集的計量將會定義于 ConfigMap 區段 *[Prometheus data_collection_settings. cluster]* 中。
* 整個節點的 HTTP URL，並探索來自服務所列端點的目標。 在此內容中收集的計量將會定義于 ConfigMap 區段 *[Prometheus_data_collection_settings. node]* 中。

| 端點 | 範圍 | 範例 |
|----------|-------|---------|
| Pod 注釋 | 全叢集 | 注釋： <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Kubernetes 服務 | 全叢集 | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| url/端點 | 每個節點和/或整個叢集 | `http://myurl:9101/metrics` |

當指定 URL 時，容器的 Azure 監視器只會搜刮端點。 指定 Kubernetes 服務時，會使用叢集 DNS 伺服器來解析服務名稱，以取得 IP 位址，然後將解析的服務剪輯。

|範圍 | 機碼 | 資料類型 | 值 | 描述 |
|------|-----|-----------|-------|-------------|
| 全叢集 | | | | 指定下列三種方法之一來抓取計量的端點。 |
| | `urls` | String | 逗點分隔陣列 | HTTP 端點 (指定) 的 IP 位址或有效 URL 路徑。 例如： `urls=[$NODE_IP/metrics]` 。  ($NODE _IP 是容器參數的特定 Azure 監視器，而且可以用來取代節點 IP 位址。 必須全部大寫。 )  |
| | `kubernetes_services` | String | 逗點分隔陣列 | Kubernetes 服務的陣列，用來從 kube 狀態計量抓取計量。 例如，`kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics",http://my-service-dns.my-namespace:9100/metrics]`。|
| | `monitor_kubernetes_pods` | Boolean | true 或 false | 當設定為時 `true` ，在整個叢集設定中，容器代理程式的 Azure 監視器將會在整個叢集中抓取 Kubernetes pod，以進行下列 Prometheus 批註：<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Boolean | true 或 false | 啟用 pod 的抓取。 `monitor_kubernetes_pods` 必須設為 `true`。 |
| | `prometheus.io/scheme` | String | http 或 https | 預設為透過 HTTP scrapping。 如有必要，請將設定為 `https` 。 | 
| | `prometheus.io/path` | String | 逗點分隔陣列 | 要從中提取度量的 HTTP 資源路徑。 如果計量路徑不是 `/metrics` ，請使用這個注釋來定義它。 |
| | `prometheus.io/port` | String | 9102 | 指定要從中抓取的埠。 如果未設定埠，則會預設為9102。 |
| | `monitor_kubernetes_pods_namespaces` | String | 逗點分隔陣列 | 允許從 Kubernetes pod 抓取計量的命名空間清單。<br> 例如， `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| 整個節點 | `urls` | String | 逗點分隔陣列 | HTTP 端點 (指定) 的 IP 位址或有效 URL 路徑。 例如： `urls=[$NODE_IP/metrics]` 。  ($NODE _IP 是容器參數的特定 Azure 監視器，而且可以用來取代節點 IP 位址。 必須全部大寫。 )  |
| 全節點或全叢集 | `interval` | String | 60 年 代 | 收集間隔預設為一分鐘 (60 秒) 。 您可以將 *[prometheus_data_collection_settings]* 和/或 *[prometheus_data_collection_settings]* 的集合修改為時間單位，例如 s、m、h。 |
| 全節點或全叢集 | `fieldpass`<br> `fielddrop`| String | 逗點分隔陣列 | 您可以藉由設定 [允許 (] `fieldpass`) ，並不允許 () 清單，指定要收集的特定計量，而不是從端點收集 `fielddrop` 。 您必須先設定允許清單。 |

ConfigMaps 是全域清單，而且只能有一個 ConfigMap 套用至代理程式。 您不能有其他 ConfigMaps overruling 集合。

## <a name="configure-and-deploy-configmaps"></a>設定和部署 ConfigMaps

請執行下列步驟來設定下列叢集的 ConfigMap 設定檔：

* Azure Kubernetes Service (AKS)
* Azure Stack 或內部部署
* Azure Red Hat OpenShift 4.x 和 Red Hat OpenShift 4.x 版

1. [下載](https://aka.ms/container-azm-ms-agentconfig) 範本 ConfigMap yaml 檔案，並將它儲存為容器-container.azm.ms-ms-agentconfig. yaml。

   >[!NOTE]
   >使用 Azure Red Hat OpenShift 時，不需要執行此步驟，因為 ConfigMap 範本已經存在於叢集上。

2. 編輯具有您自訂的 ConfigMap yaml 檔，以抓取 Prometheus 度量。

    >[!NOTE]
    >如果您正在編輯 Azure Red Hat OpenShift 的 ConfigMap yaml 檔案，請先執行命令 `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` 以在文字編輯器中開啟檔案。

    >[!NOTE]
    >您 `openshift.io/reconcile-protect: "true"` 必須在 *容器-container.azm.ms-agentconfig* ConfigMap 的中繼資料下新增下列注釋，以防止對帳。 
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

    - 若要在整個叢集中設定來自特定 URL 的 Prometheus 計量抓取，請使用下列範例來設定 ConfigMap 檔。

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - 若要針對叢集中的每個個別節點設定代理程式 DaemonSet 的 Prometheus 計量抓取，請在 ConfigMap 中設定下列各項：
    
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
        >$NODE _IP 是容器參數的特定 Azure 監視器，而且可以用來取代節點 IP 位址。 它必須全部為大寫。 

    - 若要藉由指定 pod 注釋來設定 Prometheus 計量的抓取，請執行下列步驟：

       1. 在 ConfigMap 中，指定下列各項：

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. 針對 pod 批註指定下列設定：

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          如果您想要將監視限制為具有批註的 pod 的特定命名空間，例如只包含適用于生產工作負載的 pod，請 `monitor_kubernetes_pod` `true` 在 ConfigMap 中將設定為，然後新增命名空間篩選器， `monitor_kubernetes_pods_namespaces` 以指定要抓取的命名空間。 例如， `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. 執行下列 kubectl 命令： `kubectl apply -f <configmap_yaml_file.yaml>` 。
    
    範例： `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

設定變更可能需要幾分鐘的時間才會生效，且叢集中的所有 omsagent pod 都會重新開機。 重新開機是所有 omsagent pod 的輪流重新開機，不會同時重新開機。 當重新開機完成時，會顯示類似下列的訊息，並包含結果： `configmap "container-azm-ms-agentconfig" created` 。

## <a name="configure-and-deploy-configmaps---azure-red-hat-openshift-v3"></a>設定和部署 ConfigMaps-Azure Red Hat OpenShift v3

本節包含成功設定 Azure Red Hat OpenShift v3. x 叢集之 ConfigMap 設定檔的需求和步驟。

>[!NOTE]
>針對 Azure Red Hat OpenShift v3，會在 *OpenShift-Azure 記錄* 命名空間中建立範本 ConfigMap 檔案。 未設定為主動從代理程式抓取計量或資料收集。

### <a name="prerequisites"></a>必要條件

開始之前，請確認您是 Azure Red Hat OpenShift 叢集的客戶叢集系統管理員角色成員，以設定容器化代理程式和 Prometheus 抓取設定。 若要確認您是 *osa-customer-admins* 群組的成員，請執行下列命令：

``` bash
  oc get groups
```

輸出看起來會像下面這樣：

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

如果您是 *osa-customer-admins* 群組的成員，您應該可以 `container-azm-ms-agentconfig` 使用下列命令來列出 ConfigMap：

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

輸出看起來會像下面這樣：

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="enable-monitoring"></a>啟用監視

請執行下列步驟來設定 Azure Red Hat OpenShift v3. x 叢集的 ConfigMap 設定檔。

1. 編輯具有您自訂的 ConfigMap yaml 檔，以抓取 Prometheus 度量。 Red Hat OpenShift v3 叢集上已有 ConfigMap 範本。 執行命令 `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` ，以在文字編輯器中開啟檔案。

    >[!NOTE]
    >您 `openshift.io/reconcile-protect: "true"` 必須在 *容器-container.azm.ms-agentconfig* ConfigMap 的中繼資料下新增下列注釋，以防止對帳。 
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

    - 若要在整個叢集中設定來自特定 URL 的 Prometheus 計量抓取，請使用下列範例來設定 ConfigMap 檔。

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - 若要針對叢集中的每個個別節點設定代理程式 DaemonSet 的 Prometheus 計量抓取，請在 ConfigMap 中設定下列各項：
    
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
        >$NODE _IP 是容器參數的特定 Azure 監視器，而且可以用來取代節點 IP 位址。 它必須全部為大寫。 

    - 若要藉由指定 pod 注釋來設定 Prometheus 計量的抓取，請執行下列步驟：

       1. 在 ConfigMap 中，指定下列各項：

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. 針對 pod 批註指定下列設定：

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          如果您想要將監視限制為具有批註的 pod 的特定命名空間，例如只包含適用于生產工作負載的 pod，請 `monitor_kubernetes_pod` `true` 在 ConfigMap 中將設定為，然後新增命名空間篩選器， `monitor_kubernetes_pods_namespaces` 以指定要抓取的命名空間。 例如， `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

2. 將您的變更儲存在編輯器中。

設定變更可能需要幾分鐘的時間才會生效，且叢集中的所有 omsagent pod 都會重新開機。 重新開機是所有 omsagent pod 的輪流重新開機，不會同時重新開機。 當重新開機完成時，會顯示類似下列的訊息，並包含結果： `configmap "container-azm-ms-agentconfig" created` 。

您可以藉由執行命令來查看更新的 ConfigMap `oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging` 。 

## <a name="applying-updated-configmap"></a>套用更新的 ConfigMap

如果您已將 ConfigMap 部署到您的叢集，而您想要使用較新的設定來更新它，您可以編輯先前使用的 ConfigMap 檔案，然後使用與之前相同的命令進行套用。

針對下列 Kubernetes 環境：

- Azure Kubernetes Service (AKS)
- Azure Stack 或內部部署
- Azure Red Hat OpenShift 和 Red Hat OpenShift 4.x 版

執行命令 `kubectl apply -f <configmap_yaml_file.yaml` 。 

若為 Azure Red Hat OpenShift v3. x 叢集，請執行命令， `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` 以在預設編輯器中開啟該檔案，以修改並儲存該檔案。

設定變更可能需要幾分鐘的時間才會生效，且叢集中的所有 omsagent pod 都會重新開機。 重新開機是所有 omsagent pod 的輪流重新開機，不會同時重新開機。 當重新開機完成時，會顯示類似下列的訊息，並包含結果： `configmap "container-azm-ms-agentconfig" updated` 。

## <a name="verify-configuration"></a>驗證組態

若要確認已成功將設定套用至叢集，請使用下列命令來檢查代理程式 pod 的記錄： `kubectl logs omsagent-fdf58 -n=kube-system` 。 

>[!NOTE]
>此命令不適用於 Azure Red Hat OpenShift v3. x 叢集。
> 

如果有來自 omsagent pod 的設定錯誤，輸出會顯示類似下列的錯誤：

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

套用設定變更的相關錯誤也可供審查。 下列選項可用來對設定變更和 Prometheus 計量抓取進行額外的疑難排解：

- 從使用相同命令的代理程式 pod 記錄檔 `kubectl logs` 
    >[!NOTE]
    >此命令不適用於 Azure Red Hat OpenShift 叢集。
    > 

- 從即時資料 (預覽) 。 即時資料 (預覽) 記錄顯示類似下列的錯誤：

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- 從 Log Analytics 工作區中的 **KubeMonAgentEvents** 資料表。 資料會每小時傳送一次，抓取錯誤的 *警告* 嚴重性和設定錯誤的 *錯誤* 嚴重性。 如果沒有任何錯誤，資料表中的專案將會有嚴重性 *資訊*的資料，而不會報告任何錯誤。 [標籤 **] 屬性包含** 發生錯誤之 pod 和容器識別碼的詳細資訊，以及過去一小時內第一次出現、最後一次出現和計數的詳細資訊。

- 針對 Azure Red Hat OpenShift v3. x 和 v4. x，請搜尋 **ContainerLog** 資料表來檢查 omsagent 記錄，確認是否已啟用 OpenShift 的記錄收集。

錯誤會使 omsagent 無法剖析檔案，導致它重新開機並使用預設設定。 修正 Azure Red Hat OpenShift v3 以外的叢集 ConfigMap 中的錯誤 () s 之後，請儲存 yaml 檔案，然後執行下列命令以套用更新的 ConfigMaps： `kubectl apply -f <configmap_yaml_file.yaml` 。 

若為 Azure Red Hat OpenShift v3，請執行下列命令來編輯並儲存更新的 ConfigMaps： `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` 。

## <a name="query-prometheus-metrics-data"></a>查詢 Prometheus 計量資料

若要查看依 Azure 監視器剪輯的 prometheus 計量，以及代理程式回報的任何設定/抓取錯誤，請參閱 [查詢 prometheus 計量資料](container-insights-log-search.md#query-prometheus-metrics-data) 和 [查詢設定或抓取錯誤](container-insights-log-search.md#query-config-or-scraping-errors)。

## <a name="view-prometheus-metrics-in-grafana"></a>在 Grafana 中查看 Prometheus 計量

適用於容器的 Azure 監視器支援在 Grafana 儀表板中，檢視 Log Analytics 工作區中儲存的計量。 我們提供的範本可讓您從 Grafana 的[儀表板存放庫](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) \(英文\) 下載，以便讓您能夠開始使用並進行參考，可協助您了解如何從受監視的叢集中查詢其他資料，以在自訂 Grafana 儀表板中進行視覺化。 

## <a name="review-prometheus-data-usage"></a>查看 Prometheus 資料使用量

若要找出每個計量大小的內嵌磁片區（每日 GB），以瞭解其是否很高，請提供下列查詢。

```
InsightsMetrics
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```

輸出會顯示如下所示的結果：

![資料內嵌磁片區的記錄查詢結果](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

若要預估每個計量的大小（GB），以瞭解在工作區中所收到的資料內嵌量是否很高，會提供下列查詢。

```
InsightsMetrics
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

輸出會顯示如下所示的結果：

![資料內嵌磁片區的記錄查詢結果](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

有關如何監視資料使用量和分析成本的詳細資訊，可透過 [Azure 監視器記錄來管理使用量和成本](../platform/manage-cost-storage.md)。

## <a name="next-steps"></a>後續步驟

從 [此處](container-insights-agent-config.md)的容器工作負載，深入瞭解如何設定 stdout、stderr 和環境變數的代理程式組件合設定。 
