---
title: 建立、 執行及追蹤 ML 管線
titleSuffix: Azure Machine Learning
description: 使用適用於 Python 的 Azure Machine Learning SDK 來建立及執行機器學習管線。 使用 ML 管線來建立和管理結合機器學習（ML）階段的工作流程。 這些階段包括資料準備、模型定型、模型部署，以及推斷/計分。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: 44a200f63adc0286a58c864b8ba04982ca58c76c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231884"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>使用 Azure Machine Learning SDK 建立及執行機器學習管線

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將了解如何使用 [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 來建立、發佈、執行及追蹤[機器學習管線](concept-ml-pipelines.md)。  使用**ML 管線**建立工作流程，以拼接各種 ML 階段，然後將該管線發佈到您的 Azure Machine Learning 工作區，以供稍後存取或與其他人共用。  ML 管線適用于批次評分案例，使用各種計算、重複使用步驟而不是重新執行，以及與其他人共用 ML 工作流程。

雖然您可以使用另一種稱為[Azure 管線](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml)的管線來進行 ML 工作的 CI/CD 自動化，但該類型的管線永遠不會儲存在您的工作區中。 [比較這些不同的管線](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use)。

ML 管線的每個階段（例如資料準備和模型訓練）都可以包含一或多個步驟。

您所建立的 ML 管線會顯示在 Azure Machine Learning[工作區](how-to-manage-workspace.md)的成員中。 

ML 管線會使用遠端計算目標來進行計算，以及與該管線相關聯的中繼和最終資料的儲存體。 他們可以在支援的[Azure 儲存體](https://docs.microsoft.com/azure/storage/)位置之間讀取和寫入資料。

如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

## <a name="prerequisites"></a>先決條件

* 建立 [Azure Machine Learning 工作區](how-to-manage-workspace.md)以保存您的所有管線資源。

* [設定您的開發環境](how-to-configure-environment.md)以安裝 Azure Machine Learning sdk，或使用已安裝 SDK 的[Azure Machine Learning 計算實例（預覽）](concept-compute-instance.md) 。

一開始請先附加您的工作區：

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

## <a name="set-up-machine-learning-resources"></a>設定機器學習資源

建立執行 ML 管線所需的資源：

* 設定用來存取管線步驟中所需資料的資料存放區。

* 設定`Dataset`物件，以指向位於資料存放區中或可存取的持續性資料。 針對在`PipelineData`管線步驟之間傳遞的暫存資料設定物件。 

* 設定將作為您管線步驟執行位置的[計算目標](concept-azure-machine-learning-architecture.md#compute-targets)。

### <a name="set-up-a-datastore"></a>設定資料存放區

資料存放區會儲存可供管線存取的資料。 每個工作區都有一個預設的資料存放區。 您可以註冊額外的資料存放區。 

當您建立工作區時， [Azure 檔案儲存體](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)和[Azure Blob 儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)會附加至工作區。 會註冊預設資料存放區，以連線到 Azure Blob 儲存體。 若要深入了解，請參閱[決定何時使用 Azure 檔案、Azure Blob 或 Azure 磁碟](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)。 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

請將資料檔案或目錄上傳至資料存放區，以便能夠從您的管線存取這些資料檔案或目錄。 此範例會使用 Blob 儲存體作為資料存放區：

```python
def_blob_store.upload_files(
    ["iris.csv"],
    target_path="train-dataset",
    overwrite=True)
```

管線會由一或多個步驟所組成。 步驟是在計算目標上執行的單位。 步驟可能會取用資料來源，並產生「中繼」資料。 步驟可以建立資料 (例如模型)、含有模型和相依檔案的目錄，或是暫存資料。 此資料接著便可供管線中稍後的其他步驟使用。

若要深入瞭解如何將管線連接至您的資料，請參閱[如何存取資料](how-to-access-data.md)和[如何註冊資料集](how-to-create-register-datasets.md)一文。 

### <a name="configure-data-using-dataset-and-pipelinedata-objects"></a>使用`Dataset`和`PipelineData`物件設定資料

您剛建立一個可在管線中當作某個步驟的輸出來參考的資料來源。 將資料提供給管線的慣用方式是[Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.Dataset)物件。 `Dataset`物件會指向位於或可從資料存放區或 Web URL 存取的資料。 `Dataset`類別是抽象的，因此您將會建立一個`FileDataset` （參考一或多個檔案）或從一個或多個檔案中`TabularDataset` ，以分隔資料行建立的實例。

`Dataset`物件支援版本控制、差異和摘要統計資料。 `Dataset`會延遲地進行評估（例如 Python 產生器），並藉由分割或篩選來將其子集化。 

您可以`Dataset`使用[from_file](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-)或[from_delimited_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-)等方法來建立。

```python
from azureml.core import Dataset

iris_tabular_dataset = Dataset.Tabular.from_delimited_files([(def_blob_store, 'train-dataset/iris.csv')])
```

中繼資料 (或步驟的輸出) 會由 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 物件代表。 `output_data1` 會產生為步驟的輸出，並用來作為一或多個未來步驟的輸入。 `PipelineData` 會在步驟之間導入資料相依性，並在管線中建立隱含的執行順序。 稍後建立管線步驟時，將會用到此物件。

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

如需有關使用資料集和管線資料的詳細資訊和範例程式碼，請參閱在[ML 管線步驟（Python）之間移動資料](how-to-move-data-in-out-of-pipelines.md)。

## <a name="set-up-a-compute-target"></a>設定計算目標

在 Azure Machine Learning 中，__計算__一詞 (或__計算目標__) 係指會在您機器學習管線中執行計算步驟的機器或叢集。   如需完整的計算目標清單，以及了解如何建立這些目標並將其連結至您的工作區，請參閱[用於模型定型的計算目標](how-to-set-up-training-targets.md)。  不論您是要將模型定型還是執行管線步驟，建立和/或連結計算目標的程序都相同。 在您建立並連結計算目標之後，請在您的[管線步驟](#steps)中使用 `ComputeTarget` 物件。

> [!IMPORTANT]
> 不支援從遠端作業內部對計算目標執行管理作業。 由於機器學習管線會作為遠端作業提交，因此請勿從管線內對計算目標使用管理作業。

以下是為下列項目建立並連結計算目標的範例：

* Azure Machine Learning Compute
* Azure Databricks 
* Azure Data Lake Analytics

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute

您可以建立用來執行步驟的 Azure Machine Learning Compute。

```python
from azureml.core.compute import ComputeTarget, AmlCompute

compute_name = "aml-compute"
vm_size = "STANDARD_NC6"
if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,  # STANDARD_NC6 is GPU-enabled
                                                                min_nodes=0,
                                                                max_nodes=4)
    # create the compute target
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # Can poll for a minimum number of nodes and for a specific timeout.
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current cluster status, use the 'status' property
    print(compute_target.status.serialize())
```

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks 是 Azure 雲端中的 Apache Spark 型環境。 它可與 Azure Machine Learning 管線搭配使用作為計算目標。

使用 Azure Databricks 之前，請先建立其工作區。 若要建立工作區資源，請參閱在[Azure Databricks 檔上執行 Spark 作業](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)。

若要連結 Azure Databricks 作為計算目標，請提供下列資訊：

* __Databricks 計算名稱__：您想要指派給這個計算資源的名稱。
* __Databricks 工作區名稱__： Azure Databricks 工作區的名稱。
* __Databricks 存取權杖__：用來驗證 Azure Databricks 的存取權杖。 若要產生存取權杖，請參閱[驗證](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html)文件。

下列程式碼示範如何使用 Azure Machine Learning SDK 連結 Azure Databricks 做為計算目標（__Databricks 工作區必須存在於與 AML 工作區相同的訂__用帳戶中）：

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

如需更詳細的範例，請參閱 GitHub 上的[範例筆記本](https://aka.ms/pl-databricks)。

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics 是 Azure 雲端中的巨量資料分析平台。 它可與 Azure Machine Learning 管線搭配使用作為計算目標。

在使用 Azure Data Lake Analytics 之前，請先建立其帳戶。 若要建立此資源，請參閱[開始使用 Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) 文件。

若要連結 Data Lake Analytics 來作為計算目標，您必須使用 Azure Machine Learning SDK，並提供下列資訊：

* __計算名稱__：您想要指派給這個計算資源的名稱。
* __資源群組__：包含 Data Lake Analytics 帳戶的資源群組。
* __帳戶名稱__： Data Lake Analytics 帳戶名稱。

下列程式碼示範如何連結 Data Lake Analytics 來作為計算目標：

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

如需更詳細的範例，請參閱 GitHub 上的[範例筆記本](https://aka.ms/pl-adla)。

> [!TIP]
> Azure Machine Learning 管線只能使用 Data Lake Analytics 帳戶的預設資料存放區中所儲存的資料來運作。 如果您需要使用的資料是在非預設存放區中，您可以使用[`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py)來複製資料，然後再進行定型。

## <a name="construct-your-pipeline-steps"></a><a id="steps"></a>建構您的管線步驟

建立計算目標並將其連結至您的工作區之後，您便已做好定義管線步驟的準備。 透過 Azure Machine Learning SDK，有許多內建的步驟可供使用。 這些步驟中最基本的就是[PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)，它會在指定的計算目標中執行 Python 腳本：

```python
from azureml.pipeline.steps import PythonScriptStep

ds_input = my_dataset.as_named_input('input1')

trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", ds_input.as_download(), "--output", output_data1],
    inputs=[ds_input],
    outputs=[output_data1],
    compute_target=compute_target,
    source_directory=project_folder,
    allow_reuse=True
)
```

在共同作業環境中`allow_reuse`使用管線時，重複使用先前的結果（）是重要的，因為排除不必要的重新執行時間可提供靈活性。 當 script_name、輸入和步驟的參數保持不變時，重複使用是預設行為。 重複使用步驟的輸出時，不會將作業提交至計算，而是從上一次執行的結果立即提供給下一個步驟的執行。 如果`allow_reuse`設定為 false，則會在管線執行期間，針對此步驟一律會產生新的執行。 

定義步驟之後，您必須使用這些步驟中的部分或全部步驟來建置管線。

> [!NOTE]
> 當您定義步驟或建立管線時，不會將任何檔案或資料上傳到 Azure Machine Learning。

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

下列範例會使用稍早建立的 Azure Databricks 計算目標： 

```python
from azureml.pipeline.steps import DatabricksStep

dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    compute_target=databricks_compute,
    allow_reuse=False
)
# List of steps to run
steps = [dbStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=steps)
```

### <a name="use-a-dataset"></a>使用資料集 

從 Azure Blob 儲存體、Azure 檔案儲存體、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL Database 和適用於 PostgreSQL 的 Azure 資料庫建立的資料集可用來做為任何管線步驟的輸入。 您可以將輸出寫入[DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py)、 [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)，或如果您想要將資料寫入特定 datasore 使用[PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)。 

> [!IMPORTANT]
> 只有針對 Azure Blob 和 Azure 檔案共用資料存放區，才支援使用 PipelineData 將輸出資料寫回資料存放區。 目前[ADLS Gen 2 資料存放區](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py)不支援這種功能。

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

接著，您可以使用[input_datasets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#input-datasets)字典來抓取管線中的資料集。

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

這行`Run.get_context()`值得反白顯示。 此函式會`Run`抓取，代表目前的實驗性執行。 在上述範例中，我們使用它來取出已註冊的資料集。 `Run`物件的另一個常見用法是同時取得實驗本身和實驗所在的工作區： 

```python
# Within a PythonScriptStep

ws = Run.get_context().experiment.workspace
```

如需更多詳細資訊，包括傳遞和存取資料的替代方式，請參閱[在 ML 管線步驟（Python）之間移動資料](how-to-move-data-in-out-of-pipelines.md)。

## <a name="submit-the-pipeline"></a>提交管線

當您提交管線時，Azure Machine Learning 會檢查每個步驟的相依性，並上傳您指定之來原始目錄的快照集。 如果未指定來源目錄，則會上傳目前的本機目錄。 快照集也會儲存為您工作區中實驗的一部分。

> [!IMPORTANT]
> 若要防止檔案包含在快照中，請在目錄[.gitignore](https://git-scm.com/docs/gitignore)中建立 .gitignore `.amlignore`或檔案，並在其中新增檔案。 `.amlignore`檔案會使用與[.gitignore](https://git-scm.com/docs/gitignore)檔案相同的語法和模式。 如果這兩個檔案都`.amlignore`存在，則會優先使用該檔案。
>
> 如需詳細資訊，請參閱[快照集](concept-azure-machine-learning-architecture.md#snapshots)。

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

當您第一次執行管線時，Azure Machine Learning 會：

* 從與工作區相關的 Blob 儲存體，將專案快照集下載到計算目標。
* 建置與管線中的每個步驟相對應的 Docker 映像。
* 從容器登錄將每個步驟的 Docker 映射下載到計算目標。
* 設定`Dataset`和`PipelineData`物件的存取權。 以作為`as_mount()`存取模式時，會使用熔斷器來提供虛擬存取。 如果不支援裝載，或使用者指定為`as_download()`的存取權，則會改為將資料複製到計算目標。
* 在步驟定義中指定的計算目標內執行步驟。 
* 建立步驟所指定的成品，例如記錄、stdout 和 stderr、計量及輸出。 這些成品會接著上傳並保留在使用者的預設資料存放區中。

![以管線的形式執行實驗的圖表](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

如需詳細資訊，請參閱[實驗類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)參考。

### <a name="view-results-of-a-pipeline"></a>查看管線的結果

在 studio 中查看所有管線及其執行詳細資料的清單：

1. 登入 [Azure Machine Learning Studio](https://ml.azure.com)。

1. [查看您的工作區](how-to-manage-workspace.md#view)。

1. 在左側選取 [**管線**]，以查看您所有的管線執行。
 ![機器學習管線清單](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. 選取特定管線以查看執行結果。

## <a name="git-tracking-and-integration"></a>Git 追蹤與整合

當您啟動定型回合，其中來原始目錄是本機 Git 存放庫時，儲存機制的相關資訊會儲存在執行歷程記錄中。 如需詳細資訊，請參閱[Azure Machine Learning 的 Git 整合](concept-train-model-git-integration.md)。

## <a name="publish-a-pipeline"></a>發佈管線

您可以發佈管線以稍後使用不同的輸入來執行它。 若要讓已發佈之管線的 REST 端點接受參數，您必須在發佈管線前將其參數化。

1. 若要建立管線參數，請使用 [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) 物件搭配預設值。

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. 將這個 `PipelineParameter` 物件以參數形式新增至管線中的任何步驟，如下所示：

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. 發佈這個將在被叫用時接受參數的管線。

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

### <a name="run-a-published-pipeline"></a>執行已發佈的管線

所有已發佈的管線都有 REST 端點。 此端點可從外部系統 (例如非 Python 用戶端) 叫用管線執行。 此端點可在批次評分和重新訓練案例中，提供「受控的可重複性」。

若要叫用上述管線的執行，您需要 Azure Active Directory authentication 標頭 token （如[AzureCliAuthentication 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py)參考中所述），或在[Azure Machine Learning 筆記本中的驗證](https://aka.ms/pl-restep-auth)中取得更多詳細資料。

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>建立已設定版本的管線端點

您可以建立管線端點，其中包含多個已發佈的管線。 這可以像已發佈的管線一樣使用，但會在您逐一查看並更新 ML 管線時提供固定的 REST 端點。

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PublishedPipeline.get(workspace="ws", name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

### <a name="submit-a-job-to-a-pipeline-endpoint"></a>將作業提交至管線端點

您可以將作業提交至管線端點的預設版本：

```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```

您也可以將作業提交至特定版本：

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

您可以使用 REST API 來完成相同動作：

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

### <a name="use-published-pipelines-in-the-studio"></a>在 studio 中使用已發佈的管線

您也可以從 studio 執行已發佈的管線：

1. 登入 [Azure Machine Learning Studio](https://ml.azure.com)。

1. [查看您的工作區](how-to-manage-workspace.md#view)。

1. 在左側選取 [**端點**]。

1. 在頂端，選取 [**管線端點**]。
 ![機器學習服務發佈管線的清單](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. 選取特定管線來執行、取用或檢查管線端點先前執行的結果。

### <a name="disable-a-published-pipeline"></a>停用已發行的管線

若要從已發佈的管線清單中隱藏管線，您可以在 studio 或 SDK 中停用它：

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

您可以使用`p.enable()`再次啟用它。 如需詳細資訊，請參閱[PublishedPipeline 類別](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py)參考。

## <a name="caching--reuse"></a>快取 & 重複使用  

為了優化和自訂管線的行為，您可以在快取和重複使用方面執行一些動作。 例如，您可以選擇：
+ 在[步驟定義](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)期間設定`allow_reuse=False` ，以關閉**步驟執行輸出的預設重複使用**。 在共同作業環境中使用管線時，重複使用是關鍵，因為排除不必要的執行可提供靈活性。 不過，您可以選擇不重複使用。
+ **針對執行中的所有步驟強制輸出**重新產生`pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

根據預設， `allow_reuse`會啟用步驟，並哈`source_directory`希步驟定義中指定的。 因此，如果指定步驟的腳本維持不變（`script_name`、輸入和參數），而中` source_directory`沒有任何其他變更，則會重複使用上一個步驟執行的輸出，而不會將作業提交至計算，而先前執行的結果會立即可供下一個步驟使用。

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

## <a name="next-steps"></a>後續步驟

- 使用 [GitHub 上的這些 Jupyter Notebook](https://aka.ms/aml-pipeline-readme) 來進一步探索機器學習管線。
- 請參閱[azureml-管線核心](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py)套件和[azureml-管線-步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)套件的 SDK 參考說明。
- 如需有關偵錯工具和疑難排解管線的秘訣，請參閱操作[說明](how-to-debug-pipelines.md)。

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]
