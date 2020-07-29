---
title: 分析和監視資料集上的資料漂移（預覽）
titleSuffix: Azure Machine Learning
description: 建立 Azure Machine Learning 資料集監視器（預覽）、監視資料集中的資料漂移，以及設定警示。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 06/25/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 270e93302a90c458ccbdfdc4d2ced8f0d3c263af
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319672"
---
# <a name="detect-data-drift-preview-on-datasets"></a>在資料集上偵測資料漂移（預覽）
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

> [!IMPORTANT]
> 在資料集上偵測資料漂移目前為公開預覽狀態。
> 預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

瞭解如何監視資料漂移，並在漂移偏高時設定警示。  

使用 Azure Machine Learning 資料集監視器（預覽），您可以：
* **分析資料中的漂移**，以瞭解它在一段時間內的變化。
* **監視模型資料**，以瞭解定型和服務資料集之間的差異。  首先[從已部署的模型收集模型資料](how-to-enable-data-collection.md)。
* **監視新的資料**，以瞭解任何基準和目標資料集之間的差異。
* **分析資料中的功能**，以追蹤統計屬性隨著時間變更的方式。
* **設定資料漂移的警示**，以解決早期警告的潛在問題。 

[Azure Machine learning 資料集會](how-to-create-register-datasets.md)用來建立監視器。 資料集必須包含時間戳記資料行。

您可以使用 Python SDK 或在 Azure Machine Learning studio 中，來觀看資料漂移計量。  您可以透過與 Azure Machine Learning 工作區相關聯的[Azure 應用程式 insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)資源來取得其他計量和見解。

> [!Important]
> 所有版本都提供與 SDK 的監視資料漂移。 不過，透過 web 上的 studio 監視資料漂移僅限 Enterprise edition。

## <a name="prerequisites"></a>先決條件

若要建立及使用資料集監視器，您需要：
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。
* [Azure Machine Learning 工作區](how-to-manage-workspace.md)。
* [已安裝適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)，其中包括 azureml 資料集封裝。
* 結構化（表格式）資料，其中包含檔案路徑、檔案名或資料行中所指定的時間戳記。

## <a name="what-is-data-drift"></a>什麼是資料漂移？

資料漂移是一段時間的其中一個主要原因模型的精確度下降。  針對機器學習模型，資料漂移是模型輸入資料的變更，會導致模型效能降低。  監視資料漂移有助於偵測到這些模型的效能問題。

資料漂移的原因包括：

- 上游處理常式變更，例如即將更換的感應器，會將測量單位從英寸變更為釐米。 
- 資料品質問題（例如，損壞的感應器）一律會讀取0。
- 資料中的自然漂移，例如平均溫度變化與季節。
- 變更功能之間的關聯性，或 covariate shift。 

Azure Machine Learning 藉由計算單一計量來抽象化比較資料集的複雜性，藉此簡化漂移偵測。  這些資料集可能會有數百個功能和數萬個數據列。 一旦偵測到漂移，您就可以向下切入哪些功能導致漂移。  接著，您會檢查功能層級計量以進行偵測，並找出漂移的根本原因。

這個由上而下的方法可讓您輕鬆地監視資料，而不是傳統的規則式技術。 以規則為基礎的技術（例如允許的資料範圍或允許的唯一值）可能相當耗時且容易出錯。

在 Azure Machine Learning 中，您可以使用資料集監視器來偵測和警示資料漂移。
  
### <a name="dataset-monitors"></a>資料集監視器 

使用資料集監視，您可以：

* 針對資料集內的新資料，偵測並警示資料漂移。
* 分析歷程記錄資料的漂移。
* 分析一段時間的新資料。

資料漂移演算法提供資料變更的整體量值，並指出哪些功能會負責進一步調查。 資料集監視器會藉由分析資料集中的新資料來產生一些其他計量 `timeseries` 。 

您可以透過[Azure 應用程式 Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)，在監視所產生的所有計量上設定自訂警示。 資料集監視器可用來快速攔截資料問題，並藉由找出可能的原因來縮短偵測問題的時間。  

在概念上，在 Azure Machine Learning 中設定資料集監視器的主要案例有三種。

