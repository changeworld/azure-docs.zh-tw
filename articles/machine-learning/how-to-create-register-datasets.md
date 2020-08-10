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
ms.date: 07/31/2020
ms.openlocfilehash: b2252a70aea6df755bb8b37c36b77b08db819ba9
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037536"
---
# <a name="create-azure-machine-learning-datasets"></a>建立 Azure Machine Learning 資料集

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何建立 Azure Machine Learning 資料集，以存取本機或遠端實驗的資料。 若要瞭解資料集如何放入 Azure Machine Learning 的整體資料存取工作流程中，請參閱[安全存取資料](concept-data.md#data-workflow)一文。

藉由建立資料集，您可以建立資料來源位置的參考，以及其中繼資料的複本。 因為資料會保留在現有的位置，所以您不會產生額外的儲存成本，也不會造成資料來源的完整性風險。 此外，也會延遲評估資料集，以協助工作流程效能速度。 您可以從資料存放區、公用 Url 和[Azure 開放資料集](../open-datasets/how-to-create-dataset-from-open-dataset.md)建立資料集。

有了 Azure Machine Learning 資料集，您可以：

* 在您的儲存體中保留單一資料複本（由資料集所參考）。

* 在模型定型期間順暢地存取資料，而不需要擔心連接字串或資料路徑。[深入瞭解如何使用資料集進行定型](how-to-train-with-datasets.md)。

* 共用資料並與其他使用者共同作業。

## <a name="prerequisites"></a>必要條件

若要建立及使用資料集，您需要：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立免費帳戶。 試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* [Azure Machine Learning 工作區](how-to-manage-workspace.md)。

* [已安裝適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)，其中包括 azureml 資料集封裝。

    * 建立[Azure Machine Learning 計算實例](concept-compute-instance.md#managing-a-compute-instance)，這是完全設定且受管理的開發環境，其中包含已安裝的整合式筆記本和 SDK。

    **OR**

    * 使用您自己的 Jupyter 筆記本，並透過[這些指示](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)自行安裝 SDK。

> [!NOTE]
> 有些資料集類別具有[dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)套件的相依性，這只與64位 Python 相容。 針對 Linux 使用者，只有下列散發版本支援這些類別： Red Hat Enterprise Linux (7、8) 、Ubuntu (14.04、16.04、18.04) 、Fedora (27、28) 、Debian (8、9) 和 CentOS (7) 。

## <a name="compute-size-guidance"></a>計算大小指引

建立資料集時，請檢查您的計算處理能力和記憶體中的資料大小。 儲存體中的資料大小與資料框架中的資料大小不同。 例如，CSV 檔案中的資料最多可在資料框架中展開10倍，因此 1 GB 的 CSV 檔案在資料框架中可能會變成 10 GB。 

如果您的資料已壓縮，則可以進一步擴充;以壓縮的 parquet 格式儲存的 20 GB 相對稀疏資料，在記憶體中可以擴充至 ~ 800 GB。 由於 Parquet 檔案會以單欄式格式儲存資料，如果您只需要一半的資料行，則您只需要在記憶體中載入 ~ 400 GB。

[深入瞭解如何在 Azure Machine Learning 中優化資料處理](concept-optimize-data-processing.md)。

## <a name="dataset-types"></a>資料集類型

有兩種資料集類型，根據使用者在定型時的取用方式而定;FileDatasets 和 TabularDatasets。 這兩種類型都可以用於涉及、估算器、AutoML、hyperDrive 和管線的 Azure Machine Learning 訓練工作流程。 

### <a name="filedataset"></a>FileDataset

[FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py)會參考您資料存放區或公用 url 中的單一或多個檔案。 如果您的資料已清理，而且準備好在訓練實驗中使用，您可以將檔案[下載或掛接](how-to-train-with-datasets.md#mount-vs-download)至您的計算，做為 FileDataset 物件。 

我們建議您針對機器學習工作流程 FileDatasets，因為來源檔案可以是任何格式，這可讓您更廣泛的機器學習案例，包括深度學習。

使用[PYTHON SDK](#create-a-filedataset)或[Azure Machine Learning studio](#create-datasets-in-the-studio)建立 FileDataset

### <a name="tabulardataset"></a>TabularDataset

[TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)會藉由剖析提供的檔案或檔案清單，以表格格式表示資料。 這讓您能夠將資料具體化成 pandas 或 Spark 資料框架，讓您可以使用熟悉的資料準備和訓練程式庫，而不需要離開您的筆記本。 您可以 `TabularDataset` 從 .csv、tsv、parquet、. jsonl 檔案和[SQL 查詢結果](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-)建立物件。

透過 TabularDatasets，您可以指定資料行中的時間戳記，或從路徑模式資料儲存的任何位置，以啟用時間序列特性。 此規格可讓您依時間輕鬆且有效率地進行篩選。 如需範例，請參閱[使用 NOAA 氣象資料的表格式時間序列相關 API 示範](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb)。

使用[PYTHON SDK](#create-a-tabulardataset)或[Azure Machine Learning studio](#create-datasets-in-the-studio)建立 TabularDataset。

>[!NOTE]
> 透過 Azure Machine Learning studio 產生的 AutoML 工作流程目前僅支援 TabularDatasets。 

## <a name="access-datasets-in-a-virtual-network"></a>存取虛擬網路中的資料集

如果您的工作區位於虛擬網路中，您必須將資料集設定為略過驗證。 如需如何在虛擬網路中使用資料存放區和資料集的詳細資訊，請參閱[使用私人虛擬網路進行定型 & 推斷期間的網路隔離](how-to-enable-virtual-network.md#use-datastores-and-datasets)。

<a name="datasets-sdk"></a>

## <a name="create-datasets-via-the-sdk"></a>透過 SDK 建立資料集

 若要讓 Azure Machine Learning 可存取的資料，必須從[Azure 資料存放區](how-to-access-data.md)或公用 web url 中的路徑建立資料集。 

若要使用 Python SDK 從[Azure 資料](how-to-access-data.md)存放區建立資料集：

1. 請確認您擁有 `contributor` 或已 `owner` 註冊 Azure 資料存放區的存取權。

2. 藉由參考資料存放區中的路徑來建立資料集。 您可以從多個資料存放區中的多個路徑建立資料集。 您可以從中建立資料集的檔案或資料大小沒有固定限制。 

> [!Note]
> 針對每個資料路徑，會將幾個要求傳送至儲存體服務，以檢查它是否指向檔案或資料夾。 此額外負荷可能會導致效能降低或失敗。 參考一個含有1000檔案的資料夾的資料集會被視為參考一個資料路徑。 建議您在資料存放區中建立參考小於100路徑的資料集，以獲得最佳效能。

### <a name="create-a-filedataset"></a>建立 FileDataset

在 [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) 類別上使用方法， `FileDatasetFactory` 以任何格式載入檔案，並建立未註冊的 FileDataset。 

如果您的存放裝置位於虛擬網路或防火牆後方，請 `validate=False` 在您的方法中設定參數 `from_files()` 。 這會略過初始驗證步驟，並確保您可以從這些安全檔案建立資料集。 深入瞭解如何[在虛擬網路中使用資料存放區和資料集](how-to-enable-virtual-network.md#use-datastores-and-datasets)。

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

### <a name="create-a-tabulardataset"></a>建立 TabularDataset

在 [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) 類別上使用方法 `TabularDatasetFactory` 來讀取 .csv 或 tsv 格式的檔案，並建立未註冊的 TabularDataset。 如果您要讀取多個檔案，結果會匯總成一個表格式表示。 

如果您的存放裝置位於虛擬網路或防火牆後方，請 `validate=False` 在您的方法中設定參數 `from_delimited_files()` 。 這會略過初始驗證步驟，並確保您可以從這些安全檔案建立資料集。 深入瞭解如何[在虛擬網路中使用資料存放區和資料集](how-to-enable-virtual-network.md#use-datastores-and-datasets)。

下列程式碼會依名稱取得現有的工作區和所需的資料存放區。 然後將資料存放區和檔案位置傳遞給 `path` 參數，以建立新的 TabularDataset `weather_ds` 。

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

根據預設，當您建立 TabularDataset 時，會自動推斷資料行資料類型。 如果推斷的類型不符合您的預期，您可以使用下列程式碼來指定資料行類型。 參數 `infer_column_type` 僅適用于從分隔檔案建立的資料集。 [深入瞭解支援的資料類型](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py)。


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| (索引) |PassengerId|存活的|Pclass|名稱|性別|年齡|SibSp|Parch|票證|費用|插槽|著手
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund，Mr. Owen Harris|male|22.0|1|0|A/5 21171|7.2500||S
1|2|是|1|Cumings，Mrs Bradley (Florence Briggs Th .。。|female|38.0|1|0|電腦17599|71.2833|C85|C
2|3|是|3|Heikkinen，錯過。 Laina|female|26.0|0|0|STON/O2。 3101282|7.9250||S

### <a name="create-a-dataset-from-pandas-dataframe"></a>從 pandas 資料框架建立資料集

若要從 [記憶體 pandas] 資料框架建立 TabularDataset，請將資料寫入本機檔案（例如 csv），然後從該檔案建立資料集。 下列程式碼示範此工作流程。

```python
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required

from azureml.core import Workspace, Dataset
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)
upload the local file to a datastore on the cloud

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

## <a name="register-datasets"></a>註冊資料集

若要完成建立程式，請向工作區註冊您的資料集。 使用 [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) 方法向您的工作區註冊資料集，以便與其他人共用，並跨工作區中的實驗重複使用它們：

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

<a name="datasets-ui"></a>
## <a name="create-datasets-in-the-studio"></a>在 studio 中建立資料集
下列步驟和動畫示範如何在[Azure Machine Learning studio](https://ml.azure.com)中建立資料集。

> [!Note]
> 透過 Azure Machine Learning studio 建立的資料集會自動註冊到工作區。

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

## <a name="create-datasets-with-azure-open-datasets"></a>使用 Azure 開放資料集建立資料集

[Azure 開放資料集](https://azure.microsoft.com/services/open-datasets/)是策劃的公用資料集，您可以使用這些公用資料集，將案例專有的功能新增至機器學習解決方案，以獲得更準確的模型。 資料集包含用於天氣、人口普查、假日、公共安全和位置的公用領域資料，可協助您將機器學習模型定型並擴充預測性解決方案。 開放資料集位於雲端上的 Microsoft Azure，同時包含在 SDK 和 studio 中。

瞭解如何[從 Azure 開放資料集建立 Azure Machine Learning 資料集](../open-datasets/how-to-create-dataset-from-open-dataset.md)。 

## <a name="train-with-datasets"></a>使用資料集定型

在您的機器學習實驗中使用您的資料集來定型 ML 模型。 [深入瞭解如何使用資料集進行定型](how-to-train-with-datasets.md)

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

## <a name="next-steps"></a>後續步驟

* 瞭解[如何使用資料集進行定型](how-to-train-with-datasets.md)。
* 使用自動化機器學習來[訓練 TabularDatasets](https://aka.ms/automl-dataset)。
* 如需更多資料集定型範例，請參閱[範例筆記本](https://aka.ms/dataset-tutorial)。
