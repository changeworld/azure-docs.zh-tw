---
title: 監視 Azure Machine Learning |Microsoft Docs
description: 瞭解如何使用 Azure 監視器來查看、分析和建立 Azure Machine Learning 計量的警示。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/01/2020
ms.openlocfilehash: 183d8b415ccb35d22dcc23ccf11d0707ad0778dd
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97963650"
---
# <a name="monitor-azure-machine-learning"></a>監視 Azure Machine Learning

當您有依賴 Azure 資源的重要應用程式和商務程序時，您會想要監視這些資源的可用性、效能和操作。 本文說明 Azure Machine Learning 所產生的監視資料，以及如何使用 Azure 監視器分析和警示此資料。

> [!TIP]
> 本檔中的資訊主要適用于系統 __管理員__，因為它描述 Azure Machine Learning 服務和相關聯的 Azure 服務的監視。 如果您是 __資料科學家__ 或 __開發人員__，而且想要監視 *模型定型執行* 的特定資訊，請參閱下列檔：
>
> * [啟動、監視及取消定型回合](how-to-manage-runs.md)
> * [記錄訓練執行的計量](how-to-track-experiments.md)
> * [使用 MLflow 追蹤實驗](how-to-use-mlflow.md)
> * [使用 TensorBoard 執行視覺化](how-to-monitor-tensorboard.md)
>
> 如果您想要監視部署為 web 服務或 IoT Edge 模組的模型所產生的資訊，請參閱[使用 Application Insights](how-to-enable-app-insights.md)[收集模型資料](how-to-enable-data-collection.md)和監視。

## <a name="what-is-azure-monitor"></a>Azure 監視器是什麼？

Azure Machine Learning 使用 [Azure 監視器](../azure-monitor/overview.md)來建立監視資料，這是 Azure 中的完整堆疊監視服務。 Azure 監視器提供一組完整的功能來監視您的 Azure 資源。 它也可以監視其他雲端和內部部署中的資源。

請從 [使用 Azure 監視器來監視 Azure 資源](../azure-monitor/insights/monitor-azure-resource.md)的文章開始，其中說明下列概念：

- Azure 監視器是什麼？
- 與監視相關聯的成本
- 在 Azure 中收集的監視資料
- 設定資料收集
- Azure 中用來分析和警示監視資料的標準工具

下列各節將說明針對 Azure Machine Learning 所收集的特定資料，在本文中建立。 這些章節也會提供範例，說明如何使用 Azure 工具來設定資料收集和分析此資料。

> [!TIP]
> 若要瞭解與 Azure 監視器相關聯的成本，請參閱 [使用量和估計成本](../azure-monitor/platform/usage-estimated-costs.md)。 若要瞭解您的資料在 Azure 監視器中顯示所花費的時間，請參閱 [記錄資料內嵌時間](../azure-monitor/platform/data-ingestion-time.md)。

## <a name="monitoring-data-from-azure-machine-learning"></a>監視 Azure Machine Learning 的資料

Azure Machine Learning 會收集與 [azure 資源監視資料](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)中所述的其他 Azure 資源相同的監視資料類型。 

如需 Azure Machine Learning 所建立之記錄和計量的詳細參考，請參閱 [Azure Machine Learning 監視資料參考](monitor-resource-reference.md) 。

<a id="configuration"></a>

## <a name="collection-and-routing"></a>收集和路由

系統會自動收集和儲存平臺計量和活動記錄檔，但是可以使用診斷設定將其路由至其他位置。  

在您建立診斷設定並將其路由傳送至一或多個位置之前，不會收集並儲存資源記錄。

