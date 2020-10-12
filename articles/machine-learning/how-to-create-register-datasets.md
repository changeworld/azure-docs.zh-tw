---
title: 建立 Azure Machine Learning 資料集來存取資料
titleSuffix: Azure Machine Learning
description: 瞭解如何建立 Azure Machine Learning 資料集，以存取您的資料以進行機器學習實驗執行。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.openlocfilehash: 18904987d409f526c2bbd6cd917558e8ce6f751c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842626"
---
# <a name="create-azure-machine-learning-datasets"></a>建立 Azure Machine Learning 資料集



在本文中，您將瞭解如何建立 Azure Machine Learning 資料集，以使用 Azure Machine Learning Python SDK 來存取本機或遠端實驗的資料。 若要瞭解資料集符合 Azure Machine Learning 的整體資料存取工作流程，請參閱 [安全存取資料](concept-data.md#data-workflow) 檔。

藉由建立資料集，您可以建立資料來源位置的參考，以及其中繼資料的複本。 因為資料會保留在現有的位置，所以不會產生額外的儲存成本，也不會對資料來源的完整性產生風險。 此外也會延遲評估資料集，以協助工作流程的效能速度。 您可以從資料存放區、公用 Url 和 [Azure 開放資料集](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md)建立資料集。

針對低程式碼體驗，請 [使用 Azure Machine Learning Studio 建立 Azure Machine Learning 資料集](how-to-connect-data-ui.md#create-datasets)。

使用 Azure Machine Learning 資料集，您可以：

* 在儲存體中保留資料集所參考的單一資料複本。

* 在模型定型期間順暢地存取資料，而不需要擔心連接字串或資料路徑。[深入瞭解如何使用資料集進行定型](how-to-train-with-datasets.md)。

* 共用資料並與其他使用者共同作業。

## <a name="prerequisites"></a>必要條件

若要建立及使用資料集，您需要：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立免費帳戶。 試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* [Azure Machine Learning 工作區](how-to-manage-workspace.md)。

* [已安裝適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)，其中包含 azureml 資料集套件。

    * 建立 [Azure Machine Learning 計算實例](how-to-create-manage-compute-instance.md)，此實例是完全設定且受管理的開發環境，其中包含已安裝的整合式筆記本和 SDK。

    **OR**

    * 使用您自己的 Jupyter 筆記本，並使用 [這些指示](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)自行安裝 SDK。

> [!NOTE]
> 某些資料集類別具有 [azureml dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py&preserve-view=true) 套件的相依性，其僅與64位 Python 相容。 針對 Linux 使用者，只有下列發行版本才支援這些類別： Red Hat Enterprise Linux (7、8) 、Ubuntu (14.04、16.04、18.04) 、Fedora (27、28) 、Debian (8、9) 和 CentOS (7) 。 如果您使用不支援的散發版本，請遵循 [本指南](https://docs.microsoft.com/dotnet/core/install/linux) 來安裝 .net Core 2.1 以繼續進行。 

## <a name="compute-size-guidance"></a>計算大小指引

建立資料集時，請檢查您的計算處理能力和記憶體中的資料大小。 儲存體中的資料大小與資料框架中的資料大小不同。 例如，CSV 檔案中的資料在資料框架中最多可擴大至10倍，因此 1 GB 的 CSV 檔案在資料框架中可能會變成 10 GB。 

如果您的資料已壓縮，則可以進一步擴充;以壓縮的 parquet 格式儲存的 20 GB 相對稀疏資料可以擴充至記憶體中 ~ 800 GB。 由於 Parquet 檔案會以單欄式格式儲存資料，如果您只需要一半的資料行，則只需要在記憶體中載入 ~ 400 GB。

[深入瞭解如何優化 Azure Machine Learning 中的資料處理](concept-optimize-data-processing.md)。

## <a name="dataset-types"></a>資料集類型

有兩個資料集類型，根據使用者在訓練中取用它們的方式而定;FileDatasets 和 TabularDatasets。 這兩種類型都可以在涉及、估算器、AutoML、hyperDrive 和管線的 Azure Machine Learning 訓練工作流程中使用。 

### <a name="filedataset"></a>FileDataset

[FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py&preserve-view=true)會參考資料存放區或公用 url 中的單一或多個檔案。 如果您的資料已清理，而且準備好在定型實驗中使用，您可以將檔案 [下載或掛接](how-to-train-with-datasets.md#mount-vs-download) 到您的計算中作為 FileDataset 物件。 

我們建議您 FileDatasets 機器學習工作流程，因為來源檔案可以採用任何格式，而這可提供更廣泛的機器學習案例，包括深度學習。

使用 [PYTHON SDK](#create-a-filedataset) 或 [Azure Machine Learning studio](how-to-connect-data-ui.md#create-datasets) 建立 FileDataset。
### <a name="tabulardataset"></a>TabularDataset

[TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true)藉由剖析提供的檔案或檔案清單，以表格格式代表資料。 這可讓您將資料具體化為 pandas 或 Spark 資料框架，讓您可以使用熟悉的資料準備和定型程式庫，而不需要離開您的筆記本。 您可以 `TabularDataset` 從 .csv、tsv、parquet、jsonl 檔案，以及從 [SQL 查詢結果](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py&preserve-view=true#&preserve-view=truefrom-sql-query-query--validate-true--set-column-types-none--query-timeout-30-)建立物件。

使用 TabularDatasets，您可以從資料中的資料行或從儲存路徑模式資料的任何位置指定時間戳記，以啟用時間序列特性。 此規格可讓您依時間輕鬆且有效率地進行篩選。 如需範例，請參閱 [表格式時間序列相關的 API 示範與 NOAA 氣象資料](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb)。

使用 [PYTHON SDK](#create-a-tabulardataset) 或 [Azure Machine Learning studio](how-to-connect-data-ui.md#create-datasets)建立 TabularDataset。

>[!NOTE]
> 透過 Azure Machine Learning studio 產生的 AutoML 工作流程目前只支援 TabularDatasets。 

## <a name="access-datasets-in-a-virtual-network"></a>存取虛擬網路中的資料集

如果您的工作區位於虛擬網路中，您必須將資料集設定為略過驗證。 如需如何在虛擬網路中使用資料存放區和資料集的詳細資訊，請參閱 [保護工作區和相關聯的資源](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)。

<a name="datasets-sdk"></a>

## <a name="create-datasets"></a>建立資料集

若要讓 Azure Machine Learning 可存取資料，您必須從 [Azure 資料存放區](how-to-access-data.md) 或公用 web url 中的路徑建立資料集。 

使用 Python SDK 從 [Azure 資料](how-to-access-data.md) 存放區建立資料集：

1. 確認您擁有 `contributor` 或 `owner` 存取已註冊的 Azure 資料存放區。

2. 藉由參考資料存放區中的路徑來建立資料集。 您可以從多個資料存放區中的多個路徑建立資料集。 您可以建立資料集的檔案或資料大小數量沒有硬性限制。 

> [!NOTE]
> 針對每個資料路徑，會將幾個要求傳送至儲存體服務，以檢查其是否指向檔案或資料夾。 此額外負荷可能會導致效能降低或失敗。 在內部參考一個具有1000檔案之資料夾的資料集會被視為參考一個資料路徑。 建議您在資料存放區中建立參考小於100路徑的資料集，以獲得最佳效能。

### <a name="create-a-filedataset"></a>建立 FileDataset

在 [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py&preserve-view=true#&preserve-view=truefrom-files-path--validate-true-) 類別上使用方法 `FileDatasetFactory` 來載入任何格式的檔案，以及建立未註冊的 FileDataset。 

如果您的儲存體位於虛擬網路或防火牆後方，請 `validate=False` 在您的方法中設定參數 `from_files()` 。 這會略過初始驗證步驟，並確保您可以從這些安全檔案中建立資料集。 深入瞭解如何 [在虛擬網路中使用資料存放區和資料集](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)。

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```
若要在工作區中重複使用並共用實驗中的資料集，請 [註冊您的資料集](#register-datasets)。 

> [!TIP] 
> 從本機目錄上傳檔案，並使用公用預覽方法在單一方法中建立 FileDataset， [upload_directory ( # B1 ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py&preserve-view=true#methods)。 此方法是 [實驗](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py&preserve-view=true#stable-vs-experimental) 性預覽功能，而且可能隨時變更。 
> 
>  此方法會將資料上傳至您的基礎儲存體，因此會產生儲存體成本。 
### <a name="create-a-tabulardataset"></a>建立 TabularDataset

在 [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) 類別上使用方法 `TabularDatasetFactory` 來讀取 .csv 或 tsv 格式的檔案，以及建立未註冊的 TabularDataset。 如果您要從多個檔案讀取，結果會匯總成單一的表格式標記法。 

如果您的儲存體位於虛擬網路或防火牆後方，請 `validate=False` 在您的方法中設定參數 `from_delimited_files()` 。 這會略過初始驗證步驟，並確保您可以從這些安全檔案中建立資料集。 深入瞭解如何 [在虛擬網路中使用資料存放區和資料集](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)。

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

依預設，當您建立 TabularDataset 時，會自動推斷資料行資料類型。 如果推斷的類型不符合您的預期，您可以使用下列程式碼來指定資料行類型。 參數 `infer_column_type` 只適用于從分隔的檔案建立的資料集。 [深入瞭解支援的資料類型](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py&preserve-view=true)。


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| (索引) |PassengerId|存活的|Pclass|名稱|性|年齡|SibSp|Parch|票證|費用|小屋|著手
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund，Owen Harris|male|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings，Mrs John Bradley (Florence Briggs Th .。。|female|38.0|1|0|電腦17599|71.2833|C85|C
2|3|True|3|Heikkinen，遺漏。 Laina|female|26.0|0|0|STON/O2。 3101282|7.9250||S

若要在工作區中重複使用和共用實驗中的資料集，請 [註冊您的資料集](#register-datasets)。

## <a name="create-a-dataset-from-pandas-dataframe"></a>從 pandas 資料框架建立資料集

若要從記憶體中的 pandas 資料框架建立 TabularDataset，請將資料寫入本機檔案（例如 csv），然後從該檔案建立您的資料集。 下列程式碼示範此工作流程。

```python
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required

from azureml.core import Workspace, Dataset
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)

# upload the local file to a datastore on the cloud

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')

# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(path = [(datastore, ('data/prepared.csv')]
```

> [!TIP]
> 使用具有公用預覽方法和的單一方法，從記憶體中的 spark 或 pandas 資料框架建立和註冊 TabularDataset [`register_spark_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true#methods) [`register_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true#methods) 。 這些註冊方法是 [實驗](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py&preserve-view=true#stable-vs-experimental) 性預覽功能，而且可能隨時變更。 
> 
>  這些方法會將資料上傳至您的基礎儲存體，因此會產生儲存體成本。 

## <a name="register-datasets"></a>註冊資料集

若要完成建立程式，請向工作區註冊您的資料集。 [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py&preserve-view=true#&preserve-view=trueregister-workspace--name--description-none--tags-none--create-new-version-false-)您可以使用方法向工作區註冊資料集，以便與其他人共用資料集，並在工作區中的實驗之間重複使用這些資料集：

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-using-azure-resource-manager"></a>使用 Azure Resource Manager 建立資料集

有一些範本 [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-dataset-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) 可用於建立資料集。

如需使用這些範本的詳細資訊，請參閱 [使用 Azure Resource Manager 範本建立 Azure Machine Learning 的工作區](how-to-create-workspace-template.md)。


## <a name="create-datasets-with-azure-open-datasets"></a>使用 Azure 開放資料集建立資料集

[Azure 開放資料集](https://azure.microsoft.com/services/open-datasets/)是策劃的公用資料集，您可以使用這些公用資料集，將案例專有的功能新增至機器學習解決方案，以獲得更準確的模型。 資料集包含用於天氣、人口普查、假日、公共安全和位置的公用領域資料，可協助您將機器學習模型定型並擴充預測性解決方案。 開放資料集位於雲端上的 Microsoft Azure，並包含在 SDK 和 studio 中。

瞭解如何 [從 Azure 開放資料集建立 Azure Machine Learning 資料集](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md)。 

## <a name="train-with-datasets"></a>使用資料集定型

在機器學習實驗中使用您的資料集來定型 ML 模型。 [深入瞭解如何使用資料集定型](how-to-train-with-datasets.md)

## <a name="version-datasets"></a>版本資料集

您可以藉由建立新的版本，在相同名稱下註冊新的資料集。 資料集版本是一種將資料的狀態加入書簽的方式，讓您可以套用特定版本的資料集進行實驗或未來的複製。 深入瞭解 [資料集版本](how-to-version-track-datasets.md)。
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

* 瞭解 [如何使用資料集進行定型](how-to-train-with-datasets.md)。
* 使用自動化機器學習來 [使用 TabularDatasets 進行定型](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)。
* 如需更多資料集定型範例，請參閱 [範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)。
