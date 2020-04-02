---
title: 分析並監控資料集上的資料漂移(預覽)
titleSuffix: Azure Machine Learning
description: 創建 Azure 機器學習資料集監視器(預覽)、監視數據集中的數據漂移以及設置警報。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/04/2019
ms.openlocfilehash: 0a7a89b4ff1f6deb94c545e64b4584d7959d573a
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546386"
---
# <a name="detect-data-drift-preview-on-datasets"></a>偵測資料集上的資料漂移(預覽)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中,您將瞭解如何創建 Azure 機器學習數據集監視器(預覽)、監視數據漂移和數據集中的統計更改以及設置警報。

使用 Azure 機器學習資料集監視器,您可以:
* **分析數據中的漂移**,瞭解數據隨時間的變化。
* **監視模型數據**,以發現定型數據集和服務數據集之間的差異。
* **監視新資料**,以發現任何基線數據集和目標數據集之間的差異。
* **數據中的設定檔要素**,用於追蹤統計屬性隨時間的變化。
* **設定資料漂移警報,** 以便對潛在問題發出早期警告。 

指標和見解可通過與 Azure 機器學習工作區關聯的[Azure 應用程式見解](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)資源獲得。

> [!Important]
> 請注意,使用 SDK 監視數據漂移在所有版本中都可用,而通過網路工作室監視數據漂移僅是企業版。

## <a name="prerequisites"></a>Prerequisites

要建立與使用資料集監視器,您需要:
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。
* [Azure 機器學習工作區](how-to-manage-workspace.md)。
* [安裝的 Python 的 Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py),其中包括 azurem 資料集包。
* 具有在檔案路徑、檔名或數據列中指定的時間戳的結構化(表格)數據。

## <a name="what-is-data-drift"></a>什麼是數據漂移?

在機器學習上下文中,數據漂移是模型輸入數據的變化,導致模型性能下降。 它是模型精度隨時間而降低的主要原因之一,因此監視數據漂移有助於檢測模型性能問題。

資料漂移的原因包括: 

- 上游過程變化,例如更換感測器,將測量單位從英寸更改為釐米。 
- 數據品質問題,例如感測器損壞時始終讀取 0。
- 數據中的自然漂移,如平均氣溫隨季節變化而變化。
- 特徵或協變數偏移之間的關係變化。 

借助 Azure 機器學習資料集監視器,您可以設置警報,以便隨著時間的推移在數據集中幫助進行資料漂移檢測。 

### <a name="dataset-monitors"></a>資料集監視器 

您可以創建資料集監視器來檢測和警報資料在資料集中的新資料漂移,分析歷史資料以進行漂移,並隨著時間的推移分析新資料。 數據漂移演演演算法提供了數據變化的總體度量,並指示哪些功能負責進一步調查。 數據集監視器通過分析`timeseries`數據集中的新數據生成許多其他指標。 可以通過[Azure 應用程式見解](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)對監視器生成的所有指標設置自定義警報。 數據集監視器可用於快速捕獲數據問題,並通過識別可能的原因來縮短調試問題的時間。  

從概念上講,在 Azure 機器學習中設置數據集監視器有三種主要方案。

狀況 | 描述
---|---
監視模型的服務資料,以便從模型的訓練資料中漂移 | 此方案的結果可以解釋為監視模型準確性的代理,因為如果服務數據從訓練數據漂移,則模型精度會降低。
監視時間序列資料集,以尋找與上一時間段的漂移。 | 此方案更一般,可用於監視模型構建上游或下游的數據集。  目標資料集必須具有時間戳列,而基線數據集可以是具有與目標數據集共有要素的任何表格數據集。
對過去的數據進行分析。 | 此方案可用於瞭解歷史數據,並在數據集監視器的設置中為決策提供資訊。

## <a name="how-dataset-can-monitor-data"></a>資料組如何監控資料

使用 Azure 機器學習,數據漂移通過數據集進行監視。 為了監視數據漂移,指定了基線數據集(通常是模型的訓練數據集)。 目標數據集(通常是模型輸入數據)會隨著時間的推移與基線數據集進行比較。 此比較意味著目標數據集必須指定時間戳列。

