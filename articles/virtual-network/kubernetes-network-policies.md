---
title: Azure Kubernetes 網路原則 | Microsoft Docs
description: 深入瞭解如何 Kubernetes 網路原則來保護您的 Kubernetes 叢集。
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: 0ecf3e5f30ee38c0d60c77df3d6aae3ad90930e9
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92332226"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Azure Kubernetes 網路原則總覽

網路原則提供 pod 的微分割，就像網路安全性群組一樣 (Nsg) 為 Vm 提供微型分割。 Azure 網路原則管理員 (也稱為 Azure NPM) 實行支援標準的 Kubernetes 網路原則規格。 您可以使用標籤來選取 pod 群組，並定義輸入和輸出規則的清單，以篩選進出這些 pod 的流量。 在 [Kubernetes 文件](https://kubernetes.io/docs/concepts/services-networking/network-policies/)中深入了解 Kubernetes 網路原則。

![Kubernetes 網路原則概觀](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Azure NPM 實行可與提供容器 VNet 整合的 Azure CNI 搭配運作。 NPM 目前僅在 Linux 上支援。 此實行會根據定義的原則，在 Linux IPTables 中設定允許和拒絕 IP 規則，以強制執行流量篩選。 這些規則會使用 Linux IPSets 來群組在一起。

## <a name="planning-security-for-your-kubernetes-cluster"></a>規劃 Kubernetes 叢集的安全性
在為您的叢集實施安全性時，請使用網路安全性群組 (Nsg) 來篩選進入和離開叢集子網 (北南部流量) 的流量。 使用 Azure NPM，在叢集中 pod 之間的流量 (東部-西部流量) 。

## <a name="using-azure-npm"></a>使用 Azure NPM
Azure NPM 可透過下列方式使用，以提供 pod 的微型分割。

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)
NPM 可在 AKS 中以原生方式提供，而且可以在建立叢集時啟用。 [使用 Azure Kubernetes Service (AKS) 中的網路原則](https://docs.microsoft.com/azure/aks/use-network-policies)，深入瞭解 pod 之間的安全流量。

### <a name="aks-engine"></a>AKS-引擎
AKS-Engine 是一種工具，產生在 Azure 中部署 Kubernetes 叢集的 Azure Resource Manager 範本。 叢集設定是在 JSON 檔案中指定，產生範本時會將該檔案傳遞到工具。 若要深入了解支援叢集設定及其描述的完整清單，請參閱＜Microsoft Azure Container Service 引擎 - 叢集定義＞。

若要在使用 acs-engine 所部署的叢集上啟用原則，請將叢集定義檔案中的 networkPolicy 設定值指定為 "azure"。

#### <a name="example-configuration"></a>範例設定

以下 JSON 範例設定會建立新的虛擬網路和子網路，並使用 Azure CNI 在其中部署 Kubernetes 叢集。 建議您使用 [記事本] 編輯 JSON 檔案。 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="do-it-yourself-diy-kubernetes-clusters-in-azure"></a>自行 (DIY) Azure 中的 Kubernetes 叢集
 針對 DIY 叢集，請先安裝 CNI 外掛程式，並在叢集中的每個虛擬機器上啟用它。 如需詳細指示，請參閱[部署自行部署的 Kubernetes 叢集外掛程式](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster)。

部署叢集之後，請執行下列 `kubectl` 命令來下載 AZURE NPM *daemon 集合* ，並將其套用至叢集。

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
解決方案也是開放原始碼，而程式碼位於 [Azure Container Networking repository](https://github.com/Azure/azure-container-networking/tree/master/npm) (Azure 容器網路存放庫)。

## <a name="monitor-and-visualize-network-configurations-with-azure-npm"></a>使用 Azure NPM 監視和視覺化網路設定
Azure NPM 包含資訊性 Prometheus 計量，可讓您監視及進一步瞭解您的設定。 它會在 Azure 入口網站或 Grafana Labs 中提供內建的視覺效果。 您可以使用 Azure 監視器或 Prometheus 伺服器來開始收集這些計量。

### <a name="benefits-of-azure-npm-metrics"></a>Azure NPM 計量的優點
先前使用者只能透過在叢集節點內執行的命令來瞭解其網路設定 `iptables -L` ，這會產生詳細資訊，並難以瞭解輸出。 NPM 計量提供下列與網路原則、IPTables 規則和 IPSets 相關的優點。
- 提供三個和時間維度之間關聯性的深入解析，以進行設定的調試。
- 所有 IPSets 和每個 IPSet 中的專案數。
- 套用具有 IPTable/IPSet 層級資料細微性的原則所花費的時間。
 
### <a name="supported-metrics"></a>支援的計量
以下是支援的計量清單：

|標準名稱 |描述  |Prometheus 計量類型  |標籤  |
|---------|---------|---------|---------|
|`npm_num_policies`     |網路原則的數目          |量測計         |-         |
|`npm_num_iptables_rules`     | IPTables 規則數目     | 量測計        |-         |         
|`npm_num_ipsets`     |IPSets 數目         |量測計            |-         |
|`npm_num_ipset_entries`     |所有 IPSets 中的 IP 位址專案數         |量測計         |-         |
|`npm_add_policy_exec_time`     |新增網路原則的執行時間         |摘要         |分量 (0.5、0.9 或 0.99)          |
|`npm_add_iptables_rule_exec_time`     |用於新增 IPTables 規則的執行時間         |摘要         |分量 (0.5、0.9 或 0.99)          |
|`npm_add_ipset_exec_time`     |用於新增 IPSet 的執行時間         |摘要         |分量 (0.5、0.9 或 0.99)          |
|`npm_ipset_counts` (advanced)      |每個個別 IPSet 中的專案數         |GaugeVec         |設定 & 雜湊的名稱         |

「Exec_time」計量中的不同分量層級可協助您區分一般和最糟的案例。

此外，每個「exec_time」摘要度量也有「exec_time_count」和「exec_time_sum」度量。

您可以透過 Azure 監視器的容器或 Prometheus 來剪輯計量。

### <a name="setup-for-azure-monitor"></a>Azure 監視器的設定
第一個步驟是為您的 Kubernetes 叢集啟用容器 Azure 監視器。 您可以在 [容器的 Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview)中找到步驟總覽。 啟用容器 Azure 監視器之後，請設定 [容器的 Azure 監視器 ConfigMap](https://aka.ms/container-azm-ms-agentconfig) ，以啟用 NPM 整合和 Prometheus NPM 計量的集合。 適用于容器的 Azure 監視器 ConfigMap 有一個 ```integrations``` 區段，其中包含可收集 NPM 計量的設定。 這些設定在 ConfigMap 中預設為停用。 啟用基本設定 ```collect_basic_metrics = true``` 將會收集基本的 NPM 計量。 啟用 advanced 設定 ```collect_advanced_metrics = true``` 將會收集基本計量以外的 advanced 計量。 

編輯 ConfigMap 之後，請將它儲存在本機，並將 ConfigMap 套用至您的叢集，如下所示。

```kubectl apply -f container-azm-ms-agentconfig.yaml``` 以下是來自 [ [適用于容器的 Azure 監視器] ConfigMap](https://aka.ms/container-azm-ms-agentconfig)的程式碼片段，其中顯示已啟用 [advanced 計量] 集合的 NPM 整合。
```
integrations: |-
    [integrations.azure_network_policy_manager]
        collect_basic_metrics = false
        collect_advanced_metrics = true
```
Advanced 計量是選擇性的，而開啟這些計量會自動開啟基本計量集合。 Advanced 計量目前僅包含 `npm_ipset_counts`

深入瞭解 [config map 中適用于容器的 Azure 監視器集合設定](https://aka.ms/azmon-containers-agent-collection-settings-doc)

### <a name="visualization-options-for-azure-monitor"></a>Azure 監視器的視覺效果選項
啟用 NPM 計量收集之後，您就可以使用容器深入解析或 Grafana 來查看 Azure 入口網站中的計量。

#### <a name="viewing-in-azure-portal-under-insights-for-the-cluster"></a>在叢集的深入解析下查看 Azure 入口網站
開啟 Azure 入口網站。 在叢集的深入解析中，流覽至 [活頁簿]，然後開啟 [網路原則管理員 (NPM) 設定]。

除了在) 下方 (圖片中觀看活頁簿，您也可以直接在 [見解] 區段下的 [記錄] 中查詢 Prometheus 計量。 例如，此查詢會傳回所收集的所有計量。
|TimeGenerated > 前 (5h) |其中 Name 包含 "npm_"

您也可以直接查詢計量的 Log Analytics。 深入瞭解 Log Analytics 查詢的消費者入門] (https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-log-search) 

#### <a name="viewing-in-grafana-dashboard"></a>在 Grafana 儀表板中查看
設定您的 Grafana 伺服器，並設定 Log Analytics 資料來源[（如下所述）。](https://grafana.com/grafana/plugins/grafana-azure-monitor-datasource) 然後，將 [Grafana 儀表板與 Log Analytics 後端](https://grafana.com/grafana/dashboards/10956) 匯入 Grafana Labs 中。

儀表板具有類似 Azure 活頁簿的視覺效果。 您可以將面板新增至圖表，& 從 InsightsMetrics 資料表將 NPM 計量視覺化。

### <a name="setup-for-prometheus-server"></a>設定 Prometheus 伺服器
有些使用者可能會選擇使用 Prometheus 伺服器來收集計量，而不是針對容器 Azure 監視器。 您只需要將兩個作業新增至您的抓取設定，即可收集 NPM 計量。

若要安裝簡單的 Prometheus 伺服器，請將此 helm 存放庫新增至您的叢集
```
helm repo add stable https://kubernetes-charts.storage.googleapis.com
helm repo update
```
然後新增伺服器
```
helm install prometheus stable/prometheus -n monitoring \
--set pushgateway.enabled=false,alertmanager.enabled=false, \
--set-file extraScrapeConfigs=prometheus-server-scrape-config.yaml
```
其中 `prometheus-server-scrape-config.yaml` 包含
```
- job_name: "azure-npm-node-metrics"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: node
  relabel_configs:
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
- job_name: "azure-npm-cluster-metrics"
  metrics_path: /cluster-metrics
  kubernetes_sd_configs:
  - role: service
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_service_name]
    regex: npm-metrics-cluster-service
    action: keep
# Comment from here to the end to collect advanced metrics: number of entries for each IPSet
  metric_relabel_configs:
  - source_labels: [__name__]
    regex: npm_ipset_counts
    action: drop
```


您也可以 `azure-npm-node-metrics` 使用下列內容來取代作業，或將其併入 Kubernetes pod 的預先存在作業：
```
- job_name: "azure-npm-node-metrics-from-pod-config"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: pod
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_pod_annotationpresent_azure_npm_scrapeable]
    action: keep
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
```

### <a name="visualization-options-for-prometheus"></a>Prometheus 的視覺效果選項
使用 Prometheus 伺服器時，僅支援 Grafana 儀表板。 

如果您還沒有這麼做，請設定 Grafana 伺服器並設定 Prometheus 資料來源。 然後，將 [具有 Prometheus 後端的 Grafana 儀表板](https://grafana.com/grafana/dashboards/13000) 匯入 Grafana Labs 中。

此儀表板的視覺效果與具有容器深入解析/Log Analytics 後端的儀表板相同。

### <a name="sample-dashboards"></a>範例儀表板
以下是一些範例儀表板，用於在容器深入解析中 NPM 計量 (CI) 和 Grafana

#### <a name="ci-summary-counts"></a>CI 摘要計數
![Azure 活頁簿摘要計數](media/kubernetes-network-policies/workbook-summary-counts.png)

#### <a name="ci-counts-over-time"></a>經過一段時間的 CI 計數
[![經過一段時間的 Azure 活頁簿計數](media/kubernetes-network-policies/workbook-counts-over-time.png)](media/kubernetes-network-policies/workbook-counts-over-time.png#lightbox)

#### <a name="ci-ipset-entries"></a>CI IPSet 專案
[![Azure 活頁簿 IPSet 專案](media/kubernetes-network-policies/workbook-ipset-entries.png)](media/kubernetes-network-policies/workbook-ipset-entries.png#lightbox)

#### <a name="ci-runtime-quantiles"></a>CI 執行時間分量
![Azure 活頁簿執行時間分量](media/kubernetes-network-policies/workbook-runtime-quantiles.png)

#### <a name="grafana-dashboard-summary-counts"></a>Grafana 儀表板摘要計數
![Grafana 儀表板摘要計數](media/kubernetes-network-policies/grafana-summary-counts.png)

#### <a name="grafana-dashboard-counts-over-time"></a>Grafana 一段時間的儀表板計數
[![Grafana 一段時間的儀表板計數](media/kubernetes-network-policies/grafana-counts-over-time.png)](media/kubernetes-network-policies/grafana-counts-over-time.png#lightbox)

#### <a name="grafana-dashboard-ipset-entries"></a>Grafana 儀表板 IPSet 專案
[![Grafana 儀表板 IPSet 專案](media/kubernetes-network-policies/grafana-ipset-entries.png)](media/kubernetes-network-policies/grafana-ipset-entries.png#lightbox)

#### <a name="grafana-dashboard-runtime-quantiles"></a>Grafana 儀表板執行時間分量
[![Grafana 儀表板執行時間分量](media/kubernetes-network-policies/grafana-runtime-quantiles.png)](media/kubernetes-network-policies/grafana-runtime-quantiles.png#lightbox)



## <a name="next-steps"></a>後續步驟
- 了解 [Azure Kubernetes Service](../aks/intro-kubernetes.md)。
-  了解[容器網路服務](container-networking-overview.md)。
- 部署適用于 Kubernetes 叢集或 Docker 容器[的外掛程式](deploy-container-networking.md)。

    