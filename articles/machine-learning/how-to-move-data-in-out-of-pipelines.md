---
title: 來自 ML 管線的輸入和輸出資料
titleSuffix: Azure Machine Learning
description: 準備、使用和產生 Azure Machine Learning 管線中的資料
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.openlocfilehash: 3dd1a82bf7fad1f201f5c0f52af944ef44a3fdf9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80879761"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>將資料匯入 ML 管線步驟並在其中來回移動 (Python)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

資料是機器學習管線的核心。 本文提供在 Azure Machine Learning 管線中的步驟之間匯入、轉換和移動資料的程式碼。 如需資料在 Azure Machine Learning 中運作方式的總覽，請參閱[存取 Azure 儲存體服務中的資料](how-to-access-data.md)。 如需 Azure Machine Learning 管線的優點和結構，請參閱[什麼是 Azure Machine Learning 管線？](concept-ml-pipelines.md)。

此文將示範如何：

- 針對`Dataset`預先存在的資料使用物件
- 在您的步驟中存取資料
- 將`Dataset`資料分割成子集，例如定型和驗證子集
- 建立`PipelineData`物件以將資料傳輸至下一個管線步驟
- 使用`PipelineData`物件做為管線步驟的輸入
- 建立您`Dataset`想要`PipelineData`保存的新物件

## <a name="prerequisites"></a>Prerequisites

您需要：

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