### <a name="set-the-timeseries-trait-in-the-target-dataset"></a>在`timeseries`目標資料集中設定特徵

目標資料集需要通過指定數據中的`timeseries`列或從檔的路徑模式派生的虛擬列來設置其特徵。 這可以通過 Python SDK 或 Azure 機器學習工作室完成。 必須指定表示「細粒度」時間戳的列才能向數據集`timeseries`添加特徵。 如果數據被劃分為包含時間資訊(如"_yyy/MM/dd}")的資料夾結構,則可以通過路徑模式設置創建虛擬列,並將其設置為"粗粒"時間戳,以提高時間序列功能的重要性。 

#### <a name="python-sdk"></a>Python SDK

類[`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)的方法[`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)定義數據集的時間戳列。 

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

有關使用資料集`timeseries`特徵的完整範例,請參考[範例的功能](https://aka.ms/azureml-tsd-notebook)或[資料集 SDK 文件](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)。

#### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

如果使用 Azure 機器學習工作室創建數據集,請確保數據的路徑包含時間戳資訊,包含包含數據的所有子資料夾,並設置分區格式。 

在下面的示例中,獲取子資料夾*NoaaIsdFlorida/2019*下的所有數據,分區格式指定時間戳的年份、月份和日。 

[![區塊格式](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

在 **'架構'** 設定中,指定指定資料集中的虛擬列或實列的時間戳列:

![時間戳記](./media/how-to-monitor-datasets/timestamp.png)

## <a name="dataset-monitor-settings"></a>資料集監視器設定

使用指定的時間戳設置創建資料集後,即可配置數據集監視器。

各種資料集監視器設定分為三組:**基本資訊、監視器設定**與**回填設定**。

### <a name="basic-info"></a>基本資訊

此表包含用於數據集監視器的基本設置。

| 設定 | 描述 | 提示 | 可變動 | 
| ------- | ----------- | ---- | ------- | 
| 名稱 | 數據集監視器的名稱。 | | 否 |
| 基線資料集 | 用作隨時間比較目標數據集的基線的表格數據集。 | 基線數據集必須具有與目標數據集共有的功能。 通常,基線應設置為模型的訓練數據集或目標數據集的切片。 | 否 |
| 目標資料集 | 指定時間戳列的表格資料集,用於分析數據漂移。 | 目標數據集必須具有與基線數據集共有的功能,並且應該是一個`timeseries`數據集,新數據將追加到該數據集中。 可以分析目標數據集中的歷史數據,也可以監視新數據。 | 否 | 
| 頻率 | 用於安排管道作業並分析運行回填時的歷史數據的頻率。 選項包括每日、每周或每月。 | 調整此設置以包括與基線相當的數據大小。 | 否 | 
| 特性 | 將分析隨時間漂移的數據的要素清單。 | 設置為模型的輸出特徵,以測量概念漂移。 不要包含隨時間自然漂移的要素(月、年、指數等)。 調整要素清單后,可以回填和現有數據漂移監視器。 | 是 | 
| 計算目標 | Azure 機器學習計算目標以運行數據集監視器作業。 | | 是 | 

### <a name="monitor-settings"></a>監視器設定

這些設置用於將創建的計畫數據集監視器管道。 

| 設定 | 描述 | 提示 | 可變動 | 
| ------- | ----------- | ---- | ------- |
| 啟用 | 在資料集監視器導管上啟用或關閉計畫 | 禁用計劃以使用回填設置分析歷史數據。 創建數據集監視器後,可以啟用它。 | 是 | 
| Latency | 資料到達資料集所需的時間(以小時表示) 例如,如果數據到達數據集封裝的 SQL DB 需要三天時間,則將延遲設置為 72。 | 建立資料集監視器後無法變更 | 否 | 
| 電子郵件地址 | 基於違反數據漂移百分比閾值的警報的電子郵寄地址。 | 電子郵件通過 Azure 監視器發送。 | 是 | 
| 閾值 | 用於電子郵件警報的數據漂移百分比閾值。 | 可以在工作區關聯的應用程式見解資源中的許多其他指標上設置其他警報和事件。 | 是 | 

### <a name="backfill-settings"></a>回填設定

這些設置用於對數據漂移指標的過去數據運行回填。

| 設定 | 描述 | 提示 |
| ------- | ----------- | ---- |
| 開始日期 | 回填作業的開始日期。 | | 
| 結束日期 | 回填作業的結束日期。 | 結束日期不能超過從開始日期開始的 31+頻率時間單位。 在現有數據集監視器上,可以回填指標以分析歷史數據或使用更新的設置替換指標。 |

## <a name="create-dataset-monitors"></a>建立資料集監視器 

使用 Azure 機器學習工作室或 Python SDK 創建數據集監視器以檢測和警報新數據集上的數據漂移。 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

要在數據集監視器上設置警報,包含要為其創建監視器的數據集的工作區必須具有企業版功能。 

確認工作區功能后,導航到工作室的主頁,然後選擇左側的數據集選項卡。 選擇數據集監視器。

![監視器清單](./media/how-to-monitor-datasets/monitor-list.png)

按下 **「建立監視器」** 按鈕,然後按**下「 下一步**」 繼續精靈。

![精靈](./media/how-to-monitor-datasets/wizard.png)

生成的數據集監視器將顯示在清單中。 選擇它以轉到該監視器的詳細信息頁面。

### <a name="from-python-sdk"></a>從 Python SDK

有關詳細資訊,請參閱[有關資料漂移的 Python SDK 參考文件](/python/api/azureml-datadrift/azureml.datadrift)。 

下面的範例展示如何使用 Python SDK 建立資料集監視器

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

有關設定`timeseries`資料集和資料漂移偵測器的完整範例,請參閱我們的[範例筆記本](https://aka.ms/datadrift-notebook)。

## <a name="understanding-data-drift-results"></a>瞭解資料漂移結果

數據監視器生成兩組結果:漂移概覽和功能詳細資訊。 以下動畫根據所選要素和指標顯示可用的漂移監視器圖表。 

![簡報](./media/how-to-monitor-datasets/video.gif)

### <a name="drift-overview"></a>漂移概述

**漂移概述**部分包含有關數據漂移程度以及應進一步調查哪些要素的頂級見解。 

| 計量 | 描述 | 提示 | 
| ------ | ----------- | ---- | 
| 資料漂移幅度 | 給定為基線和目標數據集之間的百分比。 從 0 到 100,其中 0 表示相同的數據集,100 表示 Azure 機器學習數據漂移功能可以完全區分兩個數據集。 | 由於採用機器學習技術生成此量級,預計測量的雜訊值為精確百分比。 | 
| 依特徵漂移貢獻 | 目標數據集中每個要素對測量漂移幅度的貢獻。 |  由於協變數偏移,要素的基礎分佈不一定需要更改才能具有相對較高的特徵重要性。 | 

下圖是 Azure 機器學習工作室中的**漂移概覽**結果中看到的圖表示例,該圖由[NOAA 集成曲面數據的](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)回填結果生成。 數據被採樣到`stationName contains 'FLORIDA'`,2019 年 1 月用作基準數據集,所有 2019 年的數據都用作目標。
 
![漂移概述](./media/how-to-monitor-datasets/drift-overview.png)

### <a name="feature-details"></a>功能詳細資訊

功能**詳細資訊**「部分包含有關所選要素分佈變化的特徵級見解,以及隨著時間的推移進行的其他統計資訊。 

目標數據集也會隨時間而分析。 每個要素的基線分佈之間的統計距離與目標數據集隨時間的分佈進行比較,這在概念上與數據漂移幅度相似,但此統計距離針對單個要素除外。 最小值、最大值和均值也可用。 

在 Azure 機器學習工作室中,如果單擊圖形中的數據點,則顯示的功能的分佈將相應地調整。 默認情況下,它顯示基線數據集的分佈和同一要素的最新運行分佈。 

這些指標也可以通過`get_metrics()``DataDriftDetector`物件上的方法在 Python SDK 中檢索。 

#### <a name="numeric-features"></a>數位特徵 

每個數據集監視器運行中都會分析數位要素。 以下功能在 Azure 機器學習工作室中公開。 顯示分佈的概率密度。

| 計量 | 描述 |  
| ------ | ----------- |  
| 瓦瑟斯坦距離 | 將基線分佈轉換為目標分佈的最小工作量。 |
| 平均值 | 要素的平均值。 |
| 最小值 | 要素的最小值。 |
| 最大值 | 要素的最大值。 |

![功能詳細資訊數字](./media/how-to-monitor-datasets/feature-details.png)

#### <a name="categorical-features"></a>類別功能 

每個數據集監視器運行中都會分析數位要素。 以下功能在 Azure 機器學習工作室中公開。 顯示分佈的直方圖。

| 計量 | 描述 |  
| ------ | ----------- |  
| 歐克利迪亞距離 | 基線和目標分佈之間的幾何距離。 |
| 唯一值 | 要素的唯一值(基數)數。 |


![功能詳細資訊類別](./media/how-to-monitor-datasets/feature-details2.png)

## <a name="metrics-alerts-and-events"></a>指標、警示和事件

指標可以在與機器學習工作區關聯的[Azure 應用程式見解](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)資源中查詢。 它允許造訪應用程式見解的所有功能,包括為自訂警報規則設置操作組和操作組以觸發操作,例如電子郵件/SMS/Push/語音或 Azure 功能。 有關詳細資訊,請參閱完整的應用程式見解文檔。 

要開始,請導航到 Azure 門戶並選擇工作區的 **「概述」** 頁。  關聯的應用程式見解資源位於最右側:

[![Azure 入口網站概觀](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

在左邊窗格的「監視」下選擇日誌(分析):

![應用程式洞察概述](./media/how-to-monitor-datasets/ai-overview.png)

資料集監控器指標儲存為`customMetrics`。 您可以在設定資料集監視器以檢視查詢後編寫與執行查詢:

[![紀錄分析查詢](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

確定設定警示規則的指標後,建立新的警報規則:

![新增警示規則](./media/how-to-monitor-datasets/alert-rule.png)

您可以使用現有操作群組,或建立新操作群組來定義滿足設定條件時要執行的操作:

![新操作群組](./media/how-to-monitor-datasets/action-group.png)

## <a name="troubleshooting"></a>疑難排解

限制並已知問題:

* 回填作業的時間範圍限制為監視器頻率設置的 31 個間隔。 
* 限制 200 個要素,除非未指定要素清單(使用的所有要素)。
* 計算大小必須足夠大才能處理數據。 
* 確保數據集在給定監視器運行的開始日期和結束日期內具有數據。
* 數據集監視器將僅適用於包含 50 行或更多行的數據集。 

數據集中的列或要素根據下表中的條件分類為分類或數位。 如果該功能不符合這些條件(例如,具有 >100 個唯一值的類型字串列),則該功能將從我們的資料漂移演演演算法中刪除,但仍被分析。 

| 功能類型 | 資料類型 | 條件 | 限制 | 
| ------------ | --------- | --------- | ----------- |
| 類別 | 字串, 布林, 布林, int, 浮動 | 要素中的唯一值數小於 100,小於行數的 5%。 | Null 被視為其自己的類別。 | 
| 數值 | int, 浮動 | 要素中的值是數值數據類型,不符合分類要素的條件。 | 如果>15% 的值為空,則要素將被刪除。 | 

## <a name="next-steps"></a>後續步驟

* 前往[Azure 機器學習工作室](https://ml.azure.com)或[Python 筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb)以設置數據集監視器。
* 瞭解如何在[部署到 Azure 庫伯奈斯服務的模型](how-to-monitor-data-drift.md)上設置數據漂移。
* 使用[事件網格](how-to-use-event-grid.md)設置數據集漂移監視器。 
