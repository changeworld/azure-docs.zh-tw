---
title: 分析和監視資料集上的資料漂移（預覽）
titleSuffix: Azure Machine Learning
description: 創建 Azure 機器學習資料集監視器（預覽）、監視資料集中的資料漂移以及設置警報。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/04/2019
ms.openlocfilehash: 401019c537cb0eb51fa6002637e170a79210f7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617641"
---
# <a name="detect-data-drift-preview-on-datasets"></a>檢測資料集上的資料漂移（預覽）
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何創建 Azure 機器學習資料集監視器（預覽）、監視資料漂移和資料集中的統計更改以及設置警報。

使用 Azure 機器學習資料集監視器，您可以：
* **分析資料中的漂移**，瞭解資料隨時間的變化。
* **監視模型資料**，以發現訓練資料集和服務資料集之間的差異。
* **監視新資料**，以發現任何基線資料集和目標資料集之間的差異。
* **資料中的設定檔要素**，用於跟蹤統計屬性隨時間的變化。
* **設置資料漂移警報，** 以便對潛在問題發出早期警告。 

指標和見解可通過與 Azure 機器學習工作區關聯的[Azure 應用程式見解](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)資源獲得。

> [!Important]
> 請注意，使用 SDK 監視資料漂移在所有版本中都可用，而通過網路工作室監視資料漂移僅是企業版。

## <a name="prerequisites"></a>Prerequisites

要創建和使用資料集監視器，您需要：
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。
* [Azure 機器學習工作區](how-to-manage-workspace.md)。
* [安裝的 Python 的 Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)，其中包括 azurem 資料集包。
* 具有在檔路徑、檔案名或資料列中指定的時間戳記的結構化（表格）資料。

## <a name="what-is-data-drift"></a>什麼是資料漂移？

在機器學習上下文中，資料漂移是模型輸入資料的變化，導致模型性能下降。 它是模型精度隨時間而降低的主要原因之一，因此監視資料漂移有助於檢測模型性能問題。

資料漂移的原因包括： 

- 上游過程變化，例如更換感應器，將測量單位從英寸更改為釐米。 
- 資料品質問題，例如感應器損壞時始終讀取 0。
- 資料中的自然漂移，如平均氣溫隨季節變化而變化。
- 特徵或協變數偏移之間的關係變化。 

借助 Azure 機器學習資料集監視器，您可以設置警報，以便隨著時間的推移在資料集中説明進行資料漂移檢測。 

### <a name="dataset-monitors"></a>資料集監視器 

您可以創建資料集監視器來檢測和警報資料在資料集中的新資料漂移，分析歷史資料以進行漂移，並隨著時間的推移分析新資料。 資料漂移演算法提供了資料變化的總體度量，並指示哪些功能負責進一步調查。 資料集監視器通過分析`timeseries`資料集中的新資料生成許多其他指標。 可以通過[Azure 應用程式見解](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)對監視器生成的所有指標設置自訂警報。 資料集監視器可用於快速捕獲資料問題，並通過識別可能的原因來縮短調試問題的時間。  

從概念上講，在 Azure 機器學習中設置資料集監視器有三種主要方案。

狀況 | 描述
---|---
監視模型的服務資料，以便從模型的訓練資料中漂移 | 此方案的結果可以解釋為監視模型準確性的代理，因為如果服務資料從訓練資料漂移，則模型精度會降低。
監視時間序列資料集，以尋找與上一時間段的漂移。 | 此方案更一般，可用於監視模型構建上游或下游的資料集。  目標資料集必須具有時間戳記列，而基線資料集可以是具有與目標資料集共有要素的任何表格資料集。
對過去的資料進行分析。 | 此方案可用於瞭解歷史資料，並在資料集監視器的設置中為決策提供資訊。

## <a name="how-dataset-can-monitor-data"></a>資料集如何監視資料

使用 Azure 機器學習，資料漂移通過資料集進行監視。 為了監視資料漂移，指定了基線資料集（通常是模型的訓練資料集）。 目標資料集（通常是模型輸入資料）會隨著時間的推移與基線資料集進行比較。 此比較意味著目標資料集必須指定時間戳記列。

### <a name="set-the-timeseries-trait-in-the-target-dataset"></a>在`timeseries`目標資料集中設置特徵

目標資料集需要通過指定資料中的`timeseries`列或從檔的路徑模式派生的虛擬列來設置其特徵。 這可以通過 Python SDK 或 Azure 機器學習工作室完成。 必須指定表示"細細微性"時間戳記的列才能向資料集`timeseries`添加特徵。 如果資料被劃分為包含時間資訊（如"_yyy/MM/dd}"）的資料夾結構，則可以通過路徑模式設置創建虛擬列，並將其設置為"粗粒"時間戳記，以提高時間序列功能的重要性。 

