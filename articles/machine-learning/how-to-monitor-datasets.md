---
title: 分析和監視資料集上的資料漂移（預覽）
titleSuffix: Azure Machine Learning
description: 建立 Azure Machine Learning 資料集監視器（預覽）、監視資料集中的資料漂移，以及設定警示。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: nibaccam
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/04/2019
ms.openlocfilehash: 15cfa56f718290af3ae5fb87aadab70016cc8594
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84430233"
---
# <a name="detect-data-drift-preview-on-datasets"></a>在資料集上偵測資料漂移（預覽）
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何建立 Azure Machine Learning 資料集監視器（預覽）、監視資料集中的資料漂移和統計變更，以及設定警示。

有了 Azure Machine Learning 資料集監視器，您可以：
* **分析資料中的漂移**，以瞭解它在一段時間內的變化。
* **監視模型資料**，以瞭解定型和服務資料集之間的差異。
* **監視新的資料**，以瞭解任何基準和目標資料集之間的差異。
* **分析資料中的功能**，以追蹤統計屬性隨著時間變更的方式。
* **設定資料漂移的警示**，以解決早期警告的潛在問題。 

計量和深入解析可透過與 Azure Machine Learning 工作區相關聯的[Azure 應用程式 insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)資源來取得。

> [!Important]
> 請注意，在所有版本中都可使用 SDK 的監視資料漂移，而透過 web 上的 studio 監視資料漂移則僅限 Enterprise edition。

## <a name="prerequisites"></a>必要條件

若要建立及使用資料集監視器，您需要：
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。
* [Azure Machine Learning 工作區](how-to-manage-workspace.md)。
* [已安裝適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)，其中包括 azureml 資料集封裝。
* 結構化（表格式）資料，其中包含檔案路徑、檔案名或資料行中所指定的時間戳記。

## <a name="what-is-data-drift"></a>什麼是資料漂移？

在機器學習環境中，資料漂移是模型輸入資料的變更，會導致模型效能降低。 這是一段時間的其中一個主要原因模型精確度下降，因此監視資料漂移有助於偵測模型效能問題。

資料漂移的原因包括： 

- 上游處理常式變更，例如即將更換的感應器，會將測量單位從英寸變更為釐米。 
- 資料品質問題（例如，損壞的感應器）一律會讀取0。
- 資料中的自然漂移，例如平均溫度變化與季節。
- 變更功能之間的關聯性，或 covariate shift。 

有了 Azure Machine Learning 資料集監視器，您可以設定警示，以協助資料集內的資料漂移偵測。 

### <a name="dataset-monitors"></a>資料集監視器 

您可以建立資料集監視，以偵測資料集內的新資料並對其發出警示、分析歷程記錄資料，並在一段時間後分析新的資料。 資料漂移演算法提供資料變更的整體量值，並指出哪些功能會負責進一步調查。 資料集監視器會藉由分析資料集中的新資料來產生一些其他計量 `timeseries` 。 您可以透過[Azure 應用程式 Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)，在監視所產生的所有計量上設定自訂警示。 資料集監視器可用來快速攔截資料問題，並藉由找出可能的原因來縮短偵測問題的時間。  

在概念上，在 Azure Machine Learning 中設定資料集監視器的主要案例有三種。

狀況 | Description
---|---
監視模型的服務資料，使其與模型的定型資料漂移 | 此案例的結果可以解讀為監視模型精確度的 proxy，假設如果服務資料從定型資料偏離，模型的精確度會降低。
監視時間序列資料集與上一個時間週期的漂移。 | 此案例較通用，而且可用來監視模型建立的上游或下游相關的資料集。  目標資料集必須具有時間戳記資料行，而基準資料集可以是具有與目標資料集共通之功能的任何表格式資料集。
對過去的資料執行分析。 | 此案例可用於瞭解歷程記錄資料，並在資料集監視器的設定中通知決策。

## <a name="how-dataset-can-monitor-data"></a>資料集可以如何監視資料

藉由使用 Azure Machine Learning，資料漂移會透過資料集來監視。 若要監視資料漂移，基準資料集-通常會指定模型的訓練資料集。 目標資料集（通常是模型輸入資料）會隨著時間與基準資料集進行比較。 這種比較表示您的目標資料集必須指定 timestamp 資料行。