狀況 | 描述
---|---
監視模型的服務資料，使其偏離定型資料 | 此案例的結果可以針對模型的精確度，解讀為監視 proxy，因為從定型資料偏離服務資料時，模型精確度會降低。
監視時間序列資料集與上一個時間週期的漂移。 | 此案例較通用，而且可用來監視模型建立的上游或下游相關的資料集。  目標資料集必須有 timestamp 資料行。 基準資料集可以是具有與目標資料集共通之功能的任何表格式資料集。
對過去的資料執行分析。 | 此案例可用於瞭解歷程記錄資料，並在資料集監視器的設定中通知決策。

資料集監視相依于下列 Azure 服務。

|Azure 服務  |說明  |
|---------|---------|
| *資料集* | 漂移會使用 Machine Learning 資料集來捕獲定型資料，並比較模型定型的資料。  產生資料的設定檔會用來產生一些回報的計量，例如 min、max、distinct values、distinct values count。 |
| *Azureml 管線和計算* | 漂移計算作業裝載于 azureml 管線中。  作業會視需要觸發，或依排程在漂移監視器建立時間所設定的計算上執行。
| *Application insights*| 漂移會發出計量給屬於機器學習服務工作區 Application Insights。
| *Azure Blob 儲存體*| 漂移會以 json 格式將計量發出至 Azure blob 儲存體。

## <a name="how-dataset-monitors-data"></a>資料集如何監視資料

使用 Machine Learning 資料集來監視資料漂移。 指定基準資料集-通常是模型的訓練資料集。 目標資料集（通常是模型輸入資料）會隨著時間與基準資料集進行比較。 這種比較表示您的目標資料集必須指定 timestamp 資料行。

## <a name="create-target-dataset"></a>建立目標資料集

