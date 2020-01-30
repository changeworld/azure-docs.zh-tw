---
title: 監視 Azure Machine Learning |Microsoft Docs
description: 瞭解如何使用 Azure 監視器來查看、分析 Azure Machine Learning 的計量並建立警示。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 11/04/2019
ms.openlocfilehash: c35afe6e3d6ec74e8fc7cd134f533d1e618c820e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845734"
---
# <a name="monitoring-azure-machine-learning"></a>監視 Azure Machine Learning

本文說明 Azure Machine Learning 所產生的監視資料。 它也會說明如何使用 Azure 監視器來分析您的資料並定義警示。

> [!TIP]
> 本檔中的資訊主要是針對系統管理員，因為它會說明 Azure Machine Learning 的監視。 如果您是資料科學家或開發人員，而且想要監視模型定型執行的特定資訊，請參閱下列檔：
>
> * [啟動、監視和取消訓練執行](how-to-manage-runs.md)
> * [定型執行的記錄計量](how-to-track-experiments.md)
> * [使用 MLflow 追蹤實驗](how-to-use-mlflow.md)
> * [使用 TensorBoard 視覺化執行](how-to-monitor-tensorboard.md)

## <a name="azure-monitor"></a>Azure Monitor

Azure Machine Learning 使用 Azure 監視器來記錄監視資料，這是 Azure 中的完整堆疊監視服務。 Azure 監視器提供一組完整的功能來監視您的 Azure 資源。 它也可以監視其他雲端和內部部署中的資源。

從[Azure 監視器總覽](/azure/azure-monitor/overview)一文開始，其中提供監視功能的總覽。 下列各節會藉由提供搭配 Azure Machine Learning 使用 Azure 監視器的細節，來建立這項資訊。

若要瞭解與 Azure 監視器相關聯的成本，請參閱[使用量和估計成本](/azure/azure-monitor/platform/usage-estimated-costs)。 若要瞭解資料在 Azure 監視器中顯示所需的時間，請參閱[記錄資料內嵌時間](/azure/azure-monitor/platform/data-ingestion-time)。

## <a name="monitoring-data-from-azure-machine-learning"></a>監視 Azure Machine Learning 的資料

Azure Machine Learning 會收集與其他 Azure 資源相同的監視資料類型，如[從 Azure 資源監視資料](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data)中所述。 如需 Azure Machine Learning 所建立之記錄和計量的詳細參考，請參閱[Azure Machine Learning 監視資料參考](monitor-resource-reference.md)。

## <a name="analyzing-metric-data"></a>分析度量資料

您可以從 [ **Azure 監視器**] 功能表開啟 [**計量**]，來分析 Azure Machine Learning 的計量。 如需使用此工具的詳細資訊，請參閱[開始使用 Azure 計量瀏覽器](/azure/azure-monitor/platform/metrics-getting-started)。

Azure Machine Learning 的所有計量都在命名空間**Machine Learning 服務 工作區**中。

![已選取 Machine Learning 服務工作區的計量瀏覽器](./media/monitor-azure-machine-learning/metrics.png)

### <a name="filtering-and-splitting"></a>篩選和分割

針對支援維度的計量，您可以使用維度值來套用篩選。 例如，篩選 `cpu-cluster`的叢集**名稱**的使用中**核心**。 

您也可以依維度分割計量，以視覺化計量的不同區段彼此之間的比較。 例如，分割**管線步驟類型**，以查看管線中使用的步驟類型計數。

如需篩選和分割的詳細資訊，請參閱[Azure 監視器的 Advanced 功能](/azure/azure-monitor/platform/metrics-charts)。

## <a name="alerts"></a>警示

您可以從 [ **Azure 監視器**] 功能表開啟 [**警示**]，來存取 Azure Machine Learning 的警示。 如需建立警示的詳細資訊，請參閱[使用 Azure 監視器建立、查看和管理計量警示](/azure/azure-monitor/platform/alerts-metric)。

下表列出 Azure Machine Learning 的一般和建議計量警示規則：

