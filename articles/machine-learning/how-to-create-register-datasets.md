---
title: 建立用來存取資料的 Azure Machine Learning 資料集
titleSuffix: Azure Machine Learning
description: 瞭解如何建立 Azure Machine Learning 資料集，以存取您的資料以進行機器學習實驗執行。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 06/29/2020
ms.openlocfilehash: a220a7279cbb5ba75c8aa803cb4bd709442a52fe
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87326387"
---
# <a name="create-azure-machine-learning-datasets"></a>建立 Azure Machine Learning 資料集

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何建立 Azure Machine Learning 資料集，以存取本機或遠端實驗的資料。 若要瞭解資料集如何放入 Azure Machine Learning 的整體資料存取工作流程中，請參閱[安全存取資料](concept-data.md#data-workflow)一文。

有了 Azure Machine Learning 資料集，您可以：

* 在您的儲存體中保留單一資料複本（由資料集所參考）。

* 在模型定型期間順暢地存取資料，而不需要擔心連接字串或資料路徑。

* 共用資料並與其他使用者共同作業。

## <a name="prerequisites"></a>必要條件

若要建立及使用資料集，您需要：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立免費帳戶。 試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* [Azure Machine Learning 工作區](how-to-manage-workspace.md)。

* [已安裝適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)，其中包括 azureml 資料集封裝。

> [!NOTE]
> 有些資料集類別具有[dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)套件的相依性，這只與64位 Python 相容。 針對 Linux 使用者，只有下列散發版本支援這些類別： Red Hat Enterprise Linux （7、8）、Ubuntu （14.04、16.04、18.04）、Fedora （27，28）、Debian （8，9）和 CentOS （7）。

## <a name="compute-size-guidance"></a>計算大小指引

建立資料集時，請檢查您的計算處理能力和記憶體中的資料大小。 儲存體中的資料大小與資料框架中的資料大小不同。 例如，CSV 檔案中的資料最多可在資料框架中展開10倍，因此 1 GB 的 CSV 檔案在資料框架中可能會變成 10 GB。 

主要因素是資料集在記憶體中的大小，亦即資料框架。 我們建議您的計算大小和處理能力，包含 RAM 大小的2倍。 因此，如果您的資料框架是 10 gb，則您想要具有20個以上 RAM 的計算目標，以確保資料框架可納入記憶體中並加以處理。 如果您的資料已壓縮，則可以進一步擴充;以壓縮的 parquet 格式儲存的 20 GB 相對稀疏資料，在記憶體中可以擴充至 ~ 800 GB。 由於 Parquet 檔案會以單欄式格式儲存資料，如果您只需要一半的資料行，則您只需要在記憶體中載入 ~ 400 GB。
 
如果您使用 Pandas，則不會有超過1個 vCPU 的理由，因為這就是它將會使用的。 您可以透過 Modin 和 Dask/Ray 輕鬆地平行處理單一 Azure Machine Learning 計算實例/節點上的多個個 vcpu，並視需要向外延展至大型叢集，只要 `import pandas as pd` 將變更為即可 `import modin.pandas as pd` 。 
 
如果您無法為數據取得夠大的虛擬機器，您有兩個選項：使用 Spark 或 Dask 之類的架構來執行資料「記憶體不足」的處理，亦即，資料框架會依分割區載入 RAM 分割並加以處理，最後的結果會在結尾收集。 如果此速度太慢，Spark 或 Dask 可讓您相應放大到仍然可以互動方式使用的叢集。 

## <a name="dataset-types"></a>資料集類型

有兩種資料集類型，根據使用者在定型中取用它們的方式而定：

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)藉由剖析提供的檔案或檔案清單，以表格格式表示資料。 這讓您能夠將資料具體化為 Pandas 或 Spark 資料框架。 您可以 `TabularDataset` 從 .csv、tsv、parquet、. jsonl 檔案和 SQL 查詢結果建立物件。 如需完整清單，請參閱[TabularDatasetFactory 類別](https://aka.ms/tabulardataset-api-reference)。

* [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py)類別會參考資料存放區或公用 url 中的單一或多個檔案。 藉由這個方法，您可以將檔案下載或掛接至您的計算，做為 FileDataset 物件。 這些檔案可以是任何格式，可提供更廣泛的機器學習服務案例，包括深度學習。 

## <a name="create-datasets"></a>建立資料集

藉由建立資料集，您可以建立資料來源位置的參考，以及其中繼資料的複本。 因為資料會保留在其現有的位置，所以您不會產生額外的儲存成本。 您可以 `TabularDataset` `FileDataset` 使用 Python SDK 或 Azure Machine Learning studio （位於）來建立和資料集 https://ml.azure.com 。

若要讓 Azure Machine Learning 可存取的資料，必須從[Azure 資料存放區](how-to-access-data.md)或公用 web url 中的路徑建立資料集。 

### <a name="use-the-sdk"></a>使用 SDK

若要使用 Python SDK 從[Azure 資料](how-to-access-data.md)存放區建立資料集：

1. 請確認您擁有 `contributor` 或已 `owner` 註冊 Azure 資料存放區的存取權。

2. 藉由參考資料存放區中的路徑來建立資料集。

> [!Note]
> 您可以從多個資料存放區中的多個路徑建立資料集。 您可以從中建立資料集的檔案或資料大小沒有固定限制。 不過，針對每個資料路徑，會將幾個要求傳送至儲存體服務，以檢查它是否指向檔案或資料夾。 此額外負荷可能會導致效能降低或失敗。 參考一個含有1000檔案的資料夾的資料集會被視為參考一個資料路徑。 建議您在資料存放區中建立參考小於100路徑的資料集，以獲得最佳效能。

#### <a name="create-a-tabulardataset"></a>建立 TabularDataset

在 [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) 類別上使用方法 `TabularDatasetFactory` 來讀取 .csv 或 tsv 格式的檔案，並建立未註冊的 TabularDataset。 如果您要讀取多個檔案，結果會匯總成一個表格式表示。 

下列程式碼會依名稱取得工作區現有的工作區和所需的資料存放區。 然後將資料存放區和檔案位置傳遞給 `path` 參數，以建立新的 TabularDataset `weather_ds` 。

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

根據預設，當您建立 TabularDataset 時，會自動推斷資料行資料類型。 如果推斷的類型不符合您的預期，您可以使用下列程式碼來指定資料行類型。 參數 `infer_column_type` 僅適用于從分隔檔案建立的資料集。 您也可以[深入瞭解支援的資料類型](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py)。

> [!IMPORTANT] 
> 如果您的存放裝置位於虛擬網路或防火牆後方，則只支援透過 SDK 建立資料集。 若要建立資料集，請務必 `validate=False` `infer_column_types=False` 在您的方法中包含參數和 `from_delimited_files()` 。 這會略過初始驗證檢查，並確保您可以從這些安全檔案建立資料集。 

```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|指數|PassengerId|存活的|Pclass|名稱|性別|年齡|SibSp|Parch|票證|費用|插槽|著手
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund，Mr. Owen Harris|male|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings，Mrs John Bradley （Florence Briggs Th .。。|female|38.0|1|0|電腦17599|71.2833|C85|C
2|3|True|3|Heikkinen，錯過。 Laina|female|26.0|0|0|STON/O2。 3101282|7.9250||S

若要從記憶體中的 pandas 資料框架建立資料集，請將資料寫入本機檔案（例如 csv），然後從該檔案建立資料集。 下列程式碼示範此工作流程。

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

在 [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-) 類別上使用方法 `TabularDatasetFactory` 來讀取 Azure SQL Database：

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore. Take note of double parenthesis.
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

在 TabularDatasets 中，您可以指定資料行中的時間戳記，或從中儲存路徑模式資料以啟用時間序列特性的任何位置。 此規格可讓您依時間輕鬆且有效率地進行篩選。

在 [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) 類別上使用方法 `TabularDataset` 來指定時間戳記資料行，並依時間啟用篩選。 如需詳細資訊，請參閱[表格式時間序列相關 API 示範與 NOAA 天氣資料](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb)。

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

在 [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) 類別上使用方法， `FileDatasetFactory` 以任何格式載入檔案，並建立未註冊的 FileDataset。 如果您的存放裝置位於虛擬網路或防火牆後方，請 `validate=False` 在您的方法中設定參數 `from_files()` 。 這會略過初始驗證步驟，並確保您可以從這些安全檔案建立資料集。

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
下列步驟和動畫示範如何在 Azure Machine Learning studio 中建立資料集 https://ml.azure.com 。

![使用 UI 建立資料集](./media/how-to-create-register-datasets/create-dataset-ui.gif)

若要在 studio 中建立資料集：
1. 在登入 https://ml.azure.com 。
1. 在左窗格的 [**資產**] 區段中，選取 [**資料集**]。 
1. 選取 [**建立資料集**] 以選擇資料集的來源。 此來源可以是本機檔案、資料存放區或公用 Url。
1. 選取**Tabular** [表格式 **] 或 [** 檔案] 做為資料集類型。
1. 選取 **[下一步]** 以開啟資料存放區**和檔案選擇**表單。 在此表單上，您可以選取要在建立資料集之後保留的位置，以及選取要用於資料集的資料檔案。 
    1. 如果您的資料位於虛擬網路中，請啟用 [略過驗證]。 深入瞭解[虛擬網路隔離和隱私權](how-to-enable-virtual-network.md#machine-learning-studio)。
1. 選取 **[下一步]** 以填入**設定和預覽**和**架構**表單;它們會根據檔案類型以智慧方式填入，您可以在建立這些表單之前進一步設定您的資料集。 
1. 選取 **[下一步]** 以查看 [**確認詳細資料**] 表單。 檢查您的選擇，並為您的資料集建立選擇性的資料設定檔。 深入了解[資料分析](how-to-use-automated-ml-for-ml-models.md#profile)。 
1. 選取 [**建立**] 以完成建立資料集。

## <a name="register-datasets"></a>註冊資料集

若要完成建立程式，請向工作區註冊您的資料集。 使用 [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) 方法向您的工作區註冊資料集，以便與其他人共用，並跨工作區中的實驗重複使用它們：

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> 透過 Azure Machine Learning studio 建立的資料集會自動註冊到工作區。

## <a name="create-datasets-with-azure-open-datasets"></a>使用 Azure 開放資料集建立資料集

[Azure 開放資料集](https://azure.microsoft.com/services/open-datasets/)是策劃的公用資料集，您可以使用這些公用資料集，將案例專有的功能新增至機器學習解決方案，以獲得更準確的模型。 資料集包含用於天氣、人口普查、假日、公共安全和位置的公用領域資料，可協助您將機器學習模型定型並擴充預測性解決方案。 開放資料集位於 Microsoft Azure 的雲端，同時包含在 SDK 和工作區 UI 中。

### <a name="use-the-sdk"></a>使用 SDK

若要使用 SDK 中的 Azure 開放資料集來建立資料集，請確定您已使用安裝套件 `pip install azureml-opendatasets` 。 每個離散資料集會以自己在 SDK 中的類別來表示，而某些類別則可當做 `TabularDataset` 、 `FileDataset` 或兩者使用。 如需類別的完整清單，請參閱[參考檔](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)。

您可以將某些類別抓取為 `TabularDataset` 或 `FileDataset` ，以讓您直接操作和（或）下載檔案。 其他**類別只能使用**其中一個或函數來取得資料 `get_tabular_dataset()` 集 `get_file_dataset()` 。 下列程式碼範例顯示這些類別類型的一些範例。

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

當您註冊從開啟的資料集建立的資料集時，不會立即下載任何資料，但稍後會在從中央儲存位置要求時（例如，在定型期間）存取資料。

### <a name="use-the-ui"></a>使用 UI

您也可以透過 UI，從開啟的資料集類別建立資料集。 在工作區中，選取 [**資產**] 底下的 [**資料集**] 索引標籤。 在 [**建立資料集**] 下拉式功能表中，選取 [**從開啟的資料集**]。

![使用 UI 開啟資料集](./media/how-to-create-register-datasets/open-datasets-1.png)

選取資料集，選取其磚。 （您可以選擇使用 [搜尋] 列進行篩選）。選取 **[下一步]**。

![選擇資料集](./media/how-to-create-register-datasets/open-datasets-2.png)

選擇用來註冊資料集的名稱，並選擇性地使用可用的篩選準則來篩選資料。 在此情況下，針對公用假日資料集，您可以將時間週期篩選為僅限一年，並將國家/地區代碼篩選為美國。 選取 [建立]。

![設定資料集參數並建立資料集](./media/how-to-create-register-datasets/open-datasets-3.png)

資料集現在可在您的工作區中的 [**資料集**] 下取得。 您可以使用與您所建立的其他資料集相同的方式來使用它。

## <a name="version-datasets"></a>版本資料集

您可以建立新的版本，以在相同名稱下註冊新的資料集。 資料集版本是將資料的狀態加入書簽的方式，以便您可以套用特定版本的資料集來進行實驗或未來的複製。 深入瞭解[資料集版本](how-to-version-track-datasets.md)。
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

## <a name="access-datasets-in-your-script"></a>存取腳本中的資料集

已註冊的資料集會在計算叢集（例如 Azure Machine Learning 計算）上，從本機和遠端存取。 若要跨實驗存取已註冊的資料集，請使用下列程式碼依名稱存取您的工作區和已註冊的資料集。 根據預設， [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) 類別上的方法 `Dataset` 會傳回已向工作區註冊之資料集的最新版本。

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

## <a name="access-datasets-in-a-virtual-network"></a>存取虛擬網路中的資料集

如果您的工作區位於虛擬網路中，您必須將資料集設定為略過驗證。 如需如何在虛擬網路中使用資料存放區和資料集的詳細資訊，請參閱[使用私人虛擬網路進行定型 & 推斷期間的網路隔離](how-to-enable-virtual-network.md#use-datastores-and-datasets)。

## <a name="next-steps"></a>後續步驟

* 瞭解[如何使用資料集進行定型](how-to-train-with-datasets.md)。
* 使用自動化機器學習來[訓練 TabularDatasets](https://aka.ms/automl-dataset)。
* 如需更多資料集定型範例，請參閱[範例筆記本](https://aka.ms/dataset-tutorial)。
