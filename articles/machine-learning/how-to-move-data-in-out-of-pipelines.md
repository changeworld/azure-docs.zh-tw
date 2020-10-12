---
title: 在 ML 管線中移動資料
titleSuffix: Azure Machine Learning
description: 瞭解 Azure Machine Learning 管線中資料的輸入 & 輸出。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 08/20/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: a1bd93931f8a94f598952b28fc3db23d33e5783f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91329766"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>將資料匯入 ML 管線步驟並在其中來回移動 (Python)



本文提供在 Azure Machine Learning 管線中的步驟之間匯入、轉換和移動資料的程式碼。 如需資料如何在 Azure Machine Learning 中運作的總覽，請參閱 [存取 Azure 儲存體服務中的資料](how-to-access-data.md)。 如需 Azure Machine Learning 管線的優點和結構，請參閱 [什麼是 Azure Machine Learning 管線？](concept-ml-pipelines.md)。

此文將示範如何：

- `Dataset`針對預先存在的資料使用物件
- 存取步驟中的資料
- 將 `Dataset` 資料分割成子集，例如定型和驗證子集
- 建立 `PipelineData` 物件以將資料傳送至下一個管線步驟
- 使用 `PipelineData` 物件作為管線步驟的輸入
- 建立 `Dataset` `PipelineData` 您想要保存的新物件

> [!TIP]
> 提供在管線步驟之間傳遞暫存資料，以及在管線執行後保存資料的改良體驗，可在公開預覽類別  [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) 和中使用 [`OutputTabularDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.output_dataset_config.outputtabulardatasetconfig?view=azure-ml-py&preserve-view=true) 。  這些類別是 [實驗](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py&preserve-view=true#&preserve-view=truestable-vs-experimental) 性預覽功能，而且可能隨時變更。


## <a name="prerequisites"></a>Prerequisites

您需要：

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

- [適用於 Python 的 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)，或 [Azure Machine Learning Studio](https://ml.azure.com/) 的存取權。

- Azure Machine Learning 工作區。
  
  [建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)，或透過 Python SDK 使用現有的工作區。 匯入 `Workspace` 和 `Datastore` 類別，並使用 `from_config()` 函式從檔案 `config.json` 載入您的訂用帳戶資訊。 依預設，此函式會在目前的目錄中尋找 JSON 檔案，但您也可以使用指定路徑參數來指向檔案 `from_config(path="your/file/path")` 。

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- 某些預先存在的資料。 本文簡要說明如何使用 [Azure blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)。

- 選擇性：現有的機器學習管線，例如 [使用 AZURE MACHINE LEARNING SDK 來建立及執行機器學習管線](how-to-create-your-first-pipeline.md)中所述的管線。

## <a name="use-dataset-objects-for-pre-existing-data"></a>`Dataset`針對預先存在的資料使用物件 

將資料內嵌至管線的慣用方式是使用 [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py&preserve-view=true) 物件。 `Dataset` 物件代表可在整個工作區中使用的持續性資料。

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

一旦您建立了命名的輸入，就可以選擇其存取模式： `as_mount()` 或 `as_download()` 。 如果您的腳本會處理資料集中的所有檔案，且計算資源上的磁片夠大，可供資料集使用，則下載存取模式是較佳的選擇。 下載存取模式可避免在執行時間串流資料的額外負荷。 如果您的腳本存取資料集的子集，或它對您的計算而言太大，請使用掛接存取模式。 如需詳細資訊，請參閱 [掛接與下載](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

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

## <a name="use-pipelinedata-for-intermediate-data"></a>用於 `PipelineData` 中繼資料

雖然 `Dataset` 物件代表持續性資料，但 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true) 物件是用於從管線步驟輸出的暫存資料。 因為物件的存留期 `PipelineData` 超過單一管線步驟，所以您可以在管線定義腳本中定義它們。 當您建立 `PipelineData` 物件時，您必須提供資料將存放的名稱和資料存放區。 `PipelineData` `PythonScriptStep` 使用_both_ `arguments` 和引數，將您的物件 (s) 傳遞給 `outputs` ：

```python

default_datastore = workspace.get_default_datastore()
dataprep_output = PipelineData("clean_data", datastore=default_datastore)

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=["--output-path", dataprep_output]
    inputs=[Dataset.get_by_name(workspace, 'raw_data')],
    outputs=[dataprep_output]
)

```

您可以選擇 `PipelineData` 使用提供立即上傳的存取模式來建立物件。 在此情況下，當您建立時 `PipelineData` ，請將設定 `upload_mode` 為， `"upload"` 並使用 `output_path_on_compute` 引數來指定您要將資料寫入其中的路徑：

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

> [!TIP]
> 公開預覽版類別提供在管線步驟之間傳遞中繼資料的改良體驗 [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) 。 如需使用的程式碼範例 `OutputFileDatasetConfig` ，請參閱如何 [建立兩個步驟的 ML 管線](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)。


### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>用作 `PipelineData` 定型步驟的輸出
在您的管線中 `PythonScriptStep` ，您可以使用程式的引數來取得可用的輸出路徑。 如果此步驟是第一個步驟，將會初始化輸出資料，您必須在指定的路徑建立目錄。 然後您可以撰寫任何您想要包含在中的檔案 `PipelineData` 。

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()
# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

如果您已建立 `PipelineData` ，並將 `is_directory` 引數設為 `True` ，就足以只執行呼叫， `os.makedirs()` 而您可以隨意將任何想要的檔案寫入路徑。 如需詳細資訊，請參閱 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true) 參考檔。


### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>讀取 `PipelineData` 為非初始步驟的輸入

在初始管線步驟將部分資料寫入至 `PipelineData` 路徑，並將它變成該初始步驟的輸出之後，您就可以在稍後的步驟中使用它來做為輸入：

```python
step1_output_data = PipelineData("processed_data", datastore=def_blob_store, output_mode="upload")
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data],
    inputs=[],
    outputs=[step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data],
    inputs=[step1_output_data]
)
pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

輸入的值 `PipelineData` 是先前輸出的路徑。 

> [!TIP]
> 公開預覽版類別提供在管線步驟之間傳遞中繼資料的改良體驗 [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) 。 如需使用的程式碼範例 `OutputFileDatasetConfig` ，請參閱如何 [建立兩個步驟的 ML 管線](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)。

如先前所示，第一個步驟撰寫了單一檔案，使用它可能看起來像這樣： 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()
with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>將 `PipelineData` 物件轉換為 `Dataset` s

如果您想要讓提供的 `PipelineData` 時間超過執行的持續時間，請使用其函式將 `as_dataset()` 它轉換成 `Dataset` 。 您可以接著註冊 `Dataset` ，使其成為您工作區中的第一類公民。 由於 `PipelineData` 每次執行管線時，您的物件都會有不同的路徑，因此強烈建議您在 `create_new_version` `True` 註冊 `Dataset` 從物件建立的時設定為 `PipelineData` 。

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)

```
> [!TIP]
> 公開預覽類別可提供在管線執行之外保存中繼資料的改良體驗 [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) 。 如需使用的程式碼範例 `OutputFileDatasetConfig` ，請參閱如何 [建立兩個步驟的 ML 管線](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)。

## <a name="next-steps"></a>後續步驟

* [建立 Azure Machine Learning 資料集](how-to-create-register-datasets.md)
* [使用 Azure Machine Learning SDK 來建立及執行機器學習管線](how-to-create-your-first-pipeline.md)
