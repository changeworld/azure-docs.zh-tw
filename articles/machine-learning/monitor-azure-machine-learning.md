---
title: 監視 Azure 機器學習 |微軟文檔
description: 瞭解如何使用 Azure 監視器查看、分析和創建 Azure 機器學習指標上的警報。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.openlocfilehash: eb4f46322bec57fb4412d3ddebb345640556ca5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399110"
---
# <a name="monitoring-azure-machine-learning"></a>監視 Azure 機器學習

本文介紹了 Azure 機器學習生成的監視資料。 它還介紹了如何使用 Azure 監視器分析資料和定義警報。

> [!TIP]
> 本文檔中的資訊主要面向管理員，因為它描述了 Azure 機器學習的監視。 如果您是資料科學家或開發人員，並且想要監視特定于模型訓練運行的資訊，請參閱以下文檔：
>
> * [啟動、監視和取消培訓運行](how-to-manage-runs.md)
> * [記錄訓練執行的計量](how-to-track-experiments.md)
> * [使用 MLflow 追蹤實驗](how-to-use-mlflow.md)
> * [使用 TensorBoard 執行視覺化](how-to-monitor-tensorboard.md)

## <a name="azure-monitor"></a>Azure 監視器

Azure 機器學習使用 Azure 監視器記錄監視資料，Azure 監視器是 Azure 中的完整堆疊監視服務。 Azure 監視器提供了一組完整的功能來監視 Azure 資源。 它還可以監視其他雲和本地的資源。

從 Azure[監視器概述](/azure/azure-monitor/overview)一開始，它提供了監視功能的概述。 以下各節通過提供將 Azure 監視器與 Azure 機器學習一起使用的詳細資訊來構建此資訊。

要瞭解與 Azure 監視器關聯的成本，請參閱[使用方式和估計成本](/azure/azure-monitor/platform/usage-estimated-costs)。 要瞭解資料在 Azure 監視器中顯示所需的時間，請參閱[日誌資料引入時間](/azure/azure-monitor/platform/data-ingestion-time)。

## <a name="monitoring-data-from-azure-machine-learning"></a>監視 Azure 機器學習中的資料

Azure 機器學習收集與其他 Azure 資源相同的監視資料，這些監視資料在[監視 Azure 資源的資料](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data)中描述。 有關 Azure 機器學習創建的日誌和指標的詳細資訊，請參閱[Azure 機器學習監視資料引用](monitor-resource-reference.md)。

## <a name="analyzing-metric-data"></a>分析指標資料

您可以通過從**Azure 監視器**功能表中打開指標來分析 Azure 機器學習**的指標**。 有關使用此工具的詳細資訊[，請參閱使用 Azure 指標資源管理器入門](/azure/azure-monitor/platform/metrics-getting-started)。

Azure 機器學習的所有指標都在命名空間**機器學習服務工作區**中。

![選擇機器學習服務工作區的指標資源管理器](./media/monitor-azure-machine-learning/metrics.png)

### <a name="filtering-and-splitting"></a>篩選和拆分

對於支援維度的指標，可以使用維度值應用篩選器。 例如，篩選的**群集名稱**為`cpu-cluster`**的"活動核心**"。 

還可以按維度拆分指標，以視覺化指標的不同段彼此比較的方式。 例如，拆分**管道步驟類型**以查看管道中使用的步驟類型的計數。

有關篩選和拆分的詳細資訊，請參閱 Azure[監視器 的高級功能](/azure/azure-monitor/platform/metrics-charts)。

## <a name="alerts"></a>警示

您可以通過從**Azure 監視器**功能表打開**警報**來訪問 Azure 機器學習的警報。 有關[創建警報的詳細資訊，請參閱使用 Azure 監視器創建、查看和管理指標警報](/azure/azure-monitor/platform/alerts-metric)。

下表列出了 Azure 機器學習的常見和推薦的指標警報規則：

