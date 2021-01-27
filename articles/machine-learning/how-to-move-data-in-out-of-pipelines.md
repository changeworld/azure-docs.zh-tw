---
title: 在 ML 管線中移動資料
titleSuffix: Azure Machine Learning
description: 瞭解 Azure Machine Learning 管線如何內嵌資料，以及如何在管線步驟之間管理和移動資料。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 01/11/2021
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, devx-track-python, data4ml
ms.openlocfilehash: 80a995b488f335ac2eb60ae18621acb2b1df58e2
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98871531"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>將資料匯入 ML 管線步驟並在其中來回移動 (Python)



本文提供在 Azure Machine Learning 管線中的步驟之間匯入、轉換和移動資料的程式碼。 如需資料如何在 Azure Machine Learning 中運作的總覽，請參閱 [存取 Azure 儲存體服務中的資料](how-to-access-data.md)。 如需 Azure Machine Learning 管線的優點和結構，請參閱 [什麼是 Azure Machine Learning 管線？](concept-ml-pipelines.md)。

此文將示範如何：

- `Dataset`針對預先存在的資料使用物件
- 存取步驟中的資料
- 將 `Dataset` 資料分割成子集，例如定型和驗證子集
- 建立 `OutputFileDatasetConfig` 物件以將資料傳送至下一個管線步驟
- 使用 `OutputFileDatasetConfig` 物件作為管線步驟的輸入
- 建立 `Dataset` `OutputFileDatasetConfig` 您想要保存的新物件

## <a name="prerequisites"></a>Prerequisites

您需要：

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

- [適用於 Python 的 Azure Machine Learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)，或 [Azure Machine Learning Studio](https://ml.azure.com/) 的存取權。

- Azure Machine Learning 工作區。
  
  [建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)，或透過 Python SDK 使用現有的工作區。 匯入 `Workspace` 和 `Datastore` 類別，並使用 `from_config()` 函式從檔案 `config.json` 載入您的訂用帳戶資訊。 依預設，此函式會在目前的目錄中尋找 JSON 檔案，但您也可以使用指定路徑參數來指向檔案 `from_config(path="your/file/path")` 。

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- 某些預先存在的資料。 本文簡要說明如何使用 [Azure blob 容器](../storage/blobs/storage-blobs-overview.md)。

- 選擇性：現有的機器學習管線，例如 [使用 AZURE MACHINE LEARNING SDK 來建立及執行機器學習管線](./how-to-create-machine-learning-pipelines.md)中所述的管線。

## <a name="use-dataset-objects-for-pre-existing-data"></a>`Dataset`針對預先存在的資料使用物件 

將資料內嵌至管線的慣用方式是使用 [Dataset](/python/api/azureml-core/azureml.core.dataset%28class%29?preserve-view=true&view=azure-ml-py) 物件。 `Dataset` 物件代表可在整個工作區中使用的持續性資料。

有許多方式可以建立和註冊 `Dataset` 物件。 表格式資料集適用于一個或多個檔案中的分隔資料。 檔案資料集適用于二進位資料 (例如) 的影像，或您將剖析的資料。 建立物件的最簡單程式設計方式， `Dataset` 就是使用工作區儲存體或公用 url 中的現有 blob：

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

如需有關使用不同的選項和不同來源來建立資料集、在 Azure Machine Learning UI 中進行註冊和檢查的詳細資訊，請瞭解資料大小如何與計算容量互動，以及如何進行版本控制，請參閱 [建立 Azure Machine Learning 資料集](how-to-create-register-datasets.md)。 

### <a name="pass-datasets-to-your-script"></a>將資料集傳遞給您的腳本

若要將資料集的路徑傳遞至您的腳本，請使用 `Dataset` 物件的 `as_named_input()` 方法。 您可以使用 `DatasetConsumptionConfig` 管線腳本的引數，將產生的物件以引數形式傳遞至腳本，或使用 `inputs` 管線腳本的引數取得資料集 `Run.get_context().input_datasets[]` 。

一旦您建立了命名的輸入，就可以選擇其存取模式： `as_mount()` 或 `as_download()` 。 如果您的腳本會處理資料集中的所有檔案，且計算資源上的磁片夠大，可供資料集使用，則下載存取模式是較佳的選擇。 下載存取模式可避免在執行時間串流資料的額外負荷。 如果您的腳本存取資料集的子集，或它對您的計算而言太大，請使用掛接存取模式。 如需詳細資訊，請參閱 [掛接與下載](./how-to-train-with-datasets.md#mount-vs-download)

若要將資料集傳遞至您的管線步驟：

1. 使用 `TabularDataset.as_named_input()` 或 `FileDataset.as_named_input()` (no ' at end) 來建立 `DatasetConsumptionConfig` 物件
1. 使用 `as_mount()` 或 `as_download()` 來設定存取模式
1. 使用 `arguments` 或引數將資料集傳遞給您的管線 `inputs` 步驟

下列程式碼片段顯示在函式中合併這些步驟的常見模式 `PythonScriptStep` ： 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_input('iris').as_mount()]
)
```

> [!NOTE]
> 您必須將所有這些引數的值取代 (也就是、 `"train_data"` 、 `"train.py"` 、 `cluster` 和 `iris_dataset`) 與您自己的資料。 上述程式碼片段只會顯示呼叫的格式，而不是 Microsoft 範例的一部分。 

您也可以使用方法（例如 `random_split()` 和） `take_sample()` 來建立多個輸入，或減少傳遞給管線步驟的資料量：

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_input('train').as_download(), test.as_named_input('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>存取腳本內的資料集

您的管線步驟腳本的命名輸入可作為物件內的字典 `Run` 。 使用取出使用中的 `Run` 物件 `Run.get_context()` ，然後使用取得已命名輸入的字典 `input_datasets` 。 如果您 `DatasetConsumptionConfig` 使用 `arguments` 引數而非引數傳遞物件 `inputs` ，請使用程式碼存取資料 `ArgParser` 。 下列程式碼片段會示範這兩種技術。

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_input('train').as_download()]
    inputs=[test.as_named_input('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

傳遞的值將會是資料集檔案的路徑 (s) 。

您也可以直接存取已註冊的 `Dataset` 。 由於註冊的資料集會持續存在並在工作區中共用，因此您可以直接取出這些資料集：

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

> [!NOTE]
> 上述程式碼片段顯示呼叫的格式，而不是 Microsoft 範例的一部分。 您必須以您自己的專案中的值取代各種引數。

## <a name="use-outputfiledatasetconfig-for-intermediate-data"></a>用於 `OutputFileDatasetConfig` 中繼資料

雖然 `Dataset` 物件只代表持續性資料，但 [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) 物件 (s) 可用於管線步驟 **和** 持續性輸出資料的暫存資料輸出。 `OutputFileDatasetConfig` 支援將資料寫入 blob 儲存體、檔案共用、adlsgen1 或 adlsgen2。 它支援掛接模式和上傳模式。 在掛接模式中，寫入至掛接目錄的檔案會在檔案關閉時永久儲存。 在上傳模式中，寫入至輸出目錄的檔案會在作業結束時上傳。 如果作業失敗或已取消，將不會上傳輸出目錄。

 `OutputFileDatasetConfig` 物件的預設行為是寫入工作區的預設資料存放區。 `OutputFileDatasetConfig`使用參數將物件傳遞給 `PythonScriptStep` `arguments` 。

```python
from azureml.data import OutputFileDatasetConfig
dataprep_output = OutputFileDatasetConfig()
input_dataset = Dataset.get_by_name(workspace, 'raw_data')

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=[input_dataset.as_named_input('raw_data').as_mount(), dataprep_output]
    )
