---
title: 教學課程：批次評分的 ML 管線
titleSuffix: Azure Machine Learning
description: 在本教學課程中，您將建立機器學習管線，用以在影像分類模型上執行批次評分。 Azure Machine Learning 可讓您專注於機器學習，而不是基礎結構和自動化。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: lobrien
ms.author: laobri
ms.reviewer: laobri
ms.date: 10/13/2020
ms.custom: contperfq4, devx-track-python
ms.openlocfilehash: 3f131e1f8d5604e566c8d7b41fa9d45cb7d2a7a2
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92054877"
---
# <a name="tutorial-build-an-azure-machine-learning-pipeline-for-batch-scoring"></a>教學課程：建置 Azure Machine Learning 管線進行批次評分



在此進階教學課程中，您將了解如何建置 [Azure Machine Learning 管線](concept-ml-pipelines.md) 以執行批次評分作業。 機器學習管線會以速度、可攜性和重複使用性來將工作流程最佳化，讓您能夠專注於機器學習，而不是基礎結構和自動化。 在建置和發佈管線之後，您可以設定可讓您從任何平台上的任何 HTTP 程式庫觸發管線的 REST 端點。 

此範例會使用預先定型的 [Inception-V3](https://arxiv.org/abs/1512.00567) 迴旋神經網路模型 (在 Tensorflow 中實作)，來分類未標記的影像。 

在本教學課程中，您會完成下列工作：

> [!div class="checklist"]
> * 設定工作區 
> * 下載並儲存範例資料
> * 建立資料集物件以擷取和輸出資料
> * 您的工作區中下載、準備和註冊模型
> * 佈建計算目標和建立評分指令碼
> * 使用 `ParallelRunStep` 類別進行非同步批次評分
> * 建置、執行和發佈管線
> * 啟用管線的 REST 端點

如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

## <a name="prerequisites"></a>必要條件

* 如果您還沒有 Azure Machine Learning 工作區或 Notebook 虛擬機器，請完成[設定教學課程的第 1 部分](tutorial-1st-experiment-sdk-setup.md)。
* 完成設定教學課程之後，請使用相同的筆記本伺服器開啟 tutorials/machine-learning-pipelines-advanced/tutorial-pipeline-batch-scoring-classification.ipynb 筆記本。

如果您想要在自己的[本機環境](how-to-configure-environment.md#local)中執行設定教學課程，您可以在 [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) 上存取教學課程。 執行 `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` 以取得必要套件。

## <a name="configure-workspace-and-create-a-datastore"></a>設定工作區和建立資料存放區

從現有的 Azure Machine Learning 工作區建立工作區物件。

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

> [!IMPORTANT]
> 此程式碼片段預期工作區組態會儲存在目前目錄或其父系目錄中。 如需建立工作區的詳細資訊，請參閱[建立和管理 Azure Machine Learning 工作區](how-to-manage-workspace.md)。 如需將組態儲存至檔案的詳細資訊，請參閱[建立工作區組態檔](how-to-configure-environment.md#workspace)。

## <a name="create-a-datastore-for-sample-images"></a>建立影像範例的資料存放區

在 `pipelinedata` 帳戶上，從 `sampledata` 公用 Blob 容器取得 ImageNet 評估的公用資料範例。 呼叫 `register_azure_blob_container()`，讓資料可供名為 `images_datastore` 的工作區使用。 然後，將工作區預設資料存放區設定為輸出資料存放區。 使用輸出資料存放區對管線中的輸出評分。

如需如何存取資料的詳細資訊，請參閱[如何存取資料](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#python-sdk)。

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-dataset-objects"></a>建立資料集物件

在建置管線時，系統會使用 `Dataset` 物件從工作區的資料存放區讀取資料，並使用 `PipelineData` 物件在管線步驟之間傳輸中繼資料。

> [!Important]
> 本教學課程中的批次評分範例只會使用一個管線步驟。 在具有多個步驟的使用案例中，一般的流程會包含下列步驟：
>
> 1. 使用 `Dataset` 物件作為*輸入*以擷取原始資料、執行一些轉換，然後*輸出*`PipelineData` 物件。
>
> 2. 使用上一個步驟中的 `PipelineData` *輸出物件*作為*輸入物件*。 請在後續步驟中重複執行前述步驟。

在此案例中，您會針對輸入影像和分類標籤 (y-測試值) 建立對應至資料存放區目錄的 `Dataset` 物件。 您也會為批次評分輸出資料建立 `PipelineData` 物件。

```python
from azureml.core.dataset import Dataset
from azureml.pipeline.core import PipelineData

input_images = Dataset.File.from_files((batchscore_blob, "batchscoring/images/"))
label_ds = Dataset.File.from_files((batchscore_blob, "batchscoring/labels/"))
output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

如果您想要在稍後重複使用資料集，請將其註冊到工作區。 此為選用步驟。

```python

input_images = input_images.register(workspace = ws, name = "input_images")
label_ds = label_ds.register(workspace = ws, name = "label_ds")
```

## <a name="download-and-register-the-model"></a>下載並註冊模型

下載預先定型的 Tensorflow 模型，以用於管線中的批次評分。 首先，建立用來儲存模型的本機目錄。 然後，下載該模型並將其解壓縮。

```python
import os
import tarfile
import urllib.request

if not os.path.isdir("models"):
    os.mkdir("models")
    
response = urllib.request.urlretrieve("http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz", "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall("models")
```

接著，向您的工作區註冊該模型，以便您可以輕鬆地在管線程序中擷取模型。 在 `register()` 靜態函式中，`model_name` 參數是您在整個 SDK 中用來尋找模型的索引鍵。

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>建立和連結遠端計算目標

機器學習管線無法在本機執行，因此您必須在雲端資源或*遠端計算目標*上加以執行。 遠端計算目標是可重複使用的虛擬計算環境，您可以在其中執行實驗機器學習工作流程。 

執行下列程式碼來建立具有 GPU 功能的 [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py&preserve-view=true) 目標，然後將其連結至您的工作區。 如需計算目標的詳細資訊，請參閱[概念性文章](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)。


```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.exceptions import ComputeTargetException
compute_name = "gpu-cluster"

# checks to see if compute target already exists in workspace, else create it
try:
    compute_target = ComputeTarget(workspace=ws, name=compute_name)
except ComputeTargetException:
    config = AmlCompute.provisioning_configuration(vm_size="STANDARD_NC6",
                                                   vm_priority="lowpriority", 
                                                   min_nodes=0, 
                                                   max_nodes=1)

    compute_target = ComputeTarget.create(workspace=ws, name=compute_name, provisioning_configuration=config)
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="write-a-scoring-script"></a>撰寫評分指令碼

若要進行評分，請建立名為 `batch_scoring.py` 的批次評分指令碼，然後將其寫入至目前的目錄。 此指令碼會取得輸入影像、套用分類模型，然後將預測輸出至結果檔案。

`batch_scoring.py` 指令碼會採用下列參數，而這些參數從您稍後建立的 `ParallelRunStep` 傳入：

- `--model_name`:所用模型的名稱。
- `--labels_dir`:`labels.txt` 檔案的位置。

管線基礎結構會使用 `ArgumentParser` 類別將參數傳遞至管線步驟。 例如，在下列程式碼中，會對第一個引數 `--model_name` 指定屬性識別碼 `model_name`。 在 `init()` 函式中，會使用 `Model.get_model_path(args.model_name)` 來存取此屬性。


```python
%%writefile batch_scoring.py

import os
import argparse
import datetime
import time
import tensorflow as tf
from math import ceil
import numpy as np
import shutil
from tensorflow.contrib.slim.python.slim.nets import inception_v3

from azureml.core import Run
from azureml.core.model import Model
from azureml.core.dataset import Dataset

slim = tf.contrib.slim

image_size = 299
num_channel = 3


def get_class_label_dict(labels_dir):
    label = []
    labels_path = os.path.join(labels_dir, 'labels.txt')
    proto_as_ascii_lines = tf.gfile.GFile(labels_path).readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


def init():
    global g_tf_sess, probabilities, label_dict, input_images

    parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
    parser.add_argument('--model_name', dest="model_name", required=True)
    parser.add_argument('--labels_dir', dest="labels_dir", required=True)
    args, _ = parser.parse_known_args()

    label_dict = get_class_label_dict(args.labels_dir)
    classes_num = len(label_dict)

    with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
        input_images = tf.placeholder(tf.float32, [1, image_size, image_size, num_channel])
        logits, _ = inception_v3.inception_v3(input_images,
                                              num_classes=classes_num,
                                              is_training=False)
        probabilities = tf.argmax(logits, 1)

    config = tf.ConfigProto()
    config.gpu_options.allow_growth = True
    g_tf_sess = tf.Session(config=config)
    g_tf_sess.run(tf.global_variables_initializer())
    g_tf_sess.run(tf.local_variables_initializer())

    model_path = Model.get_model_path(args.model_name)
    saver = tf.train.Saver()
    saver.restore(g_tf_sess, model_path)


def file_to_tensor(file_path):
    image_string = tf.read_file(file_path)
    image = tf.image.decode_image(image_string, channels=3)

    image.set_shape([None, None, None])
    image = tf.image.resize_images(image, [image_size, image_size])
    image = tf.divide(tf.subtract(image, [0]), [255])
    image.set_shape([image_size, image_size, num_channel])
    return image


def run(mini_batch):
    result_list = []
    for file_path in mini_batch:
        test_image = file_to_tensor(file_path)
        out = g_tf_sess.run(test_image)
        result = g_tf_sess.run(probabilities, feed_dict={input_images: [out]})
        result_list.append(os.path.basename(file_path) + ": " + label_dict[result[0]])
    return result_list
```

> [!TIP]
> 本教學課程中的管線只有一個步驟，此步驟會將輸出寫入至檔案。 在有多個步驟的管線中，您也可以使用 `ArgumentParser` 來定義目錄以供寫入輸出資料，從而作為後續步驟的輸入。 如需使用 `ArgumentParser` 設計模式在多個管線步驟之間傳遞資料的範例，請參閱[筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb)。

## <a name="build-the-pipeline"></a>建置管線

在執行管線之前，請先建立物件以定義 Python 環境並建立指令碼 `batch_scoring.py` 所需的相依性。 所需的主要相依性是 Tensorflow，但您也會安裝 ParallelRunStep 所需的 `azureml-core` 和 `azureml-dataprep[fuse]`。 此外，請指定 Docker 和 Docker-GPU 支援。

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.15.2",
                                            "azureml-core", "azureml-dataprep[fuse]"])
env = Environment(name="parallelenv")
env.python.conda_dependencies = cd
env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="create-the-configuration-to-wrap-the-script"></a>建立用來包裝指令碼的設定

使用指令碼、環境設定和參數來建立管線步驟。 指定您已連結至工作區的計算目標。

```python
from azureml.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    environment=env,
    entry_script="batch_scoring.py",
    source_directory=".",
    output_action="append_row",
    mini_batch_size="20",
    error_threshold=1,
    compute_target=compute_target,
    process_count_per_node=2,
    node_count=1
)
```

### <a name="create-the-pipeline-step"></a>建立管線步驟

管線步驟是一個物件，會將執行管線所需的一切項目封裝起來，包括：

* 環境和相依性設定
* 要在其上執行管線的計算資源
* 輸入和輸出資料，以及任何自訂參數
* 在步驟進行期間，所要執行指令碼或 SDK 邏輯的參考

有多個類別會繼承自父代類別 [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py&preserve-view=true)。 您可以選擇類別，以使用特定的架構或堆疊來建置步驟。 在此範例中，您會使用 `ParallelRunStep` 類別，透過自訂的 Python 指令碼來定義步驟邏輯。 如果指令碼的引數是步驟的輸入或步驟的輸出，則必須將此引數*同時*定義在 `arguments` 陣列中，*以及*分別定義在 `input` 或 `output` 參數中。 

在有多個步驟的案例中，`outputs` 陣列中的物件參考將可以作為後續管線步驟的*輸入*。

```python
from azureml.pipeline.steps import ParallelRunStep
from datetime import datetime

parallel_step_name = "batchscoring-" + datetime.now().strftime("%Y%m%d%H%M")

label_config = label_ds.as_named_input("labels_input")

batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--model_name", "inception",
               "--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
    allow_reuse=False
)
```

如需可用於不同步驟類型的所有類別清單，請參閱[步驟套件](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py&preserve-view=true)。

## <a name="submit-the-pipeline"></a>提交管線

現在，請執行管線。 首先，使用工作區參考和您建立的管線步驟來建立 `Pipeline` 物件。 `steps` 參數是步驟的陣列。 在此案例中，批次評分只有一個步驟。 若要建置有多個步驟的管線，請在此陣列中依序放置步驟。

接下來，使用 `Experiment.submit()` 函式來提交管線以供執行。 `wait_for_completion` 函式會在管線建置程序中輸出記錄。 您可以使用記錄來查看目前的進度。

> [!IMPORTANT]
> 第一次執行管線大約需要 *15 分鐘*。 因為過程中必須下載所有相依性、建立 Docker 映像，並佈建和建立 Python 環境。 再次執行管線所需的時間會大幅縮短，因為過程中會重複使用這些資源，而不會再次建立。 不過，管線的總執行時間取決於每個管線步驟中執行的指令碼和程序的工作負載。

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline)
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>下載和檢閱輸出

執行下列程式碼，以下載從 `batch_scoring.py` 指令碼建立的輸出檔案。 然後，瀏覽評分結果。

```python
import pandas as pd

batch_run = next(pipeline_run.get_children())
batch_output = batch_run.get_output_data("scores")
batch_output.download(local_path="inception_results")

for root, dirs, files in os.walk("inception_results"):
    for file in files:
        if file.endswith("parallel_run_step.txt"):
            result_file = os.path.join(root, file)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10)
```

## <a name="publish-and-run-from-a-rest-endpoint"></a>從 REST 端點發佈和執行

執行下列程式碼將管線發佈到工作區。 在 Azure Machine Learning Studio 的工作區中，您可以看到管線的中繼資料，包括執行歷程記錄和持續時間。 您也可以從 Studio 手動執行管線。

發佈管線後，您即可使用 REST 端點從任何平台上的任何 HTTP 程式庫執行該管線。

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

若要從 REST 端點執行管線，您必須要有 OAuth2 Bearer-type 驗證標頭。 下列範例會使用互動式驗證來進行說明，但對於大部分需要自動化驗證或無周邊驗證的生產案例，請使用[本文中所述](how-to-setup-authentication.md)的服務主體驗證。

若要使用服務主體驗證，必須在 *Azure Active Directory* 建立中*應用程式註冊*。 首先，您必須產生用戶端密碼，然後將您的服務主體*角色存取權*授與機器學習工作區。 請使用 [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py&preserve-view=true) 類別管理您的驗證流程。 

[`InteractiveLoginAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.interactiveloginauthentication?view=azure-ml-py&preserve-view=true) 和 `ServicePrincipalAuthentication` 都繼承自 `AbstractAuthentication`。 在這兩種情況下，均應以相同方式使用 [`get_authentication_header()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.abstractauthentication?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-authentication-header--) 函式來擷取標頭：

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

從已發佈管線物件的 `endpoint` 屬性取得 REST URL。 您也可以在 Azure Machine Learning Studio 的工作區中找到 REST URL。 

建置對端點的 HTTP POST 要求。 請在要求中指定您的驗證標頭。 新增具有實驗名稱的 JSON 承載物件。

提出要求以觸發執行。 加入程式碼以從回應字典中存取 `Id` 金鑰，進而取得執行識別碼的值。

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"process_count_per_node": 6}})
run_id = response.json()["Id"]
```

使用執行識別碼來監視新執行的狀態。 新的執行還需要 10-15 分鐘才能完成。 

新的執行看起來會類似於您先前在教學課程中執行的管線。 您可以選擇不檢視完整輸出。

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>清除資源

如果您打算執行其他 Azure Machine Learning 教學課程，請不要完成本節。

### <a name="stop-the-compute-instance"></a>停止計算執行個體

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>刪除所有內容

如果您不打算使用您建立的資源，請刪除它們，以免產生任何費用：

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]。
1. 在資源群組清單中，選取您所建立的資源群組。
1. 選取 [刪除資源群組]。
1. 輸入資源群組名稱。 然後，選取 [刪除]。

您也可以保留資源群組，但刪除單一工作區。 顯示工作區屬性，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

在此機器學習管線教學課程中，您已執行下列工作：

> [!div class="checklist"]
> * 使用環境相依性建置管線，以在遠端 GPU 計算資源上執行。
> * 建立評分指令碼，以使用預先定型的 Tensorflow 模型來執行批次預測。
> * 發佈管線並使其可從 REST 端點執行。

如需使用機器學習 SDK 來建置管線的更多範例，請參閱[筆記本存放庫](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)。