| 警示類型 | 條件 | 描述 |
|:---|:---|:---|
| 模型部署失敗 | 聚合類型：總計，運算子：大於，閾值：0 | 當一個或多個模型部署失敗時 |
| 配額利用率百分比 | 聚合類型：平均值，運算子：大於閾值：90| 當配額利用率大於 90% 時 |
| 無法使用的節點 | 聚合類型：總計，運算子：大於，閾值：0 | 當存在一個或多個不可用的節點時 |

## <a name="configuration"></a>組態

> [!IMPORTANT]
> __Azure 機器學習的指標不需要配置__，它們會自動收集，並且可在指標資源管理器中用於監視和警報。

您可以添加診斷設置來配置以下功能：

* 將日誌和指標資訊存檔到 Azure 存儲帳戶。
* 將日誌和指標資訊資料流到 Azure 事件中心。
* 將日誌和指標資訊發送到 Azure 監視器日誌分析。

啟用這些設置需要額外的 Azure 服務（存儲帳戶、事件中心或日誌分析），這可能會增加您的成本。 要計算估計成本，請訪問[Azure 定價計算機](https://azure.microsoft.com/pricing/calculator)。

有關創建診斷設置的詳細資訊，請參閱[創建診斷設置以在 Azure 中收集平臺日誌和指標](/azure/azure-monitor/platform/diagnostic-settings)。

您可以為 Azure 機器學習配置以下日誌：

| 類別 | 描述 |
|:---|:---|
| Aml計算集群事件 | Azure 機器學習計算群集中的事件。 |
| Aml 計算叢集節點事件 | 來自 Azure 機器學習計算群集中節點的事件。 |
| 阿姆計算作業事件 | 在 Azure 機器學習計算上運行的作業的事件。 |

> [!NOTE]
> 在診斷設置中啟用指標時，維度資訊當前不會包含在發送到存儲帳戶、事件中心或日誌分析的資訊中。

## <a name="analyzing-log-data"></a>分析日誌資料

使用 Azure 監視器日誌分析需要創建診斷配置並啟用__向日志分析發送資訊__。 有關詳細資訊，請參閱[配置](#configuration)部分。

Azure 監視器日誌中的資料存儲在表中，每個表都有自己的一組唯一屬性。 Azure 機器學習將資料存儲在下表中：

| Table | 描述 |
|:---|:---|
| Aml計算集群事件 | Azure 機器學習計算群集中的事件。 |
| Aml 計算叢集節點事件 | 來自 Azure 機器學習計算群集中節點的事件。 |
| 阿姆計算作業事件 | 在 Azure 機器學習計算上運行的作業的事件。 |

> [!IMPORTANT]
> 從 Azure 機器學習功能表中選擇 **"日誌"** 時，將打開日誌分析，查詢範圍設置為當前工作區。 這意味著日誌查詢將僅包括來自該資源的資料。 如果要運行包含其他資料庫的資料或其他 Azure 服務的資料的查詢，請選擇**Azure 監視器**功能表中的 **"日誌**"。 有關詳細資訊，請參閱[Azure 監視器日誌分析中的日誌查詢範圍和時間範圍](/azure/azure-monitor/log-query/scope/)。

有關日誌和指標的詳細資訊，請參閱[Azure 機器學習監視資料引用](monitor-resource-reference.md)。

### <a name="sample-queries"></a>範例查詢

以下是可用於説明監視 Azure 機器學習資源的查詢： 

+ 獲取過去五天內失敗的作業：

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ 獲取特定作業名稱的記錄：

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ 獲取 VM 大小為Standard_D1_V2的群集的最後五天內的群集事件：

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ 獲取過去八天內分配的節點：

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="next-steps"></a>後續步驟

- 有關日誌和指標的引用，請參閱 Azure[機器學習監視資料引用](monitor-resource-reference.md)。
- 有關使用與 Azure 機器學習相關的配額的資訊，請參閱[管理和請求 Azure 資源的配額](how-to-manage-quotas.md)。
- 有關監視 Azure 資源的詳細資訊，請參閱[使用 Azure 監視器監視 Azure 資源](/azure/azure-monitor/insights/monitor-azure-resource)。
