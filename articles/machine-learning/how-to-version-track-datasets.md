---
title: 資料集版本控制
titleSuffix: Azure Machine Learning
description: 瞭解如何對數據集進行最佳版本控制,以及版本控制如何使用機器學習管道。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.custom: ''
ms.openlocfilehash: 5bd4436fc63fb570f052606ab557dbcf243cf5e7
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476864"
---
# <a name="version-and-track-datasets-in-experiments"></a>在實驗中的版本和追蹤資料集
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中,您將瞭解如何對 Azure 機器學習數據集進行版本控制並跟蹤,以便重現。 數據集版本控制是一種對數據狀態進行書籤的方法,以便您可以為將來的實驗應用數據集的特定版本。

典型的版本控制方案:

* 當新資料可用於再培訓時
* 套用不同的資料準備或功能專案方法時

## <a name="prerequisites"></a>Prerequisites

在本教學課程中，您需要：

- [安裝的 Python 的 Azure 機器學習 SDK。](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) 此 SDK 包括[azureml 資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py)套件。
    
- [Azure 機器學習工作區](concept-workspace.md)。 以執行以下代碼檢索現有代碼,或[建立新工作區](how-to-manage-workspace.md)。

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- [Azure 機器學習資料集](how-to-create-register-datasets.md)。

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>註冊與檢索資料集版本

通過註冊數據集,您可以跨實驗和同事對數據集進行版本化、重用和共用。 您可以以相同名稱註冊多個數據集,並按名稱和版本號檢索特定版本。

### <a name="register-a-dataset-version"></a>註冊資料集版本

以下代碼通過將`titanic_ds``create_new_version`參數設置`True`為 註冊數據集的新版本。 如果沒有在工作區中註冊的現有`titanic_ds`數據集,代碼將創建一個名稱的新數據`titanic_ds`集 ,並將其版本設置為 1。

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>依名稱檢索資料集

默認情況下,`Dataset`類上的[get_by_name()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--)方法傳回在工作區註冊的數據集的最新版本。 

以下代碼獲取數據集的版本`titanic_ds`1。

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>版本控制最佳實作

建立資料集版本時,*不會*使用工作區創建額外的資料複本。 由於數據集是存儲服務中數據的引用,因此您具有由存儲服務管理的單一真實來源。

>[!IMPORTANT]
> 如果數據集引用的數據被覆蓋或刪除,則調用數據集的特定版本*不會*還原更改。

從數據集載入數據時,始終載入資料集引用的當前資料內容。 如果要確保每個數據集版本都是可重現的,我們建議您不要修改數據集版本引用的數據內容。 當新數據進來時,將新數據檔保存到單獨的數據資料夾中,然後創建新的數據集版本以包括來自該新資料夾中的數據。

以下影像與範例代碼顯示了建譯資料資料夾與建立參考這些資料夾的資料集版本的推薦方法:

![資料夾結構](./media/how-to-version-track-datasets/folder-image.png)

```Python
from azureml.core import Dataset

# get the default datastore of the workspace
datastore = workspace.get_default_datastore()

# create & register weather_ds version 1 pointing to all files in the folder of week 27
datastore_path1 = [(datastore, 'Weather/week 27')]
dataset1 = Dataset.File.from_files(path=datastore_path1)
dataset1.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27',
                  create_new_version = True)

# create & register weather_ds version 2 pointing to all files in the folder of week 27 and 28
datastore_path2 = [(datastore, 'Weather/week 27'), (datastore, 'Weather/week 28')]
dataset2 = Dataset.File.from_files(path = datastore_path2)
dataset2.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27, 28',
                  create_new_version = True)

```

<a name="pipeline"></a>

## <a name="version-a-pipeline-output-dataset"></a>版本導管輸出資料集

可以將數據集用作每個機器學習管道步驟的輸入和輸出。 重新運行管道時,每個管道步驟的輸出將註冊為新數據集版本。

由於每次重新運行管道時,機器學習管道都會將每個步驟的輸出填充到新資料夾中,因此可重現版本化的輸出數據集。 瞭解有關[導管中的資料集的詳細資訊](how-to-create-your-first-pipeline.md#steps)。

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData
from azureml.core. runconfig import CondaDependencies, RunConfiguration

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

conda = CondaDependencies.create(
    pip_packages=['azureml-defaults', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)

run_config = RunConfiguration()
run_config.environment.docker.enabled = True
run_config.environment.python.conda_dependencies = conda

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             runconfig=run_config,
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-datasets-in-experiments"></a>追蹤實驗中的資料集

對於每個機器學習實驗,您可以輕鬆地通過實驗`Run`物件跟蹤用作輸入的數據集。

以下代碼使用[`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--)方法 追蹤在實驗執行中使用了哪些輸入資料集:

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

您還可以使用`input_datasets`從 實驗中https://ml.azure.com/尋找 。 

下圖顯示了在 Azure 機器學習工作室上查找實驗的輸入數據集的位置。 在此範例中,轉到 **「實驗」** 窗格並**打開屬性選項**卡,用於實驗的特定執行。 `keras-mnist`

![輸入資料集](./media/how-to-version-track-datasets/input-datasets.png)

使用以下代碼將模型與資料集註冊:

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

註冊後,您可以使用 Python 或https://ml.azure.com/轉到 查看在數據集中註冊的模型清單。

以下檢視來自 **「資產**」下的**數據集**窗格。 選擇資料集,然後為在數據集中註冊的模型清單選擇 **「模型**」選項卡。 

![輸入資料集模型](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>後續步驟

* [使用資料集定型](how-to-train-with-datasets.md)
* [更多範例資料集筆記本](https://aka.ms/dataset-tutorial)
