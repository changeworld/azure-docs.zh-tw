---
title: 對巨量資料執行批次預測
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure Machine Learning 中使用 ParallelRunStep，以非同步方式來取得大量資料的推斷。 ParallelRunStep 可提供現成可用的平行處理功能，並針對巨量資料使用案例最佳化以實現高輸送量、射後不理的推斷。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: jmartens, larryfr
ms.author: tracych
author: tracychms
ms.date: 07/16/2020
ms.custom: Build2020, tracking-python
ms.openlocfilehash: 475c5b3073b25c79b57a2ab507af642a8af3547f
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288884"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>使用 Azure Machine Learning 對大量資料執行批次推斷
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

了解如何使用 Azure Machine Learning 以非同步和平行的方式執行大量資料的批次推斷。 ParallelRunStep 提供現成的平行處理原則功能。

透過 ParallelRunStep，即可直接將離線推斷擴充至大型機器叢集的數 TB 結構化和非結構化資料，進而提升生產力並獲得最佳成本。

您會在本文中了解下列工作：

> 1. 設定機器學習資源。
> 1. 設定批次推斷資料輸入和輸出。
> 1. 根據 [MNIST](https://publicdataset.azurewebsites.net/dataDetail/mnist/) 資料集，準備預先定型的影像分類模型。 
> 1.  撰寫推斷指令碼。
> 1. 建立包含 ParallelRunStep 的[機器學習管線](concept-ml-pipelines.md)，並在 MNIST 測試映像上執行批次推斷。 
> 1. 重新提交具有新資料輸入和參數的批次推斷執行。 
> 1. 檢視結果。

## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* 如需引導式快速入門，但您還沒有 Azure Machine Learning 工作區，請先完成[設定教學課程](tutorial-1st-experiment-sdk-setup.md)。 

* 若要管理您自己的環境和相依性，請參閱關於如何設定自有本機環境的[操作指南](how-to-configure-environment.md)。

## <a name="set-up-machine-learning-resources"></a>設定機器學習資源

下列動作會設定要執行批次推斷管線所需的機器學習資源：

- 連線到工作區。
- 建立或連結現有的計算資源。

### <a name="configure-workspace"></a>設定工作區

從現有的工作區建立工作區物件。 `Workspace.from_config()` 會讀取 config.json 檔案，並將詳細資料載入到名為 ws 的物件。

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

> [!IMPORTANT]
> 此程式碼片段預期工作區組態會儲存在目前目錄或其父系目錄中。 如需建立工作區的詳細資訊，請參閱[建立和管理 Azure Machine Learning 工作區](how-to-manage-workspace.md)。 如需將組態儲存至檔案的詳細資訊，請參閱[建立工作區組態檔](how-to-configure-environment.md#workspace)。

### <a name="create-a-compute-target"></a>建立計算目標

在 Azure Machine Learning 中，*計算* (或*計算目標*) 係指會在您機器學習管線中執行計算步驟的機器或叢集。 請執行下列程式碼來建立以 CPU 為基礎的 [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) 目標。

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

## <a name="configure-inputs-and-output"></a>設定輸入和輸出

### <a name="create-a-datastore-with-sample-images"></a>建立具有影像範例的資料存放區

從 `pipelinedata` 帳戶上的公用 Blob 容器 `sampledata` 取得 MNIST 評估集。 建立名稱為 `mnist_datastore` 的資料存放區，並使其指向此容器。 在下列 `register_azure_blob_container` 呼叫中，將 `overwrite` 旗標設定為 `True` 將會覆寫先前使用該名稱建立的任何資料存放區。 

您可以藉由提供您自己的 `datastore_name`、`container_name` 和 `account_name` 值，將此步驟變更為指向您的 Blob 容器。

```python
from azureml.core import Datastore
from azureml.core import Workspace

mnist_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="mnist_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata",
                      overwrite=True)
```

接著，將工作區的預設資料存放區指定為輸出資料存放區。 您會將其用於推斷輸出。

當您建立工作區時，預設會將[檔案儲存體](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) 和 [Blob 儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) 連結至工作區。 檔案儲存體是工作區的預設資料存放區，但您也可以使用 Blob 儲存體作為資料存放區。 如需詳細資訊，請參閱 [Azure 儲存體選項](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)。

```python
def_data_store = ws.get_default_datastore()
```

### <a name="create-the-data-inputs"></a>建立資料輸入

批次推斷的輸入是您想要分割以進行平行處理的資料。 批次推斷管線會透過 [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)接受資料輸入。

`Dataset` 可用來在 Azure Machine Learning 中探索、轉換和管理資料。 有兩種類型：[`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) 和 [`FileDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py)。 在此範例中，您將使用 `FileDataset` 作為輸入。 `FileDataset` 讓您將檔案下載或掛接至您的計算。 您可以建立資料集，以建立資料來源位置的參考。 如果您對資料集套用任何子集轉換，這些轉換也會儲存在資料集中。 資料會保留在現有的位置，因此不會產生額外的儲存成本。

如需 Azure Machine Learning 資料集的詳細資訊，請參閱[建立和存取資料集 (預覽)](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)。

```python
from azureml.core.dataset import Dataset

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
```

若要在執行批次推斷管線時使用動態資料輸入，您可以將輸入 `Dataset` 定義為 [`PipelineParameter`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py)。 您可以在每次重新提交批次推斷管線執行時，指定輸入資料集。

```python
from azureml.data.dataset_consumption_config import DatasetConsumptionConfig
from azureml.pipeline.core import PipelineParameter

pipeline_param = PipelineParameter(name="mnist_param", default_value=input_mnist_ds)
input_mnist_ds_consumption = DatasetConsumptionConfig("minist_param_config", pipeline_param).as_mount()
```

### <a name="create-the-output"></a>建立輸出

[`PipelineData`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 物件可用來在管線步驟之間傳輸中繼資料。 在此範例中，您會將其用於推斷輸出。

```python
from azureml.pipeline.core import Pipeline, PipelineData

output_dir = PipelineData(name="inferences", datastore=def_data_store)
```

## <a name="prepare-the-model"></a>準備模型

[下載預先定型的影像分類模型](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz)，然後將其解壓縮至 `models` 目錄。

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

然後，向您的工作區註冊模型，使其可供計算資源使用。

```python
from azureml.core.model import Model

# Register the downloaded model 
model = Model.register(model_path="models/",
                       model_name="mnist",
                       tags={'pretrained': "mnist"},
                       description="Mnist trained tensorflow model",
                       workspace=ws)
```

## <a name="write-your-inference-script"></a>撰寫推斷指令碼

>[!Warning]
>下列程式碼只是[筆記本範例](https://aka.ms/batch-inference-notebooks)所使用的範例。 請針對您的案例建立自己的指令碼。

指令碼「必須包含」兩個函式：
- `init()`:請將此函式用於高成本或一般的準備，以進行後續的推斷。 例如，使用此函式將模型載入至全域物件。 此函式只會在程序開始時呼叫一次。
-  `run(mini_batch)`:此函式會針對每個 `mini_batch` 執行個體來執行。
    -  `mini_batch`：`ParallelRunStep` 會叫用 run 方法，並將 list 或 pandas `DataFrame` 作為引數傳遞給方法。 mini_batch 中的每個項目會是檔案路徑 (如果輸入是 `FileDataset`) 或 pandas `DataFrame` (如果輸入是 `TabularDataset`)。
    -  `response`：run() 方法應該傳回 pndas `DataFrame` 或陣列。 針對 append_row output_action，這些傳回的元素會附加至一般輸出檔案。 針對 summary_only，則會忽略元素的內容。 針對所有輸出動作，每個傳回的輸出元素會指出輸入迷你批次中一次成功的輸入元素執行。 確保執行結果中有足夠的資料可將輸入對應至執行輸出結果。 執行輸出將會寫入至輸出檔案，而且不保證會按照順序，所以您應該在輸出中使用某個索引鍵以將其對應至輸入。

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://aka.ms/batch-inference-notebooks)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

如果推斷指令碼所在的相同目錄中有另一個檔案或資料夾，您可以藉由尋找目前的工作目錄來對其進行參照。

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>建置並執行包含 ParallelRunStep 的管線

現在您已擁有所需的所有項目：資料輸入、模型、輸出和推斷指令碼。 現在可以建置包含 ParallelRunStep 的批次推斷管線了。

### <a name="prepare-the-environment"></a>準備環境

首先，為指令碼指定相依性。 這麼做可讓您安裝 pip 套件，以及設定環境。

一律在 pip 套件清單中包含 **azureml-core** 和 **azureml-dataset-runtime[pandas, fuse]** 。 如果您使用自訂 Docker 映像 (user_managed_dependencies=True)，也應該安裝 Conda。

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.15.2", "pillow", 
                                                          "azureml-core", "azureml-dataset-runtime[pandas, fuse]"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="specify-the-parameters-using-parallelrunconfig"></a>使用 ParallelRunConfig 指定參數

`ParallelRunConfig` 是 Azure Machine Learning 管線中 `ParallelRunStep` 執行個體的主要組態。 您可以用此設定來包裝指令碼並設定必要參數，包括下列各項：
- `entry_script`:作為本機檔案路徑的使用者指令碼，將會在多個節點上平行執行。 如果 `source_directory` 存在，請使用相對路徑。 否則，使用可在機器上存取的路徑即可。
- `mini_batch_size`:傳遞至單一 `run()` 呼叫的迷你批次大小。 (選擇性；預設值為 `10` 個檔案 (若為 `FileDataset`) 和 `1MB` (若為 `TabularDataset`)。)
    - 針對 `FileDataset`，這是最小值為 `1` 的檔案數目。 您可以將多個檔案結合成一個迷你批次。
    - 針對 `TabularDataset`，這是資料的大小。 範例值為 `1024`、`1024KB`、`10MB` 和 `1GB`。 建議值是 `1MB`。 `TabularDataset` 中的迷你批次絕對不會跨越檔案界限。 例如，如果您有各種大小的 .csv 檔案，最小檔案為 100 KB，最大檔案則為 10 MB。 如果您設定 `mini_batch_size = 1MB`，則系統會將小於 1 MB 的檔案視為一個迷你批次。 大於 1 MB 的檔案則會分割成多個迷你批次。
- `error_threshold`:處理期間應該忽略的記錄失敗數目 (針對 `TabularDataset`) 和檔案失敗數目 (針對 `FileDataset`)。 如果整個輸入的錯誤計數超過此值，作業便會中止。 錯誤閾值適用於整個輸入，而非適用於傳送至 `run()` 方法的個別迷你批次。 範圍為 `[-1, int.max]`。 `-1` 部分會指出要在處理期間忽略所有失敗。
- `output_action`:下列其中一個值說明輸出的資料會如何被組合：
    - `summary_only`:使用者指令碼會儲存輸出。 `ParallelRunStep` 只會將輸出用於計算錯誤閾值。
    - `append_row`:針對所有輸入，輸出檔案夾中只會建立一個檔案，並以直線分隔的方式附加所有輸出。
- `append_row_file_name`:若要自訂 append_row output_action 的輸出檔名稱 (選用，預設值為 `parallel_run_step.txt`)。
- `source_directory`:資料夾的路徑，此資料夾包含要在計算目標上執行的所有檔案 (選擇性)。
- `compute_target`:只支援 `AmlCompute`。
- `node_count`:要用來執行使用者指令碼的計算節點數目。
- `process_count_per_node`:每個節點的處理序數目。 最佳做法是設定為節點擁有的 GPU 或 CPU 數目 (選用，預設值為 `1`)。
- `environment`:Python 環境定義。 您可以將其設定為使用現有 Python 環境，也可以設定暫存環境。 定義也會負責設定必要的應用程式相依性 (選擇性)。
- `logging_level`:記錄詳細程度。 增加詳細程度的值包括：`WARNING`、`INFO` 和 `DEBUG`。 (選擇性；預設值為 `INFO`)
- `run_invocation_timeout`:`run()` 方法叫用逾時 (以秒為單位)。 (選擇性；預設值為 `60`)
- `run_max_try`:迷你批次的 `run()` 嘗試次數上限。 如果擲回例外狀況，或達到 `run_invocation_timeout` 時未傳回任何內容 (選用；預設值為 `3`)，則 `run()` 會失敗。 

您可以將 `mini_batch_size`、`node_count`、`process_count_per_node`、`logging_level`、`run_invocation_timeout` 和 `run_max_try` 指定為 `PipelineParameter`，以便在重新提交管線執行時，可以微調參數值。 在此範例中，您會針對 `mini_batch_size` 和 `Process_count_per_node` 使用 `PipelineParameter`，而且將會在稍後重新提交執行時變更這些值。 

此範例假設您使用稍早所討論的 `digit_identification.py` 指令碼。 如果您使用自己的指令碼，請據以變更 `source_directory` 和 `entry_script` 參數。

```python
from azureml.pipeline.core import PipelineParameter
from azureml.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory='.',
    entry_script="digit_identification.py",
    mini_batch_size=PipelineParameter(name="batch_size_param", default_value="5"),
    error_threshold=10,
    output_action="append_row",
    append_row_file_name="mnist_outputs.txt",
    environment=batch_env,
    compute_target=compute_target,
    process_count_per_node=PipelineParameter(name="process_count_param", default_value=2),
    node_count=2)
```

### <a name="create-the-parallelrunstep"></a>建立 ParallelRunStep

使用指令碼、環境組態和參數來建立 ParallelRunStep。 指定您已附加至工作區的計算目標以作為推斷指令碼的執行目標。 使用 `ParallelRunStep` 來建立批次推斷管線步驟，其採用下列所有參數：
- `name`:步驟的名稱，具有下列命名限制：唯一的、3 至 32 個字元，且 RegEx ^\[a-z\]([-a-z0-9]*[a-z0-9])?$。
- `parallel_run_config`:如先前所定義的 `ParallelRunConfig` 物件。
- `inputs`:要分割以進行平行處理的一或多個單一類型 Azure Machine Learning 資料集。
- `side_inputs`:一或多個參考資料或資料集，用來作為不需要分割的端輸入。
- `output`:對應至輸出目錄的 `PipelineData` 物件。
- `arguments`:傳遞至使用者指令碼的引數清單。 使用 unknown_args，在您的輸入腳本中擷取 (選用)。
- `allow_reuse`:在使用相同設定/輸入執行時，步驟是否應重複使用先前的結果。 如果此參數為 `False`，則在管線執行期間，一律會為此步驟產生新的執行。 (選擇性；預設值為 `True`。)

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```
### <a name="create-and-run-the-pipeline"></a>建立並執行管線

現在，請執行管線。 首先，使用工作區參考和您建立的管線步驟來建立 [`Pipeline`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) 物件。 `steps` 參數是步驟的陣列。 在此案例中，批次推斷只有一個步驟。 若要建置有多個步驟的管線，請在此陣列中依序放置步驟。

接下來，使用 `Experiment.submit()` 函式來提交管線以供執行。

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
experiment = Experiment(ws, 'digit_identification')
pipeline_run = experiment.submit(pipeline)
```

## <a name="monitor-the-batch-inference-job"></a>監視批次推斷作業

批次推斷作業需要很長的時間才能完成。 此範例會使用 Jupyter 小工具來監視進度。 您也可以使用下列方式來監視作業的進度：

* Azure Machine Learning Studio。 
* 來自 [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py) 物件的主控台輸出。

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="resubmit-a-run-with-new-data-inputs-and-parameters"></a>以新的資料輸入和參數重新提交執行

由於您已進行輸入，並將數個項目設定為 `PipelineParameter`，因此可以重新提交具有不同資料集輸入的批次推斷執行並微調參數，而不需要建立全新的管線。 您會使用相同的資料存放區，但只會使用單一影像作為資料輸入。

```python
path_on_datastore = mnist_blob.path('mnist/0.png')
single_image_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)

pipeline_run_2 = experiment.submit(pipeline, 
                                   pipeline_parameters={"mnist_param": single_image_ds, 
                                                        "batch_size_param": "1",
                                                        "process_count_param": 1}
)

pipeline_run_2.wait_for_completion(show_output=True)
```
## <a name="view-the-results"></a>View the results

上述執行的結果會寫入 `PipelineData` 物件中指定的 `DataStore` 作為輸出資料，在此案例中稱為「推斷」。 結果會儲存在預設的 Blob 容器中，您可以瀏覽至您的儲存體帳戶，並透過儲存體總管檢視，檔案路徑為 azureml-blobstore-*GUID*/azureml/*RunId*/*output_dir*。

您也可以下載此資料以檢視結果。 以下是用來查看前 10 個資料列的範例程式碼。

```python
import pandas as pd
import tempfile

batch_run = pipeline_run.find_step_run(parallelrun_step.name)[0]
batch_output = batch_run.get_output_data(output_dir.name)

target_dir = tempfile.mkdtemp()
batch_output.download(local_path=target_dir)
result_file = os.path.join(target_dir, batch_output.path_on_datastore, parallel_run_config.append_row_file_name)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10) 
```

## <a name="next-steps"></a>後續步驟

若要查看此程序的完整運作過程，請試試[批次推斷筆記本](https://aka.ms/batch-inference-notebooks)。 

如需 ParallelRunStep 的偵錯和疑難排解指引，請參閱[操作指南](how-to-debug-parallel-run-step.md)。

如需管線的偵錯和疑難排解指引，請參閱[操作指南](how-to-debug-pipelines.md)。

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

