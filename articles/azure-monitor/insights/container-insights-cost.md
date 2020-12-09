---
title: 監視容器 Azure 監視器的成本 |Microsoft Docs
description: 本文說明 Azure 監視器為容器收集的計量 & 清查資料的監視成本，以協助客戶管理其使用量和相關成本。
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 81a20f564af68c3da6d63394e4cffe7caed91b46
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903206"
---
# <a name="understand-monitoring-costs-for-azure-monitor-for-containers"></a>了解 Azure 監視器的容器監視成本

本文提供容器 Azure 監視器的定價指導方針，以協助您瞭解下列各項：

* 如何在您啟用此深入解析之前事先預估成本

* 如何在為一或多個容器啟用容器 Azure 監視器之後測量成本

* 如何控制資料收集和降低成本

Azure 監視器記錄檔會收集、索引和儲存 Kubernetes 叢集所產生的資料。 

Azure 監視器的定價模型主要是根據您的 Log Analytics 工作區中每天內嵌 gb 的資料量。 Log Analytics 工作區的成本不只以收集的資料量為基礎，也取決於選取的方案，以及您選擇儲存從叢集產生之資料的時間長度。

>[!NOTE]
>所有大小和價格僅供範例估計之用。 請參閱 Azure 監視器 [定價](https://azure.microsoft.com/pricing/details/monitor/) 頁面，以取得最新的定價（根據您 Azure 監視器 Log Analytics 定價模型和 Azure 區域）。

以下摘要說明從 Kubernetes 叢集收集哪些類型的資料，以及會影響成本的容器 Azure 監視器，並可根據您的使用量進行自訂：

- Stdout，來自叢集中每個 Kubernetes 命名空間中每個受監視容器的 stderr 容器記錄

- 叢集中每個受監視容器的容器環境變數

- 已完成叢集中不需要監視的 Kubernetes 作業/pod

- Prometheus 計量的主動式抓取

- 收集 AKS 叢集中 Kubernetes 主要節點記錄的 [診斷記錄](../../aks/view-master-logs.md)，以分析主要元件所產生的記錄資料，例如 *kube-apiserver* 和 *kube-controller 管理員*。

## <a name="what-is-collected-from-kubernetes-clusters"></a>從 Kubernetes 叢集收集的內容

容器的 Azure 監視器包含一組預先定義的計量和清查專案，這些專案會在您的 Log Analytics 工作區中寫入為記錄資料。 依預設，所有列出的計量都會每分鐘收集一次。

### <a name="node-metrics-collected"></a>收集的節點計量

下列清單是每個所收集節點的24個度量：

- cpuUsageNanoCores
- cpuCapacityNanoCores
- cpuAllocatableNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryCapacityBytes
- memoryAllocatableBytes
- restartTimeEpoch
- 使用 (磁片) 
- 免費 (磁片) 
- used_percent (磁片) 
- io_time (diskio) 
- 寫入 (diskio) 
- 讀取 (diskio) 
- write_bytes (diskio) 
- write_time (diskio) 
- iops_in_progress (diskio) 
- read_bytes (diskio) 
- read_time (diskio) 
- err_in (net) 
- err_out (net) 
- bytes_recv (net) 
- bytes_sent (net) 
- Kubelet_docker_operations (Kubelet) 

### <a name="container-metrics"></a>容器計量

下列清單是每個收集的容器八個度量：

- cpuUsageNanoCores
- cpuRequestNanoCores
- cpuLimitNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryRequestBytes
- memoryLimitBytes
- restartTimeEpoch

### <a name="cluster-inventory"></a>叢集清查

下列清單是預設收集的叢集清查資料：

- KubePodInventory –每個容器1分鐘
- KubeNodeInventory –每個節點1分鐘
- KubeServices –每個服務每分鐘1個
- ContainerInventory –每分鐘每個容器1個

## <a name="estimating-costs-to-monitor-your-aks-cluster"></a>預估監視 AKS 叢集的成本

下列預估是以使用下列大小調整範例的 Azure Kubernetes Service (AKS) 叢集為基礎。 此外，預估值僅適用于所收集的計量和清查資料。 針對 (stdout、stderr 和環境變數) 的容器記錄，它會根據工作負載所產生的記錄大小而有所不同，並從我們的估計中排除它們。

如果您已啟用監視的 AKS 叢集，其設定如下所示。

- 三個節點
- 每個節點兩個磁片
- 每個節點一個網路介面
- 20個 pod (每個 pod 中有一個容器 = 總) 20 個容器
- 兩個 Kubernetes 命名空間
- 五個 Kubernetes 服務 (包括 kube 系統 pod、服務和命名空間) 
- 收集頻率 = 60 秒 (預設) 

您可以在指派的 Log Analytics 工作區中，看到每小時產生的資料表和資料量。 如需每個資料表的詳細資訊，請參閱 [容器記錄](container-insights-log-search.md#container-records)。

|資料表 | 大小估計 (MB/小時)  |
|------|---------------|
|Perf | 12.9 |
|InsightsMetrics | 11.3 |
|KubePodInventory | 1.5 |
|KubeNodeInventory | 0.75 |
|KubeServices | 0.13 |
|ContainerInventory | 3.6 |
|KubeHealth | 0.1 |
|KubeMonAgentEvents |0.005 |

總計 = 31 MB/小時 = 23.1 GB/月 (一個月 = 31 天) 

使用 Log Analytics 的預設 [定價](https://azure.microsoft.com/pricing/details/monitor/) ，也就是隨用隨付模型，您可以估計每月的 Azure 監視器成本。 包含容量保留之後，視選取的保留而定，每個月的價格會較高。

## <a name="controlling-ingestion-to-reduce-cost"></a>控制內嵌以降低成本

假設您的組織不同業務單位共用 Kubernetes 基礎結構和 Log Analytics 工作區的案例。 每個業務單位都以 Kubernetes 命名空間分隔。 您可以使用 [**視圖活頁簿**] 下拉式清單中提供的 [**資料使用狀況**] runbook，將每個工作區中內嵌的資料量視覺化。

[![查看活頁簿下拉式清單](media/container-insights-cost/workbooks-dropdown.png)](media/container-insights-cost/workbooks-dropdown.png#lightbox)


此活頁簿可協助您以視覺化方式呈現資料的來源，而不需要建立您自己的查詢程式庫，使其在檔中分享。 在此活頁簿中，您可以使用圖表來查看來自這類透視圖的可計費資料，如下所示：

- 依解決方案的計費資料內嵌（GB）總計
- 容器記錄所內嵌的可計費資料 (應用程式記錄檔) 
- 依 Kubernetes 命名空間的可計費容器記錄資料內嵌
- 依叢集名稱隔離的可計費容器記錄資料內嵌
- 依 logsource 專案內嵌的可計費容器記錄資料
- 診斷主要節點記錄所內嵌的可計費診斷資料

[![數據使用量活頁簿](media/container-insights-cost/data-usage-workbook.png)](media/container-insights-cost/data-usage-workbook.png#lightbox)

若要瞭解如何管理活頁簿的許可權和許可權，請參閱 [存取控制](../platform/workbooks-access-control.md)。

完成分析之後，若要判斷哪些來源或來源產生的資料超過您的需求，您可以重新設定資料收集。 如需設定 stdout、stderr 和環境變數集合的詳細資訊，請參閱 [設定代理程式資料收集設定](container-insights-agent-config.md) 一文。

以下是您可以藉由修改 ConfigMap 檔來協助控制成本，將哪些變更套用至叢集的範例。

1. 藉由在 ConfigMap 檔中修改下列內容，在叢集中的所有命名空間停用 stdout 記錄：

    ```
    [log_collection_settings]       
       [log_collection_settings.stdout]          
          enabled = false
    ```

2. 停用從開發命名空間收集 stderr 記錄 (例如， **開發/測試**) ，然後繼續從其他命名空間收集 stderr 記錄 (例如，在 ConfigMap 檔中修改下列內容，以繼續從其他命名空間收集 stderr 記錄檔例如， **生產** 和 **預設**) ：

    >[!NOTE]
    >預設會停用 kube 系統記錄收集。 預設設定會保留下來，將「 **開發/測試** 」命名空間新增至「排除」命名空間清單會套用至 stderr 記錄檔收集。

    ```
    [log_collection_settings.stderr]          
       enabled = true          
          exclude_namespaces = ["kube-system", "dev-test"]
    ```

3. 藉由在 ConfigMap 檔中修改下列各項，在整個叢集中停用環境變數集合。 這適用于每個 Kubernetes 命名空間中的所有容器。 

    ```
    [log_collection_settings.env_var]
        enabled = false
    ```

4. 若要清除已完成的作業，請在 ConfigMap 檔案中修改下列內容，以指定工作定義中的清除原則：

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: pi-with-ttl
    spec:
      ttlSecondsAfterFinished: 100
    ```

將其中一項或多項變更套用至您的 ConfigMaps 之後，請參閱套用 [已更新的 ConfigMap](container-insights-prometheus-integration.md#applying-updated-configmap) ，以將其套用至您的叢集。

### <a name="prometheus-metrics-scraping"></a>Prometheus 計量抓取

如果您使用的是 [Prometheus 計量抓取](container-insights-prometheus-integration.md)，請務必考慮下列各項，以限制您從叢集收集的計量數目：

- 確定抓取頻率設定為優化 (預設值為60秒) 。 雖然您可以將頻率增加到15秒，但您必須確保抓取的計量會以該頻率發佈。 否則，將會有許多重複的計量剪輯並傳送至您的 Log Analytics 工作區，並在其中新增至資料內嵌和保留成本，但值較低。 

- 容器的 Azure 監視器支援依計量名稱排除 & 包含清單。 例如，如果您是在叢集中抓取 **kubedns** 計量，則可能會有數百個這些計量依預設會取得剪輯，但您很可能只對子集感興趣。 確認您已指定要抓取的計量清單，或排除其他專案，以儲存在資料內嵌磁片區上。 您可以輕鬆地啟用抓取，而不使用許多計量，這些計量只會在您的 Log Analytics 帳單中增加額外費用。

- 透過 pod 注釋抓取時，請確定您依命名空間篩選，以便從不 (使用的命名空間中排除 pod 計量的抓取（例如， **開發/測試** 命名空間) ）。

## <a name="next-steps"></a>後續步驟

如需有關如何從使用容器的 Azure 監視器收集的資料，瞭解最新使用模式所需成本的詳細資訊，請參閱 [管理您的使用量和估計成本](../platform/manage-cost-storage.md)。