- [適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)，或[Azure Machine Learning studio](https://ml.azure.com/)的存取權。

- Azure Machine Learning 工作區。
  
  請[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)，或透過 Python SDK 使用現有的工作區。 匯入`Workspace`和`Datastore`類別，並`config.json`使用函式從檔案載入您的訂用`from_config()`帳戶資訊。 此函式預設會在目前的目錄中尋找 JSON 檔案，但您也可以使用`from_config(path="your/file/path")`來指定 path 參數，以指向該檔案。

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- 一些預先存在的資料。 本文簡要說明[Azure blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)的使用。

- 選擇性：現有的機器學習管線，例如[使用 AZURE MACHINE LEARNING SDK 建立及執行機器學習管線](how-to-create-your-first-pipeline.md)中所述。

## <a name="use-dataset-objects-for-pre-existing-data"></a>針對`Dataset`預先存在的資料使用物件 

將資料內嵌至管線的慣用方法是使用[Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)物件。 `Dataset`物件代表整個工作區中可用的持續性資料。

建立和註冊`Dataset`物件的方法有很多種。 表格式資料集適用于一個或多個檔案中可用的分隔資料。 檔案資料集適用于二進位資料（例如影像）或您將剖析的資料。 建立`Dataset`物件最簡單的程式設計方式，是在工作區儲存體或公用 url 中使用現有的 blob：

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

如需使用不同的選項和不同來源來建立資料集的更多選項，請在 Azure Machine Learning UI 中進行註冊並加以檢查、瞭解資料大小與計算容量的互動方式，以及如何進行版本控制，請參閱[建立 Azure Machine Learning 資料集](how-to-create-register-datasets.md)。 

### <a name="pass-datasets-to-your-script"></a>將資料集傳遞至您的腳本

若要將資料集的路徑傳遞給您的腳本`Dataset` ，請`as_named_input()`使用物件的方法。 您可以將產生`DatasetConsumptionConfig`的物件當做引數傳遞至您的腳本，或藉由`inputs`使用管線腳本的引數，您可以使用`Run.get_context().input_datasets[]`來取出資料集。

建立命名的輸入之後，您可以選擇其存取模式： `as_mount()`或。 `as_download()` 如果您的腳本會處理您的資料集內的所有檔案，且計算資源上的磁片夠大，可供資料集使用，下載存取模式是較好的選擇。 下載存取模式可避免在執行時間串流資料的額外負荷。 如果您的腳本存取資料集的子集，或它對您的計算而言太大，請使用掛接存取模式。 如需詳細資訊，請參閱[掛接與下載](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

若要將資料集傳遞至管線步驟：

1. 使用`TabularDataset.as_named_inputs()`或`FileDataset.as_named_input()` （不是 ' s '）來建立`DatasetConsumptionConfig`物件
1. 使用`as_mount()`或`as_download()`設定存取模式
1. 使用`arguments`或`inputs`引數將資料集傳遞至您的管線步驟

下列程式碼片段顯示在此`PythonScriptStep`函式中結合這些步驟的常見模式： 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_inputs('iris').as_mount()]
)
```

您也可以使用`random_split()`和`take_sample()`之類的方法來建立多個輸入，或減少傳遞至管線步驟的資料量：

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

### <a name="access-datasets-within-your-script"></a>存取腳本中的資料集

您的`Run`管線步驟腳本的命名輸入可做為物件內的字典。 使用`Run` `Run.get_context()`取出作用中的物件，然後使用`input_datasets`來取出已命名輸入的字典。 如果`DatasetConsumptionConfig`您使用`arguments`引數而非`inputs`引數傳遞物件，請使用`ArgParser`程式碼來存取資料。 下列程式碼片段會示範這兩種技術。

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

傳遞的值將是資料集檔案的路徑。

您也可以直接存取已註冊`Dataset`的。 由於註冊的資料集是持續性的，而且會在工作區之間共用，因此您可以直接抓取它們：

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-pipelinedata-for-intermediate-data"></a>`PipelineData`用於中繼資料

當`Dataset`物件代表持續性資料時， [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)物件會用於從管線步驟輸出的暫存資料。 因為`PipelineData`物件的存留期超過單一管線步驟，所以您可以在管線定義腳本中定義它們。 當您建立`PipelineData`物件時，您必須提供資料所在的名稱和資料存放區。 使用和`PipelineData` `outputs`引數，將您的`PythonScriptStep` `arguments`物件傳遞給_您的：_

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

您可以選擇使用可提供`PipelineData`立即上傳的存取模式來建立物件。 在此情況下，當您建立`PipelineData`時，請`upload_mode`將`"upload"`設定為， `output_path_on_compute`並使用引數來指定您將在其中寫入資料的路徑：

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>當做`PipelineData`定型步驟的輸出使用

在您的`PythonScriptStep`管線中，您可以使用程式的引數來捕獲可用的輸出路徑。 如果這個步驟是第一個，而且將初始化輸出資料，您必須在指定的路徑建立目錄。 接著，您可以撰寫想要包含在中的`PipelineData`任何檔案。

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

如果您已建立`PipelineData` ，並`is_directory`將引數`True`設為，則只需執行`os.makedirs()`呼叫即可，然後您就可以自由地將任何您想要的檔案寫入路徑。 如需詳細資訊，請參閱[PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)參考檔。

### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>讀取`PipelineData`為非初始步驟的輸入

在初始管線步驟將一些資料寫入`PipelineData`路徑，並成為該初始步驟的輸出後，它就可以做為後續步驟的輸入：

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

`PipelineData`輸入的值是先前輸出的路徑。 如先前所示，第一個步驟撰寫了單一檔案，使用它可能看起來像這樣： 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()

with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>將`PipelineData`物件轉換`Dataset`為 s

如果您想要讓您`PipelineData`的可用時間超過執行的期間，請使用其`as_dataset()`函式將它轉換成。 `Dataset` 接著`Dataset`，您可以註冊，使其成為您工作區中的第一級公民。 由於每`PipelineData`次執行管線時，您的物件會有不同的路徑，因此強烈建議您在`create_new_version`註冊`True`從`PipelineData`物件建立`Dataset`的時，將設為。

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)
```

## <a name="next-steps"></a>後續步驟

* [建立 Azure machine learning 資料集](how-to-create-register-datasets.md)
* [使用 Azure Machine Learning SDK 建立及執行機器學習管線](how-to-create-your-first-pipeline.md)
