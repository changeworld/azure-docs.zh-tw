---
title: 建立、 執行及追蹤 ML 管線
titleSuffix: Azure Machine Learning
description: 使用適用於 Python 的 Azure Machine Learning SDK 來建立及執行機器學習管線。 使用 ML 管線來建立和管理將機器學習服務 (ML) 階段拼接在一起的工作流程。 這些階段包括資料準備、模型定型、模型部署，以及推斷/評分。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: nilsp
author: NilsPohlmann
ms.date: 8/14/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: ca1419fe95e9ca383c09c7bc33a16ce148549cb6
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2020
ms.locfileid: "88755070"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>使用 Azure Machine Learning SDK 來建立及執行機器學習管線

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將了解如何使用 [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 來建立、發佈、執行及追蹤[機器學習管線](concept-ml-pipelines.md)。  使用 **ML 管線** 來建立可將各種 ML 階段將在一起的工作流程，然後將該管線發佈到您的 Azure Machine Learning 工作區，以供稍後存取或與其他人共用。  ML 管線適用于批次評分案例，使用各種計算，重複使用步驟而不是重新執行步驟，以及與其他人共用 ML 工作流程。

雖然您可以使用不同種類的管線（稱為 [Azure 管線](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) ）來進行 ML 工作的 CI/CD 自動化，但該類型的管線不會儲存在您的工作區中。 [比較這些不同的管線](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use)。

ML 管線的每個階段（例如資料準備和模型定型）可以包含一或多個步驟。

您 Azure Machine Learning [工作區](how-to-manage-workspace.md)的成員可以看到您所建立的 ML 管線。 

ML 管線會使用遠端計算目標，來處理與該管線相關聯的計算和暫存資料。 他們可以從支援的 [Azure 儲存體](https://docs.microsoft.com/azure/storage/) 位置讀取和寫入資料。

如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

## <a name="prerequisites"></a>Prerequisites

* 建立 [Azure Machine Learning 工作區](how-to-manage-workspace.md)以保存您的所有管線資源。

* [設定您的開發環境](how-to-configure-environment.md) 以安裝 Azure Machine Learning sdk，或使用已安裝 sdk 的 [Azure Machine Learning 計算實例](concept-compute-instance.md) 。

從附加工作區開始：

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

## <a name="set-up-machine-learning-resources"></a>設定機器學習資源

建立執行 ML 管線所需的資源：

* 設定用來存取管線步驟中所需資料的資料存放區。

* 將物件設定為指向存在於資料存放區 `Dataset` 中或可在資料存放區中存取的持續性資料。 `OutputFileDatasetConfig`針對管線步驟之間傳遞的暫存資料設定物件，或為建立輸出。 
> [!NOTE]
>`OutputFileDatasetConfig`類別是實驗性預覽功能，而且可能隨時變更。
>
>如需詳細資訊，請參閱https://aka.ms/azuremlexperimental。

* 設定將作為您管線步驟執行位置的[計算目標](concept-azure-machine-learning-architecture.md#compute-targets)。

### <a name="set-up-a-datastore"></a>設定資料存放區

資料存放區會儲存可供管線存取的資料。 每個工作區都有一個預設的資料存放區。 您可以註冊額外的資料存放區。 

當您建立工作區時， [Azure 檔案儲存體](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) 和 [Azure Blob 儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) 會附加至工作區。 已註冊預設資料存放區，以連接到 Azure Blob 儲存體。 若要深入了解，請參閱[決定何時使用 Azure 檔案、Azure Blob 或 Azure 磁碟](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)。 

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

管線會由一或多個步驟所組成。 步驟是在計算目標上執行的單位。 步驟可能會使用資料來源，並產生「中繼」資料。 步驟可以建立資料 (例如模型)、含有模型和相依檔案的目錄，或是暫存資料。 此資料接著便可供管線中稍後的其他步驟使用。

若要深入瞭解如何將管線連接到您的資料，請參閱文章 [如何存取資料](how-to-access-data.md) 和 [如何註冊資料集](how-to-create-register-datasets.md)。 

### <a name="configure-data-with-dataset-and-outputfiledatasetconfig-objects"></a>使用 `Dataset` 和物件設定 `OutputFileDatasetConfig` 資料

您剛建立一個可在管線中當作某個步驟的輸出來參考的資料來源。 將資料提供給管線的慣用方式是 [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.Dataset) 物件。 `Dataset`物件會指向位於或可從資料存放區或 WEB URL 存取的資料。 此 `Dataset` 類別是抽象的，因此您將會建立一個或多個檔案的 (實例，其中包含 `FileDataset` 一個或多個檔案) 或 `TabularDataset` 從一個或多個具有分隔資料行的檔案建立的。

`Dataset` 物件支援版本控制、差異及摘要統計資料。 `Dataset`會延遲評估 (例如 Python 產生器) ，而將它們分割或篩選是很有效率的。 

您可以 `Dataset` 使用 [from_file](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) 或 [from_delimited_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-)之類的方法來建立。

```python
from azureml.core import Dataset

iris_tabular_dataset = Dataset.Tabular.from_delimited_files([(def_blob_store, 'train-dataset/iris.csv')])
```

步驟) 的中繼資料 (或輸出會以 [OutputFileDatasetConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py) 物件表示。 `output_data1` 會產生為步驟的輸出，並用來作為一或多個未來步驟的輸入。 `OutputFileDatasetConfig` 會在步驟之間導入資料相依性，並在管線中建立隱含的執行順序。 稍後建立管線步驟時，將會使用這個物件。

`OutputFileDatasetConfig` 物件會傳回目錄，依預設會將輸出寫入工作區的預設資料存放區。

```python
from azureml.data import OutputFileDatasetConfig

output_data1 = OutputFileDatasetConfig()
```

使用資料集和 OutputFileConfig 物件的詳細資訊和範例程式碼，將 [資料移入和移出 ML 管線步驟， (Python) ](how-to-move-data-in-out-of-pipelines.md)。

## <a name="set-up-a-compute-target"></a>設定計算目標

在 Azure Machine Learning 中，__計算__一詞 (或__計算目標__) 係指會在您機器學習管線中執行計算步驟的機器或叢集。 如需完整的計算目標清單，以及了解如何建立這些目標並將其連結至您的工作區，請參閱[用於模型定型的計算目標](how-to-set-up-training-targets.md)。 無論您是定型模型或執行管線步驟，建立和或附加計算目標的程式都相同。 在您建立並連結計算目標之後，請在您的[管線步驟](#steps)中使用 `ComputeTarget` 物件。

> [!IMPORTANT]
> 不支援從遠端作業內部對計算目標執行管理作業。 由於機器學習管線會作為遠端作業提交，因此請勿從管線內對計算目標使用管理作業。

以下是為下列項目建立並連結計算目標的範例：

* Azure Machine Learning Compute
* Azure Databricks 
* Azure Data Lake Analytics

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

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

使用 Azure Databricks 之前，請先建立其工作區。 若要建立工作區資源，請參閱在 [Azure Databricks 檔上執行 Spark 作業](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) 。

若要連結 Azure Databricks 作為計算目標，請提供下列資訊：

* __Databricks 計算名稱__：您要指派給這個計算資源的名稱。
* __Databricks 工作區名稱__： Azure Databricks 工作區的名稱。
* __Databricks 存取權杖__：用來向 Azure Databricks 驗證的存取權杖。 若要產生存取權杖，請參閱[驗證](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html)文件。

下列程式碼示範如何使用 Azure Machine Learning SDK 來附加 Azure Databricks 作為計算目標 (__Databricks 工作區必須存在於與 AML 工作區) 相同的訂用帳戶中__ ：

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

如需更詳細的範例，請參閱 GitHub 上的 [範例筆記本](https://aka.ms/pl-databricks) 。

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics 是 Azure 雲端中的巨量資料分析平台。 它可與 Azure Machine Learning 管線搭配使用作為計算目標。

在使用 Azure Data Lake Analytics 之前，請先建立其帳戶。 若要建立此資源，請參閱[開始使用 Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) 文件。

若要連結 Data Lake Analytics 來作為計算目標，您必須使用 Azure Machine Learning SDK，並提供下列資訊：

* __計算名稱__：您想要指派給這個計算資源的名稱。
* __資源群組__：包含 Data Lake Analytics 帳戶的資源群組。
* __帳戶名稱__： Data Lake Analytics 的帳戶名稱。

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

如需更詳細的範例，請參閱 GitHub 上的 [範例筆記本](https://aka.ms/pl-adla) 。

> [!TIP]
> Azure Machine Learning 管線只能使用 Data Lake Analytics 帳戶的預設資料存放區中所儲存的資料來運作。 如果您需要使用的資料是在非預設的存放區中，您可以使用在 [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) 定型之前複製資料。

## <a name="configure-the-training-runs-environment"></a>設定定型回合的環境

下一步是確定遠端定型執行具有訓練步驟所需的所有相依性。 您可以藉由建立和設定物件來設定相依性和執行時間內容 `RunConfiguration` 。 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import Environment 

aml_run_config = RunConfiguration()
# `compute_target` as defined in "Azure Machine Learning compute" section above
aml_run_config.target = compute_target

USE_CURATED_ENV = True
if USE_CURATED_ENV :
    curated_environment = Environment.get(workspace=ws, name="AzureML-Tutorial")
    aml_run_config.environment = curated_environment
else:
    aml_run_config.environment.python.user_managed_dependencies = False
    
    # Add some packages relied on by data prep step
    aml_run_config.environment.python.conda_dependencies = CondaDependencies.create(
        conda_packages=['pandas','scikit-learn'], 
        pip_packages=['azureml-sdk', 'azureml-dataprep[fuse,pandas]'], 
        pin_sdk_version=False)
```

上述程式碼顯示兩個處理相依性的選項。 如所示， `USE_CURATED_ENV = True` 在中，設定是以策劃環境為基礎。 策劃環境是「prebaked」，具有常見的相互相依程式庫，而且在線上時可能會大幅加快。 策劃環境已在 [Microsoft Container Registry](https://hub.docker.com/publishers/microsoftowner)中預先建立 Docker 映射。 當您變更 `USE_CURATED_ENV` 以 `False` 顯示明確設定相依性的模式時，所採用的路徑。 在該案例中，將會在資源群組內的 Azure Container Registry 中建立並註冊新的自訂 Docker 映射 (請參閱 [Azure) 中的私人 Docker 容器登錄簡介](https://docs.microsoft.com/azure/container-registry/container-registry-intro) 。 建立和註冊此映射可能需要幾分鐘的時間。

## <a name="construct-your-pipeline-steps"></a><a id="steps"></a>建構您的管線步驟

建立計算資源和環境之後，您就可以開始定義管線的步驟。 您可以透過 Azure Machine Learning SDK 使用許多內建步驟，如您在 [ `azureml.pipeline.steps` 封裝的參考檔](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py)中所見。 最具彈性的類別是 [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)，它會執行 Python 腳本。

```python
from azureml.pipeline.steps import PythonScriptStep

dataprep_source_dir = "./dataprep_src"
entry_point = "prepare.py"

# `my_dataset` as defined above
ds_input = my_dataset.as_named_input('input1')

# `output_data1`, `compute_target`, `aml_run_config` as defined above
data_prep_step = PythonScriptStep(
    script_name=entry_point,
    source_directory=dataprep_source_dir,
    arguments=["--input", ds_input.as_download(), "--output", output_data1],
    compute_target=compute_target,
    runconfig=aml_run_config,
    allow_reuse=True
)
```

上述程式碼顯示典型的初始管線步驟。 您的資料準備程式碼位於子目錄中， (在此範例中 `"prepare.py"` 的目錄 `"./dataprep.src"`) 。 在管線建立過程中，此目錄會壓縮並上傳至， `compute_target` 且步驟會執行指定為之值的腳本 `script_name` 。

這些 `arguments` 值會指定步驟的輸入和輸出。 在上述範例中，基準資料是 `my_dataset` 資料集。 對應的資料將會下載到計算資源，因為程式碼會將它指定為 `as_download()` 。 腳本 `prepare.py` 會執行任何適用于手邊工作的資料轉換工作，並將資料輸出至 `output_data1` 類型 `OutputFileDatasetConfig` 。 如需詳細資訊，請參閱將 [資料移入和移出 ML 管線步驟 (Python) ](how-to-move-data-in-out-of-pipelines.md)。 

此步驟會在使用設定的電腦上執行 `compute_target` `aml_run_config` 。 

在共同作業 `allow_reuse` 環境中使用管線時，重複使用先前的結果 () 是關鍵，因為消除不必要的重新建立可提供靈活性。 當步驟的 script_name、輸入和參數保持不變時，重複使用是預設行為。 允許重複使用時，會立即將先前執行的結果傳送至下一個步驟。 如果 `allow_reuse` 設定為 `False` ，則在管線執行期間，一律會為此步驟產生新的執行。

您可以使用單一步驟建立管線，但幾乎一律會選擇將整體程式分成數個步驟。 例如，您可能會有資料準備、定型、模型比較和部署的步驟。 例如，您可能會認為在 `data_prep_step` 上述指定的之後，下一個步驟可能是定型：

```python
train_source_dir = "./train_src"
train_entry_point = "train.py"

training_results = OutputFileDatasetConfig(name = "training_results",
                                           destination = def_blob_store)

train_step = PythonScriptStep(
    script_name=train_entry_point,
    source_directory=train_source_dir,
    arguments=["--prepped_data", output_data1, "--training_results", training_results],
    compute_target=compute_target,
    runconfig=aml_run_config,
    allow_reuse=True
)
```

上述程式碼與資料準備步驟的程式碼非常類似。 定型程式碼位於與資料準備程式碼不同的目錄中。 `OutputFileDatasetConfig`資料準備步驟的輸出 `output_data1` 會用來做為定型步驟的_輸入_。 建立新的 `OutputFileDatasetConfig` 物件， `training_results` 以保存後續比較或部署步驟的結果。 

定義步驟之後，您必須使用這些步驟中的部分或全部步驟來建置管線。

> [!NOTE]
> 當您定義步驟或建立管線時，不會將任何檔案或資料上傳至 Azure Machine Learning。

```python
# list of steps to run (`compare_step` definition not shown)
compare_models = [data_prep_step, train_step, compare_step]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compare_models])
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

從 Azure Blob 儲存體、Azure 檔案儲存體、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL Database 和適用於 PostgreSQL 的 Azure 資料庫建立的資料集會可用來作為任何管線步驟的輸入。 您可以將輸出寫入至 [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py)、 [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)，或者，如果您想要將資料寫入至特定資料存放區，請使用 [OutputFileDatasetConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py)。 

> [!IMPORTANT]
> `OutputFileDatasetConfig`只有 Azure Blob、azure 檔案共用、ADLS gen 1 和 ADLS gen 2 資料存放區支援將輸出資料寫回資料存放區。

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

然後，您可以使用 [執行 input_datasets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#input-datasets) 字典，在管線中取出資料集。

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

這一行 `Run.get_context()` 值得您強調。 此函式會抓取， `Run` 表示目前的實驗性執行。 在上述範例中，我們使用它來取出已註冊的資料集。 物件的另一種常見用法 `Run` 是同時取出實驗本身以及實驗所在的工作區： 

```python
# Within a PythonScriptStep

ws = Run.get_context().experiment.workspace
```

如需更多詳細資料，包括傳遞和存取資料的替代方式，請參閱將 [資料移入和移出 ML 管線步驟 (Python) ](how-to-move-data-in-out-of-pipelines.md)。

## <a name="submit-the-pipeline"></a>提交管線

當您提交管線時，Azure Machine Learning 會檢查每個步驟的相依性，並上傳您所指定之來原始目錄的快照集。 如果未指定來源目錄，則會上傳目前的本機目錄。 快照集也會另存為您工作區實驗的一部分。

> [!IMPORTANT]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
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
* 設定和物件的存取權 `Dataset` `OutputFileDatasetConfig` 。 在 `as_mount()` 存取模式中，會使用保險絲來提供虛擬存取。 如果不支援裝載，或使用者指定了存取權 `as_upload()` ，則會改為將資料複製到計算目標。
* 在步驟定義中指定的計算目標內執行步驟。 
* 建立步驟所指定的成品，例如記錄、stdout 和 stderr、計量及輸出。 這些成品會接著上傳並保留在使用者的預設資料存放區中。

![以管線的形式執行實驗的圖表](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

如需詳細資訊，請參閱 [實驗類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) 參考。

### <a name="view-results-of-a-pipeline"></a>查看管線的結果

查看您所有管線的清單，以及其在 studio 中的執行詳細資料：

1. 登入 [Azure Machine Learning Studio](https://ml.azure.com)。

1. [查看您的工作區](how-to-manage-workspace.md#view)。

1. 選取左側的 [ **管線** ] 以查看您的所有管線執行。
 ![機器學習管線清單](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. 選取特定管線以查看執行結果。

## <a name="git-tracking-and-integration"></a>Git 追蹤與整合

當您啟動來源目錄是本機 Git 存放庫的定型回合時，該存放庫的相關資訊會儲存在回合歷程記錄中。 如需詳細資訊，請參閱 [Azure Machine Learning 的 Git 整合](concept-train-model-git-integration.md)。

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

所有已發佈的管線都有 REST 端點。 使用管線端點時，您可以從任何外部系統（包括非 Python 用戶端）觸發管線的執行。 此端點可在批次評分和重新訓練案例中，提供「受控的可重複性」。

若要叫用上述管線的執行，您需要 Azure Active Directory authentication 標頭權杖，如 [>azurecliauthentication 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) 參考中所述，或在 Azure Machine Learning 筆記本的 [驗證](https://aka.ms/pl-restep-auth) 中取得更多詳細資料。

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>建立已建立版本的管線端點

您可以建立管線端點，其中包含多個已發佈的管線。 這可以像已發佈的管線一樣使用，但會在您逐一查看並更新 ML 管線時提供固定的 REST 端點。

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PipelineEndpoint.get(workspace=ws, name="My_Published_Pipeline")
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

使用 REST API 可以完成相同動作：

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

1. 選取左側的 [ **端點**]。

1. 在頂端，選取 **管線端點**。
 ![機器學習已發佈管線的清單](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. 選取特定管線以執行、取用或查看管線端點先前執行的結果。

### <a name="disable-a-published-pipeline"></a>停用已發佈的管線

若要從已發佈的管線清單中隱藏管線，請在 studio 或從 SDK 中停用管線：

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

您可以使用重新啟用它 `p.enable()` 。 如需詳細資訊，請參閱 [PublishedPipeline 類別](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py) 參考。

## <a name="caching--reuse"></a>快取 & 重複使用  

為了優化和自訂管線的行為，您可以在快取和重複使用方面做一些事。 例如，您可以選擇：
+ 藉由在步驟定義期間設定，關閉**步驟執行輸出的預設重複使用** `allow_reuse=False` 。 [step definition](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) 在共同作業環境中使用管線時，重複使用是關鍵，因為消除不必要的執行可提供靈活性。 不過，您可以選擇不重複使用。
+ **執行中所有步驟的強制輸出** 重新產生 `pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

依預設， `allow_reuse` 會啟用，並且會 `source_directory` 雜湊步驟定義中指定的。 因此，如果指定步驟的腳本維持相同的 (`script_name` 、輸入和參數) ，而中沒有任何其他專案已 ` source_directory` 變更，則會重複使用上一個步驟執行的輸出，作業不會提交至計算，而從上一次執行的結果則會立即提供給下一個步驟。

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
- 請參閱 [azureml-管線-核心](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) 封裝和 [azureml-管線-步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) 套件的 SDK 參考說明。
- 請參閱 [如何](how-to-debug-pipelines.md) 取得對管線進行偵錯工具和疑難排解的秘訣。

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]