| 警示類型 | 條件 | 說明 |
|:---|:---|:---|
| 模型部署失敗 | 匯總類型： Total、Operator：大於、臨界值：0 | 當一或多個模型部署失敗時 |
| 配額使用量百分比 | 匯總類型：平均值，運算子：大於，臨界值：90| 當配額使用率百分比大於90% 時 |
| 無法使用的節點 | 匯總類型： Total、Operator：大於、臨界值：0 | 當有一或多個無法使用的節點時 |

## <a name="configuration"></a>組態

> [!IMPORTANT]
> __不需要設定 Azure Machine Learning 的計量__，它們會自動收集，並可在計量瀏覽器中用來進行監視和警示。

您可以新增診斷設定以設定下列功能：

* 將記錄和計量資訊封存至 Azure 儲存體帳戶。
* 將記錄和計量資訊串流至 Azure 事件中樞。
* 將記錄和計量資訊傳送至 Azure 監視器 Log Analytics。

啟用這些設定需要額外的 Azure 服務（儲存體帳戶、事件中樞或 Log Analytics），這可能會增加您的成本。 若要計算預估成本，請造訪[Azure 定價計算機](https://azure.microsoft.com/pricing/calculator)。

如需有關建立診斷設定的詳細資訊，請參閱[建立診斷設定以收集 Azure 中的平臺記錄和計量](/azure/azure-monitor/platform/diagnostic-settings)。

您可以為 Azure Machine Learning 設定下列記錄：

| 類別 | 說明 |
|:---|:---|
| AmlComputeClusterEvent | 來自 Azure Machine Learning 計算叢集的事件。 |
| AmlComputeClusterNodeEvent | Azure Machine Learning 計算叢集中的節點事件。 |
| AmlComputeJobEvent | Azure Machine Learning 計算上執行之作業的事件。 |

> [!NOTE]
> 當您在診斷設定中啟用計量時，維度資訊目前不會包含在傳送至儲存體帳戶、事件中樞或 log analytics 的資訊中。

## <a name="analyzing-log-data"></a>分析記錄資料

使用 Azure 監視器 Log Analytics 需要您建立診斷設定，並啟用__將資訊傳送至 Log analytics__。 如需詳細資訊，請參閱[設定一節](#configuration)。

Azure 監視器記錄檔中的資料會儲存在資料表中，而且每個資料表都有一組專屬的唯一屬性。 Azure Machine Learning 會將資料儲存在下列資料表中：

| 表格 | 說明 |
|:---|:---|
| AmlComputeClusterEvent | 來自 Azure Machine Learning 計算叢集的事件。 |
| AmlComputeClusterNodeEvent | Azure Machine Learning 計算叢集中的節點事件。 |
| AmlComputeJobEvent | Azure Machine Learning 計算上執行之作業的事件。 |

> [!IMPORTANT]
> 當您從 [Azure Machine Learning] 功能表中選取 [**記錄**] 時，會開啟 Log Analytics，並將查詢範圍設定為目前的工作區。 這表示記錄查詢只會包含來自該資源的資料。 如果您想要執行的查詢包含來自其他資料庫的資料或來自其他 Azure 服務的資料，請從 [ **Azure 監視器**] 功能表中選取 [**記錄**]。 如需詳細資訊，請參閱[Azure 監視器 Log Analytics 中的記錄查詢範圍和時間範圍](/azure/azure-monitor/log-query/scope/)。

如需記錄和計量的詳細參考，請參閱[Azure Machine Learning 監視資料參考](monitor-resource-reference.md)。

### <a name="sample-queries"></a>範例查詢

以下是您可以用來協助您監視 Azure Machine Learning 資源的查詢： 

+ 在過去五天內取得失敗的作業：

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ 取得特定工作名稱的記錄：

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ 針對 Standard_D1_V2 VM 大小的叢集，在過去五天內取得叢集事件：

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

## <a name="next-steps"></a>後續步驟

- 如需記錄和計量的參考，請參閱[Azure Machine Learning 監視資料參考](monitor-resource-reference.md)。
- 如需使用與 Azure Machine Learning 相關之配額的詳細資訊，請參閱[管理和要求 Azure 資源的配額](how-to-manage-quotas.md)。
- 如需監視 Azure 資源的詳細資訊，請參閱[使用 Azure 監視器監視 azure 資源](/azure/azure-monitor/insights/monitor-azure-resource)。