目標資料集需要 `timeseries` 在其上設定的特性，方法是從資料中的資料行或從檔案的路徑模式衍生的虛擬資料行中指定 timestamp 資料行。 透過[PYTHON SDK](#sdk-dataset)或[Azure Machine Learning studio](#studio-dataset)建立具有時間戳記的資料集。 必須指定代表「時間戳記」的資料行，才能將特性加入 `timeseries` 至 dataset。 如果您的資料已分割成具有時間資訊的資料夾結構，例如 ' {yyyy/MM/dd} '，請透過路徑模式設定建立虛擬資料行，並將它設定為「分割時間戳記」，以改善時間序列功能的重要性。

### <a name="python-sdk"></a><a name="sdk-dataset"></a>Python SDK

[`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)Class [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) 方法會定義資料集的時間戳記資料行。

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

### <a name="azure-machine-learning-studio"></a><a name="studio-dataset"></a>Azure Machine Learning studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

如果您使用 Azure Machine Learning studio 建立資料集，請確定您的資料路徑包含時間戳記資訊、包含所有具有資料的子資料夾，以及設定分割區格式。

在下列範例中，會採用子資料夾*NoaaIsdFlorida/2019*底下的所有資料，而分割區格式會指定時間戳記的年、月和日。

[![資料分割格式](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

在 [**架構**設定] 中，從指定資料集的虛擬或實際資料行中，指定 timestamp 資料行：

:::image type="content" source="media/how-to-monitor-datasets/timestamp.png" alt-text="設定時間戳記":::

如果您的資料是依日期進行分割，則在此情況下，您也可以指定 partition_timestamp。  這可讓您更有效率地處理日期。

:::image type="content" source="media/how-to-monitor-datasets/timeseries-partitiontimestamp.png" alt-text="分割區時間戳記":::


## <a name="create-dataset-monitors"></a>建立資料集監視器

建立資料集監視器，以偵測新資料集上的資料漂移併發出警示。  請使用[PYTHON SDK](#sdk-monitor)或[Azure Machine Learning studio](#studio-monitor)。

### <a name="python-sdk"></a><a name="sdk-monitor"></a>Python SDK

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

### <a name="azure-machine-learning-studio"></a><a name="studio-monitor"></a>Azure Machine Learning studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

若要在資料集監視器上設定警示，包含您想要建立監視之資料集的工作區必須具有 Enterprise edition 功能。

確認工作區功能之後，流覽至[studio 的首頁](https://ml.azure.com)，然後選取左側的 [**資料集**] 索引標籤。 選取 [**資料集監視器**]。

![監看清單](./media/how-to-monitor-datasets/monitor-list.png)

按一下 [ **+ 建立監視器**] 按鈕，然後按一下 **[下一步**] 繼續流覽嚮導。  

:::image type="content" source="media/how-to-monitor-datasets/wizard.png" alt-text="建立監視嚮導":::

* **選取 [目標資料集**]。  目標資料集是具有指定時間戳記資料行的表格式資料集，將會分析資料漂移。 目標資料集必須具有與基準資料集共同的功能，而且應該是將 `timeseries` 新資料附加至其中的資料集。 可以分析目標資料集中的歷程記錄資料，或可以監視新的資料。

* **選取 [基準資料集]。**  選取表格式資料集，做為一段時間內目標資料集的比較基準。  基準資料集必須具有與目標資料集共通的功能。  選取 [時間範圍] 以使用目標資料集的配量，或指定要當做基準使用的個別資料集。

* **監視設定**。  這些設定適用于將建立的排程資料集監視管線。 

    | 設定 | 說明 | 提示 | 可變動 | 
    | ------- | ----------- | ---- | ------- |
    | Name | 資料集監視器的名稱。 | | 否 |
    | 特性 | 將分析一段時間內資料漂移的功能清單。 | 設定為模型的輸出功能，以測量概念漂移。 請勿包含在一段時間內自然漂移的功能（月、年、索引等等）。 調整功能清單之後，您可以回填和現有的資料漂移監視器。 | 是 | 
    | 計算目標 | Azure Machine Learning 計算目標來執行資料集監視作業。 | | 是 | 
    | 啟用 | 啟用或停用資料集監視器管線上的排程 | 使用回填設定來停用排程來分析歷程記錄資料。 您可以在建立資料集監視器之後啟用它。 | 是 | 
    | 頻率 | 在執行回填時，用來排程管線作業和分析歷程記錄資料的頻率。 選項包括 [每日]、[每週] 或 [每月]。 | 每次執行都會根據頻率來比較目標資料集內的資料： <li>每日：比較目標資料集的最新完整日與基準 <li>每週：比較目標資料集的最新完整周（星期一-星期日）與基準 <li>每月：比較目標資料集的最新完整月份與基準 | 否 | 
    | Latency | 資料在資料集中抵達所需的時間（以小時為單位）。 例如，如果需要三天的時間，資料才會抵達 SQL DB （dataset 封裝），請將延遲設定為72。 | 建立資料集監視器之後無法變更 | 否 | 
    | 電子郵件地址 | 根據違反資料漂移百分比閾值而發出警示的電子郵件地址。 | 電子郵件是透過 Azure 監視器傳送。 | 是 | 
    | 閾值 | 電子郵件警示的資料漂移百分比閾值。 | 進一步的警示和事件可以在工作區相關聯 Application Insights 資源中的許多其他計量上設定。 | 是 |

完成 wizard 之後，所產生的資料集監視將會出現在清單中。 選取它以移至該監視的詳細資料頁面。

## <a name="understand-data-drift-results"></a>瞭解資料漂移結果

本節說明監視資料集的結果，您可以在 Azure studio 的 **[資料**  /  **集監視器**] 頁面中找到。  您可以更新設定，以及在此頁面上分析現有的資料一段特定的時間。  

從最高層級的深入解析開始，進入資料漂移的範圍，以及要進一步調查的功能醒目提示。

:::image type="content" source="media/how-to-monitor-datasets/drift-overview.png" alt-text="漂移總覽":::


| 計量 | 描述 | 
| ------ | ----------- | 
| 資料漂移量 | 一段時間內基準與目標資料集之間的漂移百分比。 範圍從0到100，0表示相同的資料集，100表示 Azure Machine Learning 的資料漂移模型可以完全分辨出兩個資料集。 因為使用機器學習技術來產生這種程度，所以預期測量的確切百分比中有雜訊。 | 
| 熱門離開功能 | 顯示資料集內已漂移最多的功能，因此會對漂移程度計量做出最大的影響。 由於 covariate 轉移的緣故，功能的基礎分佈不一定需要變更，才會有相當高的功能重要性。 |
| 閾值 | 超出設定閾值的資料漂移大小將會觸發警示。 這可以在 [監視設定] 中設定。 | 

### <a name="drift-magnitude-trend"></a>漂移程度趨勢

查看資料集在指定的時間週期中，與目標資料集的差異。  接近100%，這兩個資料集的差異愈多。

:::image type="content" source="media/how-to-monitor-datasets/drift-magnitude.png" alt-text="漂移程度趨勢":::

### <a name="drift-magnitude-by-features"></a>依特徵的漂移大小

本節包含所選功能的散發變更的功能層級深入解析，以及隨時間變化的其他統計資料。

目標資料集也會在一段時間內進行分析。 每項功能的基準分佈之間的統計距離，會與目標資料集的一段時間進行比較。  就概念而言，這類似于資料漂移的程度。  不過，此統計距離是針對個別功能而非所有功能。 最小值、最大值和平均值也可供使用。

在 Azure Machine Learning studio 中，按一下圖表中的橫條，以查看該日期的功能等級詳細資料。 根據預設，您會看到基準資料集的散發和最近一次執行相同功能的散發。

:::image type="content" source="media/how-to-monitor-datasets/drift-by-feature.gif" alt-text="依特徵的漂移大小":::

您也可以透過物件上的方法，在 Python SDK 中抓取這些計量 `get_metrics()` `DataDriftDetector` 。

### <a name="feature-details"></a>功能詳細資料

最後，向下滾動以查看每個個別功能的詳細資料。  使用圖表上方的下拉式清單來選取功能，並另外選取您要查看的度量。

:::image type="content" source="media/how-to-monitor-datasets/numeric-feature.gif" alt-text="數值特徵圖表和比較":::

圖表中的度量取決於功能的類型。

* 數值特徵

    | 計量 | 描述 |  
    | ------ | ----------- |  
    | Wasserstein 距離 | 將基準散發轉換成目標分佈的最低工作量。 |
    | 平均值 | 功能的平均值。 |
    | 最小值 | 功能的最小值。 |
    | 最大值 | 功能的最大值。 |

* 類別功能
    
    | 計量 | 描述 |  
    | ------ | ----------- |  
    | Euclidian 距離     |  針對分類資料行計算。Euclidean 距離是以兩個向量計算而成，從兩個資料集的相同類別的經驗分佈產生。0表示經驗分佈中沒有任何差異。它與0的偏差愈多，此資料行的漂移越多。您可以從這個計量的時間序列圖觀察趨勢，並在發現離開功能時很有説明。  |
    | 唯一值 | 功能的唯一值（基數）數目。 |

在此圖表上，選取單一日期來比較目標和此日期之間的功能分佈，以瞭解所顯示的功能。 對於數值特徵，這會顯示兩個機率分佈。  如果此功能為數值，則會顯示橫條圖。

:::image type="content" source="media/how-to-monitor-datasets/select-date-to-compare.gif" alt-text="選取要與目標比較的日期":::

## <a name="metrics-alerts-and-events"></a>計量、警示和事件

您可以在與您的機器學習服務工作區相關聯的[Azure 應用程式 Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)資源中查詢計量。 您可以存取 Application Insights 的所有功能，包括設定自訂警示規則和動作群組來觸發動作，例如電子郵件/SMS/推播/語音或 Azure 功能。 如需詳細資訊，請參閱完整的 Application Insights 檔。 

若要開始使用，請流覽至[Azure 入口網站](https://portal.azure.com)，然後選取工作區的 **[總覽**] 頁面。  相關聯的 Application Insights 資源位於最右邊：

[![Azure 入口網站概觀](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

在左窗格中，選取 [監視] 下的 [記錄（分析）]：

![Application insights 總覽](./media/how-to-monitor-datasets/ai-overview.png)

資料集監視器計量會儲存為 `customMetrics` 。 您可以在設定資料集監視器之後撰寫並執行查詢來加以查看：

[![Log analytics 查詢](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

在識別計量以設定警示規則之後，請建立新的警示規則：

![新增警示規則](./media/how-to-monitor-datasets/alert-rule.png)

您可以使用現有的動作群組，或建立一個新的，以定義符合設定條件時所要採取的動作：

![新增動作群組](./media/how-to-monitor-datasets/action-group.png)

## <a name="next-steps"></a>後續步驟

* 前往[Azure Machine Learning studio](https://ml.azure.com)或[Python 筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb)來設定資料集監視。
* 瞭解如何在[部署至 Azure Kubernetes Service 的模型](how-to-monitor-data-drift.md)上設定資料漂移。
* 使用[事件方格](how-to-use-event-grid.md)來設定資料集漂移監視器。 
* 如果您遇到問題，請參閱這些常見的[疑難排解秘訣](resource-known-issues.md#data-drift)。
