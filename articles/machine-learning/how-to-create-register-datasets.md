---
title: 建立 Azure 機器學習資料集以存取資料
titleSuffix: Azure Machine Learning
description: 瞭解如何創建 Azure 機器學習數據集以造訪用於機器學習實驗運行的數據。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 02/10/2020
ms.openlocfilehash: cc7a8df80e719173c7818055ab8771ddd7f73691
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682778"
---
# <a name="create-azure-machine-learning-datasets"></a>建立 Azure 機器學習資料集

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中,您將瞭解如何創建 Azure 機器學習數據集以訪問本地或遠端實驗的數據。

使用 Azure 機器學習資料集,您可以:

* 在存儲中保留單個數據副本,由數據集引用。

* 在模型培訓期間無縫訪問數據,而不必擔心連接字串或數據路徑。

* 共享數據並與其他使用者協作。

## <a name="prerequisites"></a>Prerequisites
' 要建立與使用資料集,您需要:

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立免費帳戶。 試著[Azure 機器學習的免費或付費版本](https://aka.ms/AMLFree)。

* [Azure 機器學習工作區](how-to-manage-workspace.md)。

* [安裝的 Python 的 Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py),其中包括 azurem 資料集包。

> [!NOTE]
> 某些數據集類對[azureml 數據準備](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)包具有依賴關係。 對於 Linux 用戶,這些類僅支援以下發行版:紅帽企業 Linux、Ubuntu、Fedora 和 CentOS。

## <a name="compute-size-guidance"></a>計算大小指南

創建資料集時,請查看計算處理能力和記憶體中數據的大小。 存儲中的數據大小與數據幀中的數據大小不同。 例如,CSV 檔中的數據可以在數據幀中擴展多達 10 倍,因此 1 GB 的 CSV 檔可以在數據幀中變為 10 GB。 

主要因素是數據集在記憶體中有多大,即作為數據框。 我們建議您的計算大小和處理能力包含 RAM 大小的 2 倍。 因此,如果數據幀為 10GB,則希望具有 20 GB RAM 的計算目標,以確保數據幀能夠舒適地放入記憶體並進行處理。 如果數據被壓縮,它可以進一步擴展;以壓縮鑲木地板格式存儲的 20 GB 相對稀疏的數據可以擴展到記憶體中 ±800 GB。 由於 Parquet 檔以列格式儲存數據,因此,如果您只需要一半的列,則只需在記憶體中載入 +400 GB。
 
如果你使用的是熊貓,沒有理由有超過1個vCPU,因為這是它將使用的所有。 您可以透過 Modin 與 Dask/Ray 輕鬆地並行化到單個 Azure 機器學習計算實體/節點上的多個 vCPU,並在需要時擴展到`import pandas as pd``import modin.pandas as pd`大型群集,只需更改為 。 
 
如果數據無法獲得足夠的虛擬數據,則有兩個選項:使用 Spark 或 Dask 等框架對「記憶體不足」的數據執行處理,即數據幀通過分區載入到 RAM 分區並進行處理,最終結果在末尾收集。 如果速度太慢,Spark 或 Dask 允許您橫向擴展到仍可交互使用的群集。 

## <a name="dataset-types"></a>資料集類型

有兩種數據集類型,具體取決於使用者在培訓中使用它們的方式:

* [表格資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)通過分析提供的檔案或檔案清單來表示表格格式的數據。 這使您能夠將數據具體化到熊貓或 Spark 數據幀中。 可以從 .csv、.tsv、.parquet、.jsonl 檔和 SQL`TabularDataset`查詢結果創建 物件。 關於完整清單,請參閱[表格資料集工廠類別](https://aka.ms/tabulardataset-api-reference)。

* [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py)類別參考資料儲存或公共 URL 中的單個或多個檔案。 以此方法,您可以將檔案下載或載入到計算中,作為檔案資料集物件。 這些檔可以採用任何格式,支援更廣泛的機器學習方案,包括深度學習。

要瞭解有關即將進行的 API 變更的更多資訊,請參閱[資料集 API 變更通知](https://aka.ms/tabular-dataset)。

## <a name="create-datasets"></a>建立資料集

通過創建資料集,可以創建對數據來源位置的引用及其元數據的副本。 由於數據保留在其現有位置,因此不會產生額外的存儲成本。 您可以使用`TabularDataset``FileDataset`Python SDKhttps://ml.azure.com或在

要由 Azure 機器學習存取資料,必須從[Azure 資料儲存](how-to-access-data.md)或公共 Web URL 中的路徑創建資料集。 

### <a name="use-the-sdk"></a>使用 SDK

要使用 Python SDK 從[Azure 資料儲存](how-to-access-data.md)建立資料集,請執行以下操作:

1. 驗證您是否擁有`contributor``owner`或 存取已註冊的 Azure 資料儲存。

2. 通過引用數據存儲中的路徑來創建數據集。
> [!Note]
> 可以從多個資料存儲中的多個路徑創建數據集。 可以從中創建數據集的文件數或數據大小沒有硬限制。 但是,對於每個數據路徑,將發送一些請求到存儲服務,以檢查它是指向檔案還是資料夾。 此開銷可能導致性能下降或失敗。 引用包含 1000 個檔的資料夾的數據集被視為引用一個數據路徑。 我們建議創建引用數據存儲中少於 100 個路徑的數據集,以實現最佳性能。

#### <a name="create-a-tabulardataset"></a>建立表格資料集

使用[`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-)`TabularDatasetFactory`類上的方法讀取 .csv 或 .tsv 格式的檔,並創建未註冊的 TabularDataset。 如果要從多個檔讀取結果,結果將聚合到一個表格表示形式中。 

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

預設情況下,在創建表格數據集時,將自動推斷列數據類型。 如果推斷的類型不符合您的預期,則可以使用以下代碼指定列類型。 該參數`infer_column_type`僅適用於從分隔文件創建的數據集。您還可以[瞭解有關受支援的資料型態的更多資訊](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py)。

> [!IMPORTANT] 
> 如果您的儲存位於虛擬網路或防火牆後面,則僅支援透過 SDK 創建資料集。 要創建數據集,請確保在`validate=False``infer_column_types=False``from_delimited_files()`方法中包括參數和方法。 這將繞過初始驗證檢查,並確保可以從這些安全文件創建數據集。 

```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |乘客Id|存活的|Pclass|名稱|性|Age|西布普|帕奇|票證|費用|小屋|已著手
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|布勞德,歐文·哈裡斯先生|male|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|庫明斯,約翰·布蘭得利夫人(弗洛倫斯·布裡格斯·|female|38.0|1|0|PC 17599|71.2833|C85|C
2|3|True|3|海基甯 小姐 萊納|female|26.0|0|0|斯通/O2。 3101282|7.9250||S


要從記憶體中的熊貓數據框創建數據集,請將數據寫入本地檔(如 csv)並從該檔創建數據集。 以下代碼演示此工作流。

```python
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)
upload the local file to a datastore on the cloud
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required
from azureml.core import Workspace, Dataset

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')
# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(datastore.path('data/prepared.csv'))
```

使用類別[`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-)`TabularDatasetFactory`上的方法從 Azure SQL 資料庫讀取:

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

在表格資料集中,可以從資料中的欄或路徑模式資料儲存位置指定時間戳,以啟用時間序列特徵。 此規範允許按時間輕鬆高效地進行過濾。

使用類[`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)`TabularDataset`上的方法指定時間戳列,並啟用按時間進行篩選。 有關詳細資訊,請參閱帶有[NOAA 天氣資料的與 Taba 時間序列相關的 API 簡報](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb)。

```Python
# create a TabularDataset with time series trait
datastore_paths = [(datastore, 'weather/*/*/*/data.parquet')]

# get a coarse timestamp column from the path pattern
dataset = Dataset.Tabular.from_parquet_files(path=datastore_path, partition_format='weather/{coarse_time:yyy/MM/dd}/data.parquet')

# set coarse timestamp to the virtual column created, and fine grain timestamp from a column in the data
dataset = dataset.with_timestamp_columns(fine_grain_timestamp='datetime', coarse_grain_timestamp='coarse_time')

# filter with time-series-trait-specific methods
data_slice = dataset.time_before(datetime(2019, 1, 1))
data_slice = dataset.time_after(datetime(2019, 1, 1))
data_slice = dataset.time_between(datetime(2019, 1, 1), datetime(2019, 2, 1))
data_slice = dataset.time_recent(timedelta(weeks=1, days=1))
```

#### <a name="create-a-filedataset"></a>建立 FileDataset

使用[`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-)`FileDatasetFactory`類別的方法以任何格式載入檔案並建立未註冊的檔案資料集。 如果您的儲存位於虛擬網路或防火牆後面,請設置方法`validate =False``from_files()`中的參數。 這將繞過初始驗證步驟,並確保可以從這些安全文件創建數據集。

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

#### <a name="on-the-web"></a>網路上 
以下步驟和動畫示範如何在 Azure 機器學習工作室中建立https://ml.azure.com資料集 。

![使用 UI 建立資料集](./media/how-to-create-register-datasets/create-dataset-ui.gif)

要在工作室中創建資料集,請:
1. 登錄。 https://ml.azure.com
1. 在左邊窗格的 **「資產**」 部份中選擇**資料集**。 
1. 選擇 **「創建資料集」** 以選擇資料集的來源。 此源可以是本地檔案、資料存儲或公共 URL。
1. 為資料集型態選擇**表格**或**檔案**。
1. 選擇**下一步**以開啟**資料儲存和檔案選擇**窗體。 在此表單,您可以選擇建立後保留資料集的位置,以及選擇要用於資料集的資料檔。 
1. 選擇 **「下一步**」以填充 **「設定」和「預覽**」**和「架構」** 窗體;它們根據檔案類型進行智慧填充,您可以在在這些窗體上創建之前進一步設定數據集。 
1. 選擇 **「下一步**」 以檢視 **「確認詳細資訊**」 表單。 檢查您的選擇並為資料集創建可選的資料設定檔。 深入了解[資料分析](how-to-use-automated-ml-for-ml-models.md#profile)。 
1. 選擇 **「創建**」以完成資料集建立。

## <a name="register-datasets"></a>註冊資料集

要完成創建過程,請使用工作區註冊數據集。 使用[`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-)方法將資料集註冊到工作區,以便與他人共享數據並在各種實驗中重用它們:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> 通過 Azure 機器學習工作室創建的數據集將自動註冊到工作區。

## <a name="create-datasets-with-azure-open-datasets"></a>使用 Azure 開放資料集建立資料集

[Azure 開放資料集](https://azure.microsoft.com/services/open-datasets/)是策劃的公用資料集，您可以使用這些公用資料集，將案例專有的功能新增至機器學習解決方案，以獲得更準確的模型。 資料集包含用於天氣、人口普查、假日、公共安全和位置的公用領域資料，可協助您將機器學習模型定型並擴充預測性解決方案。 打開資料集位於 Microsoft Azure 上的雲端中,並包含在 SDK 和工作區 UI 中。

### <a name="use-the-sdk"></a>使用 SDK

使用 SDK 使用 Azure 開放資料集建立資料集,請確保`pip install azureml-opendatasets`已安裝套件 。 每個離散數據集都由 SDK 中的自己的類表示,某些類`TabularDataset``FileDataset`可以作為 或 同時提供。 有關類別的完整清單,請參考[文件](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)。

您可以檢查某些類別作為`TabularDataset`或`FileDataset`,這允許您直接操作和/或下載檔案。 其他類**只能通過**使用或`get_tabular_dataset()``get_file_dataset()`函數之一獲取數據集。 下面的代碼示例顯示了這些類型的類的幾個示例。

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

註冊從開放數據集創建的數據集時,不會立即下載任何數據,但稍後會從中央存儲位置請求時(例如,在培訓期間)訪問數據。

### <a name="use-the-ui"></a>使用 UI

還可以通過 UI 從開放數據集類創建數據集。 在工作區中,選擇 **「資產**」下的**數據集**選項卡。 在 **「創建資料集**下拉選單」 中,**選擇「 打開資料集**」 。

![使用 UI 開啟資料集](./media/how-to-create-register-datasets/open-datasets-1.png)

通過選擇數據集的磁貼來選擇數據集。 (您可以選擇使用搜尋欄進行篩選。選擇**下一個**。

![選擇資料集](./media/how-to-create-register-datasets/open-datasets-2.png)

選擇用於註冊數據集的名稱,並選擇使用可用的篩選器篩選數據。 在這種情況下,對於公共假日數據集,您將時間段篩選為一年,並將國家/地區代碼篩選為僅美國。 選取 [建立]  。

![設定資料集參數並建立資料集](./media/how-to-create-register-datasets/open-datasets-3.png)

數據集現在在**數據集**下的工作區中可用。 可以像創建的其他數據集一樣使用它。

## <a name="version-datasets"></a>版本資料集

您可以通過創建新版本以相同名稱註冊新數據集。 數據集版本是一種對數據狀態進行書籤的方法,以便您可以應用數據集的特定版本進行實驗或將來的複製。 瞭解有關[數據集版本](how-to-version-track-datasets.md)的更多。
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="access-datasets-in-your-script"></a>存取文稿中的資料集

在 Azure 機器學習計算等計算群集上,可本地和遠端訪問已註冊的數據集。 要跨實驗訪問已註冊的數據集,請使用以下代碼按名稱訪問工作區和已註冊的數據集。 默認情況下,[`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--)`Dataset`類上的方法返回在工作區註冊的數據集的最新版本。

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="next-steps"></a>後續步驟

* [瞭解如何使用資料集進行訓練](how-to-train-with-datasets.md)。
* 使用自動機器學習使用[表格資料集進行培訓](https://aka.ms/automl-dataset)。
* 有關更多資料集培訓範例,請參閱[範例筆記本](https://aka.ms/dataset-tutorial)。
