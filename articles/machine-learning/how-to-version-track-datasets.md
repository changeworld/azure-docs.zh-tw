---
title: 資料集版本控制
titleSuffix: Azure Machine Learning
description: 瞭解如何對資料集進行最佳版本控制，以及版本控制如何使用機器學習管道。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.custom: ''
ms.openlocfilehash: acbd2e3ba756255cbc69ae8a7b7ad62d7a1c1c5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528467"
---
# <a name="version-and-track-datasets-in-experiments"></a>在實驗中的版本和跟蹤資料集
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何對 Azure 機器學習資料集進行版本控制並跟蹤，以便重現。 資料集版本控制是一種對資料狀態進行書簽的方法，以便您可以為將來的實驗應用資料集的特定版本。

典型的版本控制方案：

* 當新資料可用於再培訓時
* 應用不同的資料準備或功能工程方法時

## <a name="prerequisites"></a>Prerequisites

在本教學課程中，您需要：

- [安裝的 Python 的 Azure 機器學習 SDK。](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) 此 SDK 包括[azureml 資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py)包。
    
- [Azure 機器學習工作區](concept-workspace.md)。 通過運行以下代碼檢索現有代碼，或[創建新工作區](how-to-manage-workspace.md)。

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- [Azure 機器學習資料集](how-to-create-register-datasets.md)。

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>註冊和檢索資料集版本

通過註冊資料集，您可以跨實驗和同事對資料集進行版本化、重用和共用。 您可以以相同名稱註冊多個資料集，並按名稱和版本號檢索特定版本。

### <a name="register-a-dataset-version"></a>註冊資料集版本

以下代碼通過將`titanic_ds``create_new_version`參數設置為`True`註冊資料集的新版本。 如果沒有在工作區中註冊的現有`titanic_ds`資料集，代碼將創建一個名稱的新資料集`titanic_ds`，並將其版本設置為 1。

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```
您還可以在 

### <a name="retrieve-a-dataset-by-name"></a>按名稱檢索資料集

預設情況下，`Dataset`類上的[get_by_name（）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--)方法返回在工作區註冊的資料集的最新版本。 

以下代碼獲取資料集的版本`titanic_ds`1。

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>版本控制最佳實踐

創建資料集版本時，*不會*使用工作區創建額外的資料副本。 由於資料集是存儲服務中資料的引用，因此您具有由存儲服務管理的單一真實來源。

>[!IMPORTANT]
> 如果資料集引用的資料被覆蓋或刪除，則調用資料集的特定版本*不會*還原更改。

從資料集載入資料時，始終載入資料集引用的當前資料內容。 如果要確保每個資料集版本都是可重現的，我們建議您不要修改資料集版本引用的資料內容。 當新資料進來時，將新資料檔案保存到單獨的資料檔案夾中，然後創建新的資料集版本以包括來自該新資料夾中的資料。

以下圖像和示例代碼顯示了構建資料檔案夾和創建引用這些資料夾的資料集版本的推薦方法：

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

## <a name="version-a-pipeline-output-dataset"></a>版本管道輸出資料集

可以將資料集用作每個機器學習管道步驟的輸入和輸出。 重新運行管道時，每個管道步驟的輸出將註冊為新資料集版本。

由於每次重新運行管道時，機器學習管道都會將每個步驟的輸出填充到新資料夾中，因此可重現版本化的輸出資料集。 瞭解有關[管道中的資料集的更多詳細資訊](how-to-create-your-first-pipeline.md#steps)。

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

## <a name="track-datasets-in-experiments"></a>跟蹤實驗中的資料集

對於每個機器學習實驗，您可以輕鬆地通過實驗`Run`物件跟蹤用作輸入的資料集。

以下代碼使用 方法[`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--)跟蹤在實驗運行中使用了哪些輸入資料集：

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

您還可以使用 從`input_datasets`實驗中查找https://ml.azure.com/。 

下圖顯示了在 Azure 機器學習工作室上查找實驗的輸入資料集的位置。 在此示例中，轉到 **"實驗"** 窗格並打開"**屬性**"選項卡，用於實驗的特定運行。 `keras-mnist`

![輸入資料集](./media/how-to-version-track-datasets/input-datasets.png)

使用以下代碼將模型與資料集註冊：

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

註冊後，您可以使用 Python 或轉到https://ml.azure.com/查看在資料集中註冊的模型清單。

以下視圖來自 **"資產**"下的**資料集**窗格。 選擇資料集，然後為在資料集中註冊的模型清單選擇 **"模型**"選項卡。 

![輸入資料集模型](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>後續步驟

* [使用資料集定型](how-to-train-with-datasets.md)
* [更多示例資料集筆記本](https://aka.ms/dataset-tutorial)