如需使用 Azure 入口網站、CLI 或 PowerShell 建立診斷設定的詳細程式，請參閱 [建立診斷設定以收集 Azure 中的平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md) 。 當您建立診斷設定時，可以指定要收集的記錄類別。 Azure Machine Learning 的類別列在 [Azure Machine Learning 監視資料參考](monitor-resource-reference.md#resource-logs)中。

> [!IMPORTANT]
> 若要啟用這些設定，需要額外的 Azure 服務 (儲存體帳戶、事件中樞或 Log Analytics) ，這可能會增加您的成本。 若要計算估計成本，請造訪 [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator)。

您可以為 Azure Machine Learning 設定下列記錄：

| 類別 | 描述 |
|:---|:---|
| AmlComputeClusterEvent | Azure Machine Learning 計算叢集的事件。 |
| AmlComputeClusterNodeEvent | Azure Machine Learning 計算叢集中節點的事件。 |
| AmlComputeJobEvent | Azure Machine Learning 計算上執行之作業的事件。 |

> [!NOTE]
> 當您在診斷設定中啟用計量時，維度資訊目前不會包含在傳送至儲存體帳戶、事件中樞或 log analytics 的資訊中。

下列各節將討論您可以收集的計量和記錄。

## <a name="analyzing-metrics"></a>分析計量

您可以從 [ **Azure 監視器**] 功能表開啟 **計量**，以分析 Azure Machine Learning 的計量，以及來自其他 Azure 服務的度量。 如需使用此工具的詳細資訊，請參閱[開始使用 Azure 計量瀏覽器](../azure-monitor/platform/metrics-getting-started.md)。

如需所收集的平臺度量清單，請參閱 [監視 Azure Machine Learning 資料參考度量](monitor-resource-reference.md#metrics)。

Azure Machine Learning 的所有計量都在命名空間 **Machine Learning 服務工作區** 中。

![已選取 Machine Learning 服務工作區的計量瀏覽器](./media/monitor-azure-machine-learning/metrics.png)

如需參考，您可以查看 [Azure 監視器中支援的所有資源計量](../azure-monitor/platform/metrics-supported.md)清單。

> [!TIP]
> Azure 監視器計量資料可供90天使用。 不過，在建立圖表時，只能視覺化30天。 例如，如果您想要將90天期間視覺化，您必須在90天期間內將它分成30天的三個圖表。
### <a name="filtering-and-splitting"></a>篩選和分割

針對支援維度的計量，您可以使用維度值來套用篩選。 例如，針對的叢集 **名稱** 篩選使用中 **核心** `cpu-cluster` 。 

您也可以依維度分割計量，以視覺化不同的度量區段與彼此的比較。 例如，分割 **管線步驟類型** 以查看管線中所使用之步驟類型的計數。

如需篩選和分割的詳細資訊，請參閱 [Azure 監視器的 Advanced 功能](../azure-monitor/platform/metrics-charts.md)。

<a id="analyzing-log-data"></a>
## <a name="analyzing-logs"></a>分析記錄

使用 Azure 監視器 Log Analytics 需要您建立診斷設定，並啟用 __將資訊傳送至 Log analytics__。 如需詳細資訊，請參閱 [收集和路由](#collection-and-routing) 區段。

Azure 監視器記錄檔中的資料會儲存在資料表中，每個資料表都有自己的唯一屬性集。 Azure Machine Learning 會將資料儲存在下列資料表中：

| Table | 描述 |
|:---|:---|
| AmlComputeClusterEvent | Azure Machine Learning 計算叢集的事件。 |
| AmlComputeClusterNodeEvent | Azure Machine Learning 計算叢集中節點的事件。 |
| AmlComputeJobEvent | Azure Machine Learning 計算上執行之作業的事件。 |

> [!IMPORTANT]
> 當您從 [Azure Machine Learning] 功能表選取 [ **記錄** ] 時，會開啟 Log Analytics，並將查詢範圍設定為目前的工作區。 這表示記錄查詢只會包含來自該資源的資料。 如果您想要執行包含其他資料庫資料或其他 Azure 服務資料的查詢，請從 [Azure 監視器] 功能表中選取 [記錄]。 如需詳細資訊，請參閱 [Azure 監視器 Log Analytics 中的記錄查詢範圍和時間範圍](../azure-monitor/log-query/scope.md)。

如需記錄和計量的詳細參考，請參閱 [Azure Machine Learning 監視資料參考](monitor-resource-reference.md)。

### <a name="sample-kusto-queries"></a>範例 Kusto 查詢

> [!IMPORTANT]
> 當您從 [服務-名稱] 功能表選取 **記錄** 時，會開啟 Log Analytics，並將查詢範圍設定為目前的 Azure Machine Learning 工作區。 這表示記錄查詢只會包含來自該資源的資料。 如果您想要執行包含來自其他工作區或來自其他 Azure 服務之資料的查詢，請從 [ **Azure 監視器**] 功能表中選取 [**記錄**]。 如需詳細資訊，請參閱 [Azure 監視器 Log Analytics 中的記錄查詢範圍和時間範圍](../azure-monitor/log-query/scope.md)。

以下是您可以用來協助監視 Azure Machine Learning 資源的查詢： 

+ 在過去五天內取得失敗的作業：

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ 取得特定作業名稱的記錄：

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ 針對 VM 大小為 Standard_D1_V2 的叢集，在過去五天內取得叢集事件：

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ 取得過去八天內配置的節點：

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="alerts"></a>警示

您可以從 [ **Azure 監視器**] 功能表開啟 **警示**，以存取 Azure Machine Learning 的警示。 如需建立警示的詳細資訊，請參閱 [使用 Azure 監視器建立、查看和管理計量警示](../azure-monitor/platform/alerts-metric.md) 。

下表列出 Azure Machine Learning 的常見和建議的度量警示規則：

| 警示類型 | 條件 | 描述 |
|:---|:---|:---|
| 失敗的模型部署 | 匯總類型：總計、運算子：大於、臨界值：0 | 當一或多個模型部署失敗時 |
| 配額使用率百分比 | 匯總類型：平均、運算子：大於、臨界值：90| 當配額使用率百分比大於90% 時 |
| 無法使用的節點 | 匯總類型：總計、運算子：大於、臨界值：0 | 當有一或多個無法使用的節點時 |

## <a name="next-steps"></a>後續步驟

- 如需記錄和計量的參考，請參閱 [監視 Azure Machine Learning 資料參考](monitor-resource-reference.md)。
- 如需使用與 Azure Machine Learning 相關之配額的詳細資訊，請參閱 [管理和要求 Azure 資源的配額](how-to-manage-quotas.md)。
- 如需監視 Azure 資源的詳細資訊，請參閱 [使用 Azure 監視器監視 azure 資源](../azure-monitor/insights/monitor-azure-resource.md)。