```

您可以選擇 `OutputFileDatasetConfig` 在執行結束時上傳物件的內容。 在此情況下，請使用函式 `as_upload()` 搭配您的 `OutputFileDatasetConfig` 物件，並指定是否要覆寫目的地中的現有檔案。 

```python
#get blob datastore already registered with the workspace
blob_store= ws.datastores['my_blob_store']
OutputFileDatasetConfig(name="clean_data", destination=blob_store).as_upload(overwrite=False)
```

> [!NOTE]
> 對的並行寫入 `OutputFileDatasetConfig` 將會失敗。 請勿嘗試同時使用單一 `OutputFileDatasetConfig` 。 請勿在多重處理的 `OutputFileDatasetConfig` 情況下（例如使用分散式訓練時）共用單一。 

### <a name="use-outputfiledatasetconfig-as-outputs-of-a-training-step"></a>用作 `OutputFileDatasetConfig` 定型步驟的輸出

在管線的 `PythonScriptStep` 中，您可以使用程式的引數來擷取可用的輸出路徑。 如果這是第一個步驟，而且這個步驟將初始化輸出資料，則必須在指定的路徑中建立目錄。 然後您可以撰寫任何您想要包含在中的檔案 `OutputFileDatasetConfig` 。

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

### <a name="read-outputfiledatasetconfig-as-inputs-to-non-initial-steps"></a>讀取 `OutputFileDatasetConfig` 為非初始步驟的輸入

在初始管線步驟將部分資料寫入至 `OutputFileDatasetConfig` 路徑，並將它變成該初始步驟的輸出後，就可以用來做為稍後步驟的輸入。 

在下列程式碼中， 

* `step1_output_data` 表示 PythonScriptStep 的輸出 `step1` 會寫入至 ADLS Gen 2 資料存放區（ `my_adlsgen2` 在上傳存取模式中）。 深入瞭解如何 [設定角色許可權](how-to-access-data.md#azure-data-lake-storage-generation-2) ，以便將資料寫回 ADLS Gen 2 資料存放區。 

* `step1`完成並將輸出寫入至指定的目的地之後，就 `step1_output_data` 可以使用步驟2做 `step1_output_data` 為輸入。 

```python
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']
step1_output_data = OutputFileDatasetConfig(name="processed_data", destination=datastore).as_upload()

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data.as_input]

)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

## <a name="register-outputfiledatasetconfig-objects-for-reuse"></a>註冊 `OutputFileDatasetConfig` 要重複使用的物件

如果您想要讓您的 `OutputFileDatasetConfig` 可用時間超過實驗的持續時間，請將其註冊至您的工作區，以在實驗之間共用及重複使用。

```python
step1_output_ds = step1_output_data.register_on_complete(name='processed_data', 
                                                         description = 'files from step1`)
```


## <a name="next-steps"></a>後續步驟

* [建立 Azure Machine Learning 資料集](how-to-create-register-datasets.md)
* [使用 Azure Machine Learning SDK 來建立及執行機器學習管線](./how-to-create-machine-learning-pipelines.md)