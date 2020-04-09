---
title: 從 ML 導管的輸入與輸出資料
titleSuffix: Azure Machine Learning
description: 在 Azure 機器學習管中準備、使用和產生資料
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.openlocfilehash: 3dd1a82bf7fad1f201f5c0f52af944ef44a3fdf9
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879761"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>將資料移至 ML 導管步驟 (Python) 中與之間

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

數據是機器學習管道的核心。 本文提供了用於在 Azure 機器學習管道中的步驟之間導入、轉換和行動資料的代碼。 有關資料在 Azure 機器學習中工作原理的概述,請參閱[Azure 儲存服務中的存取資料](how-to-access-data.md)。 有關 Azure 機器學習管道的優點和結構,請參閱什麼是[Azure 機器學習管道?](concept-ml-pipelines.md)

此文將示範如何：

- 對`Dataset`預先存在的資料使用物件
- 存取步驟中的資料
- 將數據`Dataset`拆分為子集,如訓練和驗證子集
- 建立`PipelineData`物件以將資料傳輸到下一個導管步驟
- 使用`PipelineData`物件為導管步驟的輸入
- 從`PipelineData``Dataset`希望保留建立新物件

## <a name="prerequisites"></a>Prerequisites

您需要：

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 試著[Azure 機器學習的免費或付費版本](https://aka.ms/AMLFree)。

- 用於 Python 的[Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)或造訪 Azure[機器學習工作室](https://ml.azure.com/)。

- Azure Machine Learning 工作區。
  
  [創建 Azure 機器學習工作區](how-to-manage-workspace.md)或透過 Python SDK 使用現有工作區。 導入`Workspace``Datastore`和 類,並使用`config.json``from_config()`函數 從檔載入訂閱資訊。 預設情況下,此函數查找目前目錄中的 JSON 檔,但您也可以指定路徑參數以`from_config(path="your/file/path")`使用指向該檔。

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- 一些預先存在的數據。 本文簡要顯示了 Azure blob[容器](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)的使用。

- 可選:現有的機器學習管道,如[使用 Azure 機器學習 SDK 創建和運行機器學習管道](how-to-create-your-first-pipeline.md)中所述管道。

## <a name="use-dataset-objects-for-pre-existing-data"></a>對`Dataset`預先存在的資料使用物件 

將數據引入管道的首選方法是使用[數據集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)物件。 `Dataset`物件表示整個工作區中可用的持久數據。

創建和註冊`Dataset`物件的方法有很多種。 表格資料集用於一個或多個檔案中可用的分隔數據。 檔資料集用於二進位資料(如影像)或要分析的資料。 建立`Dataset`物件的最簡單的程式設計方法是在工作區儲存或公共 URL 中使用現有 blob:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

有關建立具有不同選項和不同來源的資料集的更多選項、在 Azure 機器學習 UI 中註冊資料集並查看它們、瞭解資料大小如何與計算容量交互以及對其進行版本控制,請參閱[建立 Azure 機器學習資料集](how-to-create-register-datasets.md)。 

### <a name="pass-datasets-to-your-script"></a>將資料集傳遞到文稿

要將資料集的路徑傳遞給腳稿,`Dataset`請使用`as_named_input()`物件的方法。 可以將生成的`DatasetConsumptionConfig`物件作為參數傳遞給腳本,也可以通過`inputs`使用 參數將結果物件傳遞到管道腳本,`Run.get_context().input_datasets[]`也可以使用 檢索數據集。

建立命名輸入後,可以選擇其存取模式`as_mount()`:`as_download()`或 。 如果文本處理數據集中的所有檔,而計算資源上的磁碟足夠大,以便數據集使用,則下載訪問模式是更好的選擇。 下載訪問模式將避免在運行時流式傳輸數據的開銷。 如果腳本訪問數據集的子集,或者該子集對於計算來說太大,請使用裝載訪問模式。 有關詳細資訊,請閱讀["安裝與下載"](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

要將數據集傳遞到管道步驟,

1. 使用`TabularDataset.as_named_inputs()``FileDataset.as_named_input()`或 (末尾無' s'`DatasetConsumptionConfig`) 建立 物件
1. 使用`as_mount()``as_download()`或設定存取模式
1. 使用`arguments``inputs`或 參數將資料集傳遞到導管步驟

以下代碼片段顯示在`PythonScriptStep`建構函數中組合這些步驟的常見模式: 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_inputs('iris').as_mount()]
)
```

您還可以使用 與`random_split()``take_sample()`等方法建立多個輸入或減少傳遞到管道步驟的資料量:

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_inputs('train').as_download(), test.as_named_inputs('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>存取文稿中的資料集

管道步驟腳本的命名輸入作為`Run`物件中的字典提供。 使用`Run``Run.get_context()`檢索活動物件,然後`input_datasets`使用檢索命名輸入的字典。 如果使用`DatasetConsumptionConfig``arguments`參數而不是參數傳遞物件`inputs`,`ArgParser`請使用程式碼存取資料。 這兩種技術都體現在以下代碼段中。

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_inputs('train').as_download()]
    inputs=[test.as_named_inputs('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

傳遞的值將是數據集檔的路徑。

也可以直接存取已註冊`Dataset`的 。 由於已註冊的數據集是永久性的,並且跨工作區共用,因此可以直接檢索它們:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-pipelinedata-for-intermediate-data"></a>用於`PipelineData`中間資料

當`Dataset`物件表示持久性數據時,[管道數據](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)物件用於從管道步驟輸出的臨時數據。 由於`PipelineData`物件的生命週期比單個管道步驟長,因此在管道定義腳本中定義它們。 創建`PipelineData`物件時,必須提供數據將駐留在其中的名稱和數據存儲。 `PipelineData``arguments``outputs`使用 與 參數會傳送給物件_both_`PythonScriptStep`:

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

您可以選擇使用提供即時上載的存`PipelineData`取模式創建物件。 在這種情況下,當您建立的時`PipelineData`,`upload_mode`將`"upload"`設定`output_path_on_compute`與參數指定要將資料寫入資料的路徑:

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>以進行`PipelineData`訓練步驟的輸出

在管道中`PythonScriptStep`,可以使用程式的參數檢索可用的輸出路徑。 如果此步驟是第一步,並且將初始化輸出數據,則必須在指定的路徑上創建目錄。 然後,您可以編寫您希望包含在的任何`PipelineData`檔。

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

`PipelineData`如果建立`is_directory`參數設定`True`為 ,則`os.makedirs()`只需執行 呼叫就足夠了,然後您可以自由地將任何您希望寫入路徑的檔寫入。 有關詳細資訊,請參閱[管道數據](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)參考文檔。

### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>讀取`PipelineData`為非初始步驟的輸入

初始導管步驟將一些資料寫`PipelineData`入 路徑,並成為該初始步驟的輸出後,它可用作後續步驟的輸入:

```python
step1_output_data = PipelineData("processed_data", datastore=def_blob_store, output_mode="upload")

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

輸入的值是前`PipelineData`一個輸出的路徑。 如果如前所示,第一步編寫了單個檔,則使用它可能如下所示: 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()

with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>轉換`PipelineData`物件轉換為`Dataset`s

如果要使可用`PipelineData`時間超過執行持續時間,請使用函`as_dataset()`數將其轉換為 。 `Dataset` 然後,您可以註冊`Dataset`,使其成為您的工作區中的一流公民。 由於物件`PipelineData`在每次執行導管時都將具有不同的路徑,因此`create_new_version`強烈建議 您`True``Dataset``PipelineData`在註冊從 物件建立時設定為 。

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)
```

## <a name="next-steps"></a>後續步驟

* [建立 Azure 機器學習資料集](how-to-create-register-datasets.md)
* [使用 Azure 機器學習 SDK 建立與執行機器學習管道](how-to-create-your-first-pipeline.md)
