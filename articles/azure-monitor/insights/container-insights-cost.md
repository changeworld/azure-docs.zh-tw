---
title: 容器 Azure 監視器的監視成本 |Microsoft Docs
description: 本文說明針對容器 Azure 監視器所收集的計量 & 清查資料的監視成本，以協助客戶管理其使用量和相關成本。
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: a03e94fa7650c56a4d3b3beda3c27283329aebbe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84204645"
---
# <a name="understand-monitoring-costs-for-azure-monitor-for-containers"></a>瞭解容器 Azure 監視器的監視成本

本文提供容器 Azure 監視器的定價指導方針，以協助您瞭解下列各項：

* 如何在啟用此深入解析之前，預先預估成本

* 如何在為一或多個容器啟用容器 Azure 監視器之後測量成本

* 如何控制資料收集並降低成本

Azure 監視器記錄檔會收集、編制索引，並儲存 Kubernetes 叢集所產生的資料。 

Azure 監視器計價模式主要是根據您 Log Analytics 工作區中每天內嵌的資料量（gb）。 Log Analytics 工作區的成本不只是收集的資料量，也取決於選取的方案，以及您選擇用來儲存從叢集產生之資料的時間長度。

>[!NOTE]
>所有大小和定價僅適用于範例估計。 請根據您的 Azure 監視器 Log Analytics 定價模式和 Azure 區域，參閱 Azure 監視器[定價](https://azure.microsoft.com/pricing/details/monitor/)頁面以取得最新的定價。

以下摘要說明從 Kubernetes 叢集收集哪些類型的資料，以及會影響成本並可根據您的使用量自訂的容器 Azure 監視器：

- Stdout，在叢集中每個 Kubernetes 命名空間內每個受監視容器的 stderr 容器記錄

- 叢集中每個受監視容器的容器環境變數

- 叢集中的已完成 Kubernetes 作業/pod 不需要監視

- Prometheus 計量的主動式抓取

- AKS 叢集中 Kubernetes 主要節點記錄的[診斷記錄集合](../../aks/view-master-logs.md)，用來分析主要元件（例如*kube-kube-apiserver*和*kube 控制器管理員*）所產生的記錄資料。

## <a name="what-is-collected-from-kubernetes-clusters"></a>從 Kubernetes 叢集收集的內容

適用于容器的 Azure 監視器包含一組預先定義的計量和清查專案，並在 Log Analytics 工作區中寫入為記錄資料。 依預設，每隔一分鐘會收集下列所有計量。

### <a name="node-metrics-collected"></a>已收集的節點計量

下列清單是所收集的每個節點24個計量：

- cpuUsageNanoCores
- cpuCapacityNanoCores
- cpuAllocatableNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryCapacityBytes
- memoryAllocatableBytes
- restartTimeEpoch
- 已使用（磁片）
- 免費（磁片）
- used_percent （磁片）
- io_time （diskio）
- 寫入（diskio）
- 讀取（diskio）
- write_bytes （diskio）
- write_time （diskio）
- iops_in_progress （diskio）
- read_bytes （diskio）
- read_time （diskio）
- err_in （net）
- err_out （net）
- bytes_recv （net）
- bytes_sent （net）
- Kubelet_docker_operations （Kubelet）

### <a name="container-metrics"></a>容器計量

下列清單是每個收集的容器八個計量：

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

- KubePodInventory –每個容器每分鐘1個
- KubeNodeInventory –每個節點每分鐘1個
- KubeServices –每個服務每分鐘1個
- ContainerInventory –每個容器每分鐘1個

## <a name="estimating-costs-to-monitor-your-aks-cluster"></a>估計監視 AKS 叢集的成本

下列估計是以具有下列大小調整範例的 Azure Kubernetes Service （AKS）叢集為基礎。 此外，預估僅適用于所收集的計量和清查資料。 針對容器記錄（stdout、stderr 和環境變數），它會根據工作負載所產生的記錄大小而有所不同，而且會從我們的估計中排除。

如果您啟用了 AKS 叢集的監視，其設定如下所示：

- 三個節點
- 每個節點兩個磁片
- 每個節點一個網路介面
- 20個 pod （每個 pod 中一個容器 = 總計20個容器）
- 兩個 Kubernetes 命名空間
- 五個 Kubernetes 服務（包括 kube 系統 pod、服務和命名空間）
- 收集頻率 = 60 秒（預設值）

您可以在指派的 Log Analytics 工作區中查看每小時產生的資料表和資料量。 如需每個資料表的詳細資訊，請參閱[容器記錄](container-insights-log-search.md#container-records)。

|資料表 | 估計大小（MB/小時） |
|------|---------------|
|Perf | 12.9 |
|InsightsMetrics | 11.3 |
|KubePodInventory | 1.5 |
|KubeNodeInventory | 0.75 |
|KubeServices | 0.13 |
|ContainerInventory | 3.6 |
|KubeHealth | 0.1 |
|KubeMonAgentEvents |0.005 |

總計 = 31 MB/小時 = 23.1 GB/月（一個月 = 31 天）

使用 Log Analytics 的預設[定價](https://azure.microsoft.com/pricing/details/monitor/)，這是隨用隨付模型，您可以預估每月 Azure 監視器成本。 包含容量保留之後，根據選取的保留期，每月價格會較高。

## <a name="controlling-ingestion-to-reduce-cost"></a>控制內嵌以降低成本

假設您的組織有不同的業務單位共用 Kubernetes 基礎結構和 Log Analytics 工作區。 每個業務單位都以 Kubernetes 命名空間分隔。 您可以使用最近發行的活頁簿，以視覺化方式在每個工作區中內嵌多少資料。 [活頁[簿] 圖庫](../platform/workbooks-overview.md#getting-started)中的 [**容器深入解析使用量**] 活頁簿可協助您將資料的來源視覺化，而不需要從我們在檔中共用的內容中建立自己的查詢程式庫。 在此活頁簿中，有一些圖表可讓您從這類觀點查看計費資料，如下所示：

- 解決方案的計費資料內嵌總計（GB）

- 容器記錄所內嵌的可計費資料（應用程式記錄）

- 依 Kubernetes 命名空間內嵌的可計費容器記錄資料

- 依叢集名稱隔離的可計費容器記錄資料內嵌

- 依 logsource 專案內嵌的可計費容器記錄資料

- 診斷主要節點記錄所內嵌的可計費診斷資料

若要瞭解如何管理活頁簿的權利和許可權，請參閱[存取控制](../platform/workbooks-access-control.md)。

在完成分析以判斷哪些來源或哪些來源產生的資料超過您的需求時，您可以重新設定資料收集。 [設定代理程式資料收集設定](container-insights-agent-config.md)一文中會說明如何設定 stdout、stderr 和環境變數集合的詳細資料。

以下是您可以藉由修改 ConfigMap 檔來協助控制成本，而套用至叢集的變更範例。

1. 藉由在 ConfigMap 檔案中修改下列內容，停用叢集內所有命名空間的 stdout 記錄檔：

    ```
    [log_collection_settings]       
       [log_collection_settings.stdout]          
          enabled = false
    ```

2. 停用從開發命名空間（例如，**開發/測試**）收集 stderr 記錄，並在 ConfigMap 檔案中修改下列內容，繼續從其他命名空間（例如，**生產**和**預設**）收集 stderr 記錄：

    >[!NOTE]
    >預設會停用 kube 系統記錄集合。 預設設定會保留，並將**開發/測試**命名空間新增至排除命名空間清單，並套用到 stderr 記錄集合。

    ```
    [log_collection_settings.stderr]          
       enabled = true          
          exclude_namespaces = ["kube-system", "dev-test"]
    ```

3. 藉由在 ConfigMap 檔中修改下列各項，停用跨叢集的環境變數集合。 這適用于每個 Kubernetes 命名空間中的所有容器。 

    ```
    [log_collection_settings.env_var]
        enabled = false
    ```

4. 若要清除已完成的作業，請在 ConfigMap 檔案中修改下列內容，以在作業定義中指定清除原則：

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: pi-with-ttl
    spec:
      ttlSecondsAfterFinished: 100
    ```

將其中一或多項變更套用至您的 ConfigMaps 之後，請參閱套用[更新的 ConfigMap](container-insights-prometheus-integration.md#applying-updated-configmap)以將其套用到您的叢集。

### <a name="prometheus-metrics-scraping"></a>Prometheus 計量抓取

如果您使用的是[Prometheus 公制抓取](container-insights-prometheus-integration.md)，請務必考慮下列各項，以限制您從叢集收集的計量數目：

- 確保抓取頻率設定為優化（預設值為60秒）。 雖然您可以將頻率增加為15秒，但您必須確保您所抓取的計量會以該頻率發佈。 否則，將會有許多重複的計量剪輯並傳送到您的 Log Analytics 工作區，並以增加到資料內嵌和保留成本的間隔，但其價值較低。 

- 容器的 Azure 監視器支援依計量名稱排除 & 包含清單。 例如，如果您要在叢集中抓取**kubedns**計量，預設可能會有數百個會取得剪輯，但您很可能只對子集感興趣。 請確認您已指定要抓取的計量清單，或排除其他專案，但不包括少數，以儲存在資料內嵌磁片區上。 您可以輕鬆地啟用抓取，而不使用其中許多計量，這只會將額外費用新增至您的 Log Analytics 帳單。

- 透過 pod 注釋抓取時，請務必依照命名空間進行篩選，以便從您不使用的命名空間（例如，**開發/測試**命名空間）排除 pod 計量的抓取。

## <a name="next-steps"></a>後續步驟

如需瞭解如何根據最近的使用模式，從以容器的 Azure 監視器收集的資料來瞭解可能產生的成本，請參閱[管理您的使用量和估計成本](../platform/manage-cost-storage.md)。