#### <a name="python-sdk"></a>Python SDK

類[`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-)的方法[`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-)定義資料集的時間戳記列。 

```python 
from azureml.core import Workspace, Dataset, Datastore

# get workspace object
ws = Workspace.from_config()

# get datastore object 
dstore = Datastore.get(ws, 'your datastore name')

# specify datastore paths
dstore_paths = [(dstore, 'weather/*/*/*/*/data.parquet')]

# specify partition format
partition_format = 'weather/{state}/{date:yyyy/MM/dd}/data.parquet'

# create the Tabular dataset with 'state' and 'date' as virtual columns 
dset = Dataset.Tabular.from_parquet_files(path=dstore_paths, partition_format=partition_format)

# assign the timestamp attribute to a real or virtual column in the dataset
dset = dset.with_timestamp_columns('date')

# register the dataset as the target dataset
dset = dset.register(ws, 'target')
```

有關使用資料集`timeseries`特徵的完整示例，請參閱[示例筆記本](https://aka.ms/azureml-tsd-notebook)或[資料集 SDK 文檔](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-)。

#### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

如果使用 Azure 機器學習工作室創建資料集，請確保資料的路徑包含時間戳記資訊，包含包含資料的所有子資料夾，並設置分區格式。 

在下面的示例中，獲取子資料夾*NoaaIsdFlorida/2019*下的所有資料，分區格式指定時間戳記的年份、月份和日。 

[![分區格式](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

在 **"架構"** 設置中，指定指定資料集中的虛擬列或實列的時間戳記列：

![時間戳記](./media/how-to-monitor-datasets/timestamp.png)

## <a name="dataset-monitor-settings"></a>資料集監視器設置

使用指定的時間戳記設置創建資料集後，即可配置資料集監視器。

各種資料集監視器設置分為三組：**基本資訊、監視器設置**和**回填設置**。

### <a name="basic-info"></a>基本資訊

此表包含用於資料集監視器的基本設置。

| 設定 | 描述 | 提示 | 可變動 | 
| ------- | ----------- | ---- | ------- | 
| 名稱 | 資料集監視器的名稱。 | | 否 |
| 基線資料集 | 用作隨時間比較目標資料集的基線的表格資料集。 | 基線資料集必須具有與目標資料集共有的功能。 通常，基線應設置為模型的訓練資料集或目標資料集的切片。 | 否 |
| 目標資料集 | 指定時間戳記列的表格資料集，用於分析資料漂移。 | 目標資料集必須具有與基線資料集共有的功能，並且應該是一個`timeseries`資料集，新資料將追加到該資料集中。 可以分析目標資料集中的歷史資料，也可以監視新資料。 | 否 | 
| 頻率 | 用於安排管道作業並分析運行回填時的歷史資料的頻率。 選項包括每日、每週或每月。 | 調整此設置以包括與基線相當的資料大小。 | 否 | 
| 特性 | 將分析隨時間漂移的資料的要素清單。 | 設置為模型的輸出特徵，以測量概念漂移。 不要包含隨時間自然漂移的要素（月、年、指數等）。 調整要素清單後，可以回填和現有資料漂移監視器。 | 是 | 
| 計算目標 | Azure 機器學習計算目標以運行資料集監視器作業。 | | 是 | 

### <a name="monitor-settings"></a>監視器設置

這些設置用於將創建的計畫資料集監視器管道。 

| 設定 | 描述 | 提示 | 可變動 | 
| ------- | ----------- | ---- | ------- |
| 啟用 | 在資料集監視器管道上啟用或禁用計畫 | 禁用計畫以使用回填設置分析歷史資料。 創建資料集監視器後，可以啟用它。 | 是 | 
| Latency | 資料到達資料集所需的時間（以小時表示） 例如，如果資料到達資料集封裝的 SQL DB 需要三天時間，則將延遲設置為 72。 | 創建資料集監視器後無法更改 | 否 | 
| 電子郵件地址 | 基於違反資料漂移百分比閾值的警報的電子郵件地址。 | 電子郵件通過 Azure 監視器發送。 | 是 | 
| 閾值 | 用於電子郵件警報的資料漂移百分比閾值。 | 可以在工作區關聯的應用程式見解資源中的許多其他指標上設置其他警報和事件。 | 是 | 

### <a name="backfill-settings"></a>回填設置

這些設置用於對資料漂移指標的過去資料運行回填。

| 設定 | 描述 | 提示 |
| ------- | ----------- | ---- |
| 開始日期 | 回填作業的開始日期。 | | 
| 結束日期 | 回填作業的結束日期。 | 結束日期不能超過從開始日期開始的 31+頻率時間單位。 在現有資料集監視器上，可以回填指標以分析歷史資料或使用更新的設置替換指標。 |

## <a name="create-dataset-monitors"></a>創建資料集監視器 

使用 Azure 機器學習工作室或 Python SDK 創建資料集監視器以檢測和警報新資料集上的資料漂移。 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

要在資料集監視器上設置警報，包含要為其創建監視器的資料集的工作區必須具有企業版功能。 

確認工作區功能後，導航到工作室的主頁，然後選擇左側的資料集選項卡。 選擇資料集監視器。

![監視器清單](./media/how-to-monitor-datasets/monitor-list.png)

按一下 **"創建監視器"** 按鈕，然後按一下"**下一步**"繼續嚮導。

![精靈](./media/how-to-monitor-datasets/wizard.png)

生成的資料集監視器將顯示在清單中。 選擇它以轉到該監視器的詳細資訊頁面。

### <a name="from-python-sdk"></a>從 Python SDK

有關詳細資訊，請參閱[有關資料漂移的 Python SDK 參考文檔](/python/api/azureml-datadrift/azureml.datadrift)。 

下面的示例演示如何使用 Python SDK 創建資料集監視器

```python
from azureml.core import Workspace, Dataset
from azureml.datadrift import DataDriftDetector
from datetime import datetime

# get the workspace object
ws = Workspace.from_config()

# get the target dataset
dset = Dataset.get_by_name(ws, 'target')

# set the baseline dataset
baseline = target.time_before(datetime(2019, 2, 1))

# set up feature list
features = ['latitude', 'longitude', 'elevation', 'windAngle', 'windSpeed', 'temperature', 'snowDepth', 'stationName', 'countryOrRegion']

# set up data drift detector
monitor = DataDriftDetector.create_from_datasets(ws, 'drift-monitor', baseline, target, 
                                                      compute_target='cpu-cluster', 
                                                      frequency='Week', 
                                                      feature_list=None, 
                                                      drift_threshold=.6, 
                                                      latency=24)

# get data drift detector by name
monitor = DataDriftDetector.get_by_name(ws, 'drift-monitor')

# update data drift detector
monitor = monitor.update(feature_list=features)

# run a backfill for January through May
backfill1 = monitor.backfill(datetime(2019, 1, 1), datetime(2019, 5, 1))

# run a backfill for May through today
backfill1 = monitor.backfill(datetime(2019, 5, 1), datetime.today())

# disable the pipeline schedule for the data drift detector
monitor = monitor.disable_schedule()

# enable the pipeline schedule for the data drift detector
monitor = monitor.enable_schedule()
```

有關設置`timeseries`資料集和資料漂移檢測器的完整示例，請參閱我們的[示例筆記本](https://aka.ms/datadrift-notebook)。

## <a name="understanding-data-drift-results"></a>瞭解資料漂移結果

資料監視器生成兩組結果：漂移概覽和功能詳細資訊。 以下動畫根據所選要素和指標顯示可用的漂移監視器圖表。 

![演示視頻](./media/how-to-monitor-datasets/video.gif)

### <a name="drift-overview"></a>漂移概述

**漂移概述**部分包含有關資料漂移程度以及應進一步調查哪些要素的頂級見解。 

| 計量 | 描述 | 提示 | 
| ------ | ----------- | ---- | 
| 資料漂移幅度 | 給定為基線和目標資料集之間的百分比。 從 0 到 100，其中 0 表示相同的資料集，100 表示 Azure 機器學習資料漂移功能可以完全區分兩個資料集。 | 由於採用機器學習技術生成此量級，預計測量的雜訊值為精確百分比。 | 
| 按特徵漂移貢獻 | 目標資料集中每個要素對測量漂移幅度的貢獻。 |  由於協變數偏移，要素的基礎分佈不一定需要更改才能具有相對較高的特徵重要性。 | 

下圖是 Azure 機器學習工作室中的**漂移概覽**結果中看到的圖表示例，該圖由[NOAA 集成曲面資料的](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)回填結果生成。 資料被採樣到`stationName contains 'FLORIDA'`，2019 年 1 月用作基準資料集，所有 2019 年的資料都用作目標。
 
![漂移概述](./media/how-to-monitor-datasets/drift-overview.png)

### <a name="feature-details"></a>功能詳細資訊

"**功能詳細資訊**"部分包含有關所選要素分佈變化的特徵級見解，以及隨著時間的推移進行的其他統計資訊。 

目標資料集也會隨時間而分析。 每個要素的基線分佈之間的統計距離與目標資料集隨時間的分佈進行比較，這在概念上與資料漂移幅度相似，但此統計距離針對單個要素除外。 最小值、最大值和均值也可用。 

在 Azure 機器學習工作室中，如果按一下圖形中的資料點，則顯示的功能的分佈將相應地調整。 預設情況下，它顯示基線資料集的分佈和同一要素的最新運行分佈。 

這些指標也可以通過`get_metrics()``DataDriftDetector`物件上的方法在 Python SDK 中檢索。 

#### <a name="numeric-features"></a>數位特徵 

每個資料集監視器運行中都會分析數位要素。 以下功能在 Azure 機器學習工作室中公開。 顯示分佈的概率密度。

| 計量 | 描述 |  
| ------ | ----------- |  
| 瓦瑟斯坦距離 | 將基線分佈轉換為目標分佈的最小工作量。 |
| 平均值 | 要素的平均值。 |
| 最小值 | 要素的最小值。 |
| 最大值 | 要素的最大值。 |

![功能詳細資訊數位](./media/how-to-monitor-datasets/feature-details.png)

#### <a name="categorical-features"></a>分類功能 

每個資料集監視器運行中都會分析數位要素。 以下功能在 Azure 機器學習工作室中公開。 顯示分佈的長條圖。

| 計量 | 描述 |  
| ------ | ----------- |  
| 歐克利迪亞距離 | 基線和目標分佈之間的幾何距離。 |
| 唯一值 | 要素的唯一值（基數）數。 |


![功能詳細資訊分類](./media/how-to-monitor-datasets/feature-details2.png)

## <a name="metrics-alerts-and-events"></a>指標、警報和事件

指標可以在與機器學習工作區關聯的[Azure 應用程式見解](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)資源中查詢。 它允許訪問應用程式見解的所有功能，包括為自訂警報規則設置操作組和操作組以觸發操作，例如電子郵件/SMS/Push/語音或 Azure 功能。 有關詳細資訊，請參閱完整的應用程式見解文檔。 

要開始，請導航到 Azure 門戶並選擇工作區的 **"概述"** 頁。  關聯的應用程式見解資源位於最右側：

[![Azure 入口網站概觀](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

在左側窗格的"監視"下選擇日誌（分析）：

![應用程式洞察概述](./media/how-to-monitor-datasets/ai-overview.png)

資料集監視器指標存儲為`customMetrics`。 您可以在設置資料集監視器以查看查詢後編寫和執行查詢：

[![日誌分析查詢](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

確定設置警報規則的指標後，創建新的警報規則：

![新增警示規則](./media/how-to-monitor-datasets/alert-rule.png)

您可以使用現有操作組，或創建新操作組來定義滿足設置條件時要執行的操作：

![新操作組](./media/how-to-monitor-datasets/action-group.png)

## <a name="troubleshooting"></a>疑難排解

限制和已知問題：

* 回填作業的時間範圍限制為監視器頻率設置的 31 個間隔。 
* 限制 200 個要素，除非未指定要素清單（使用的所有要素）。
* 計算大小必須足夠大才能處理資料。 
* 確保資料集在給定監視器運行的開始日期和結束日期內具有資料。
* 資料集監視器將僅適用于包含 50 行或更多行的資料集。 

資料集中的列或要素根據下表中的條件分類為分類或數位。 如果該功能不符合這些條件（例如，具有 >100 個唯一值的類型字串列），則該功能將從我們的資料漂移演算法中刪除，但仍被分析。 

| 功能類型 | 資料類型 | 條件 | 限制 | 
| ------------ | --------- | --------- | ----------- |
| 類別 | 字串， 布林， int， 浮動 | 要素中的唯一值數小於 100，小於行數的 5%。 | Null 被視為其自己的類別。 | 
| 數值 | int， 浮動 | 要素中的值是數值資料類型，不符合分類要素的條件。 | 如果>15% 的值為空，則要素將被刪除。 | 

## <a name="next-steps"></a>後續步驟

* 前往[Azure 機器學習工作室](https://ml.azure.com)或[Python 筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb)以設置資料集監視器。
* 瞭解如何在[部署到 Azure 庫伯奈斯服務的模型](how-to-monitor-data-drift.md)上設置資料漂移。
* 使用[事件網格](how-to-use-event-grid.md)設置資料集漂移監視器。 