### <a name="set-the-timeseries-trait-in-the-target-dataset"></a>設定 `timeseries` 目標資料集中的特性

目標資料集必須 `timeseries` 在其上設定特性，方法是從資料中的資料行，或從檔案的路徑模式衍生的虛擬資料行中指定 timestamp 資料行。 這可以透過 Python SDK 或 Azure Machine Learning studio 來完成。 必須指定代表「細微程度」時間戳記的資料行，才能將特性加入 `timeseries` 至 dataset。 如果您的資料分割成具有時間資訊的資料夾結構（例如 ' {yyyy/MM/dd} '），您可以透過路徑模式設定建立虛擬資料行，並將它設定為「粗略細微性」時間戳記，以改善時間序列功能的重要性。 

#### <a name="python-sdk"></a>Python SDK

[`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)類別的 [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) 方法會定義資料集的時間戳記資料行。 

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

如需使用 `timeseries` 資料集特性的完整範例，請參閱[範例筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb)或[資料集 SDK 檔](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)。

#### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

如果您使用 Azure Machine Learning studio 建立資料集，請確定您的資料路徑包含時間戳記資訊、包含所有具有資料的子資料夾，以及設定分割區格式。 

在下列範例中，會採用子資料夾*NoaaIsdFlorida/2019*底下的所有資料，而分割區格式會指定時間戳記的年、月和日。 

[![資料分割格式](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

在 [**架構**設定] 中，從指定資料集的虛擬或實際資料行中，指定 timestamp 資料行：

![時間戳記](./media/how-to-monitor-datasets/timestamp.png)

## <a name="dataset-monitor-settings"></a>資料集監視設定

建立具有指定時間戳記設定的資料集之後，您就可以設定資料集監視器。

各種資料集監視設定分成三個群組：**基本資訊、監視設定**和**回填設定**。

### <a name="basic-info"></a>基本資訊

此資料表包含用於資料集監視器的基本設定。

| 設定 | 說明 | 提示 | 可變動 | 
| ------- | ----------- | ---- | ------- | 
| Name | 資料集監視器的名稱。 | | No |
| 基準資料集 | 表格式資料集，將用來做為一段時間的目標資料集比較基準。 | 基準資料集必須具有與目標資料集共通的功能。 一般來說，基準應該設定為模型的訓練資料集或目標資料集的配量。 | No |
| 目標資料集 | 已指定時間戳記資料行的表格式資料集，將會分析資料漂移。 | 目標資料集必須具有與基準資料集共同的功能，而且應該是將 `timeseries` 新資料附加至其中的資料集。 可以分析目標資料集中的歷程記錄資料，或可以監視新的資料。 | No | 
| 頻率 | 在執行回填時，用來排程管線作業和分析歷程記錄資料的頻率。 選項包括 [每日]、[每週] 或 [每月]。 | 調整這項設定以包含可比較的資料大小給基準。 | No | 
| 特性 | 將分析一段時間內資料漂移的功能清單。 | 設定為模型的輸出功能，以測量概念漂移。 請勿包含在一段時間內自然漂移的功能（月、年、索引等等）。 調整功能清單之後，您可以回填和現有的資料漂移監視器。 | Yes | 
| 計算目標 | Azure Machine Learning 計算目標來執行資料集監視作業。 | | Yes | 

### <a name="monitor-settings"></a>監視設定

這些設定適用于將建立的排程資料集監視管線。 

| 設定 | 說明 | 提示 | 可變動 | 
| ------- | ----------- | ---- | ------- |
| 啟用 | 啟用或停用資料集監視器管線上的排程 | 使用回填設定來停用排程來分析歷程記錄資料。 您可以在建立資料集監視器之後啟用它。 | Yes | 
| Latency | 資料在資料集中抵達所需的時間（以小時為單位）。 例如，如果需要三天的時間，資料才會抵達 SQL DB （dataset 封裝），請將延遲設定為72。 | 建立資料集監視器之後無法變更 | No | 
| 電子郵件地址 | 根據違反資料漂移百分比閾值而發出警示的電子郵件地址。 | 電子郵件是透過 Azure 監視器傳送。 | Yes | 
| 閾值 | 電子郵件警示的資料漂移百分比閾值。 | 進一步的警示和事件可以在工作區相關聯 Application Insights 資源中的許多其他計量上設定。 | Yes | 

### <a name="backfill-settings"></a>回填設定

這些設定是用來針對資料漂移計量的過去資料執行回填。

| 設定 | 說明 | 提示 |
| ------- | ----------- | ---- |
| 開始日期 | 回填工作的開始日期。 | | 
| 結束日期 | 回填作業的結束日期。 | 結束日期不能超過 31 * 開始日期的頻率單位。 在現有的資料集監視器上，可以回填度量來分析歷程記錄資料，或以更新的設定取代計量。 |

## <a name="create-dataset-monitors"></a>建立資料集監視器 

使用 Azure Machine Learning studio 或 Python SDK 來建立資料集監視器，以偵測新資料集的資料漂移併發出警示。 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

若要在資料集監視器上設定警示，包含您想要建立監視之資料集的工作區必須具有 Enterprise edition 功能。 

確認工作區功能之後，流覽至 studio 的首頁，然後選取左側的 [資料集] 索引標籤。 選取 [資料集監視器]。

![監看清單](./media/how-to-monitor-datasets/monitor-list.png)

按一下 [ **+ 建立監視器**] 按鈕，然後按一下 **[下一步**] 繼續流覽嚮導。

![精靈](./media/how-to-monitor-datasets/wizard.png)

產生的資料集監視將會出現在清單中。 選取它以移至該監視的詳細資料頁面。

### <a name="from-python-sdk"></a>從 Python SDK

如需完整的詳細資訊，請參閱有關[資料漂移的 PYTHON SDK 參考檔](/python/api/azureml-datadrift/azureml.datadrift)。 

下列範例說明如何使用 Python SDK 來建立資料集監視

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

如需設定 `timeseries` 資料集和資料漂移偵測器的完整範例，請參閱我們的[範例筆記本](https://aka.ms/datadrift-notebook)。

## <a name="understanding-data-drift-results"></a>瞭解資料漂移結果

資料監視器會產生兩個結果群組：漂移總覽和功能詳細資料。 下列動畫會根據選取的功能和度量，顯示可用的漂移監視器圖表。 

![示範影片](./media/how-to-monitor-datasets/video.gif)

### <a name="drift-overview"></a>漂移總覽

**漂移總覽**一節包含資料漂移程度的最上層深入解析，以及應進一步調查哪些功能。 

| 計量 | 描述 | 提示 | 
| ------ | ----------- | ---- | 
| 資料漂移量 | 指定為一段時間的基準和目標資料集之間的百分比。 範圍從0到100，其中0表示相同的資料集，而100表示 Azure Machine Learning 資料漂移功能可以完全分辨出兩個資料集。 | 因為使用機器學習技術來產生這種程度，所以預期測量的確切百分比中有雜訊。 | 
| 依功能的漂移比重 | 目標資料集內每項功能在測量漂移程度上的比重。 |  由於 covariate 轉移的緣故，功能的基礎分佈不一定需要變更，才會有相當高的功能重要性。 | 

下圖是在 Azure Machine Learning studio 的**漂移總覽**結果中看到的圖表範例，這是由[NOAA 整合式表面資料](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)的回填所產生。 已將資料取樣至，並使用 `stationName contains 'FLORIDA'` 2019 年1月作為基準資料集，並將所有2019資料當做目標使用。
 
![漂移總覽](./media/how-to-monitor-datasets/drift-overview.png)

### <a name="feature-details"></a>功能詳細資料

[**功能詳細資料**] 區段包含一段時間內所選功能的散發和其他統計資料變更的功能層級深入解析。 

目標資料集也會在一段時間內進行分析。 每項功能的基準分佈之間的統計距離會與目標資料集的一段時間進行比較，這在概念上類似于資料漂移的範圍，但此統計距離是針對個別功能所產生的例外狀況。 最小值、最大值和平均值也可供使用。 

在 Azure Machine Learning studio 中，如果您按一下圖形中的資料點，則會據以調整所顯示的功能分佈。 根據預設，它會顯示基準資料集的散發，以及相同功能的最新執行散發。 

您也可以透過物件上的方法，在 Python SDK 中抓取這些計量 `get_metrics()` `DataDriftDetector` 。 

#### <a name="numeric-features"></a>數值特徵 

數值功能會在每個資料集監視執行中進行分析。 下列會在 Azure Machine Learning studio 中公開。 機率密度會針對分佈顯示。

| 計量 | 描述 |  
| ------ | ----------- |  
| Wasserstein 距離 | 將基準散發轉換成目標分佈的最低工作量。 |
| 平均值 | 功能的平均值。 |
| 最小值 | 功能的最小值。 |
| 最大值 | 功能的最大值。 |

![功能詳細資料數值](./media/how-to-monitor-datasets/feature-details.png)

#### <a name="categorical-features"></a>類別功能 

數值功能會在每個資料集監視執行中進行分析。 下列會在 Azure Machine Learning studio 中公開。 隨即顯示分佈的長條圖。

| 計量 | 描述 |  
| ------ | ----------- |  
| Euclidian 距離 | 基準與目標分佈之間的幾何距離。 |
| 唯一值 | 功能的唯一值（基數）數目。 |


![功能詳細資料類別](./media/how-to-monitor-datasets/feature-details2.png)

## <a name="metrics-alerts-and-events"></a>計量、警示和事件

您可以在與您的機器學習服務工作區相關聯的[Azure 應用程式 Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)資源中查詢計量。 這可讓您存取 Application Insights 的所有功能，包括設定自訂警示規則和動作群組來觸發動作，例如電子郵件/SMS/推播/語音或 Azure 功能。 如需詳細資訊，請參閱完整的 Application Insights 檔。 

若要開始使用，請流覽至 Azure 入口網站，然後選取工作區的 **[總覽**] 頁面。  相關聯的 Application Insights 資源位於最右邊：

[![Azure 入口網站概觀](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

在左窗格中，選取 [監視] 下的 [記錄（分析）]：

![Application insights 總覽](./media/how-to-monitor-datasets/ai-overview.png)

資料集監視器計量會儲存為 `customMetrics` 。 您可以在設定資料集監視器之後撰寫並執行查詢來加以查看：

[![Log analytics 查詢](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

在識別計量以設定警示規則之後，請建立新的警示規則：

![新增警示規則](./media/how-to-monitor-datasets/alert-rule.png)

您可以使用現有的動作群組，或建立一個新的，以定義符合設定條件時所要採取的動作：

![新增動作群組](./media/how-to-monitor-datasets/action-group.png)

## <a name="troubleshooting"></a>疑難排解

限制與已知問題：

* 回填作業的時間範圍限制為監視頻率設定的31個間隔。 
* 200功能的限制，除非未指定功能清單（所有使用的功能）。
* 計算大小必須夠大，才能處理資料。 
* 請確定您的資料集在指定的監視執行的開始和結束日期內有資料。
* 資料集監視器僅適用于包含50個或更多資料列的資料集。 

資料集中的資料行或功能會根據下表中的條件分類為類別或數值。 如果功能不符合這些條件（例如，具有 >100 唯一值之 string 類型的資料行），則會從我們的資料漂移演算法中卸載此功能，但仍會進行分析。 

| 功能類型 | 資料類型 | 條件 | 限制 | 
| ------------ | --------- | --------- | ----------- |
| 類別 | string、bool、int、float | 此功能中的唯一值數目小於100，且小於5% 的資料列數目。 | Null 會被視為它自己的類別。 | 
| 數值 | int、float | 功能中的值是數值資料類型，而且不符合類別功能的條件。 | 如果 >的15% 值為 null，則會卸載功能。 | 

## <a name="next-steps"></a>後續步驟

* 前往[Azure Machine Learning studio](https://ml.azure.com)或[Python 筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb)來設定資料集監視。
* 瞭解如何在[部署至 Azure Kubernetes Service 的模型](how-to-monitor-data-drift.md)上設定資料漂移。
* 使用[事件方格](how-to-use-event-grid.md)來設定資料集漂移監視器。 
