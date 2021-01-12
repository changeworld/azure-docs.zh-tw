---
title: 針對 ParallelRunStep 進行疑難排解
titleSuffix: Azure Machine Learning
description: 當您在機器學習管線中使用 ParallelRunStep 收到錯誤時，如何進行疑難排解的秘訣。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.custom: troubleshooting
ms.reviewer: larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 09/23/2020
ms.openlocfilehash: 6ea796fb2ec038a03595d37d903fe8ee3ce904db
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98070264"
---
# <a name="troubleshooting-the-parallelrunstep"></a>針對 ParallelRunStep 進行疑難排解

在本文中，您將瞭解如何在使用[AZURE MACHINE LEARNING SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)的[ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?preserve-view=true&view=azure-ml-py)類別取得錯誤時進行疑難排解。

如需針對管線進行疑難排解的一般秘訣，請參閱 [疑難排解機器學習管線](how-to-debug-pipelines.md)。

## <a name="testing-scripts-locally"></a>在本機測試指令碼

 您的 ParallelRunStep 會以 ML 管線中的步驟執行。 您可能會想要在 [本機測試您的腳本](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines) ，做為第一個步驟。

##  <a name="script-requirements"></a>指令碼需求

的腳本 `ParallelRunStep` *必須包含* 兩個函式：
- `init()`:請將此函式用於高成本或一般的準備，以進行後續的推斷。 例如，使用此函式將模型載入至全域物件。 此函式只會在程序開始時呼叫一次。
-  `run(mini_batch)`:此函式會針對每個 `mini_batch` 執行個體來執行。
    -  `mini_batch`：`ParallelRunStep` 會叫用 run 方法，並將 list 或 pandas `DataFrame` 作為引數傳遞給方法。 mini_batch 中的每個項目會是檔案路徑 (如果輸入是 `FileDataset`) 或 pandas `DataFrame` (如果輸入是 `TabularDataset`)。
    -  `response`：run() 方法應該傳回 pndas `DataFrame` 或陣列。 針對 append_row output_action，這些傳回的元素會附加至一般輸出檔案。 針對 summary_only，則會忽略元素的內容。 針對所有輸出動作，每個傳回的輸出元素會指出輸入迷你批次中一次成功的輸入元素執行。 確保執行結果中有足夠的資料可將輸入對應至執行輸出結果。 執行輸出將會寫入至輸出檔案，而且不保證會按照順序，所以您應該在輸出中使用某個索引鍵以將其對應至輸入。

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
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

### <a name="parameters-for-parallelrunconfig"></a>ParallelRunConfig 的參數

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

### <a name="parameters-for-creating-the-parallelrunstep"></a>用來建立 ParallelRunStep 的參數

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

## <a name="debugging-scripts-from-remote-context"></a>從遠端內容偵錯指令碼

從在本機對評分指令碼進行偵錯轉換到在實際管線中對評分指令碼進行偵錯，可能是困難的一大步驟。 如需在入口網站中尋找記錄的詳細資訊，請參閱  [從遠端內容對腳本進行偵錯工具的機器學習管線一節](how-to-debug-pipelines.md)。 該結中的資訊也適用於 ParallelRunStep。

例如，記錄檔 `70_driver_log.txt` 包含啟動 ParallelRunStep 程式碼的控制器所產生的資訊。

ParallelRunStep 作業具有分散的特性，因此會有來自數個不同來源的記錄。 不過，系統會建立兩個提供高階資訊的合併檔案：

- `~/logs/job_progress_overview.txt`:此檔案會提供關於目前為止所建立的迷你批次 (也稱為工作) 數目，以及目前為止處理的迷你批次數目的高階資訊。 在這部分，會顯示作業的結果。 如果作業失敗，則會顯示錯誤訊息，以及應從何處開始進行疑難排解。

- `~/logs/sys/master_role.txt`：此檔案會提供主要節點 (也稱為執行中工作的 orchestrator) view。 其中包含工作建立、進度監視和執行結果。

使用 EntryScript 協助程式和 print 陳述式從輸入指令碼產生的記錄，將位於下列檔案中：

- `~/logs/user/entry_script_log/<ip_address>/<process_name>.log.txt`：這些檔案是使用 EntryScript helper 從 entry_script 寫入的記錄。

- `~/logs/user/stdout/<ip_address>/<process_name>.stdout.txt`：這些檔案是 stdout 的記錄 (例如 entry_script 的 print 語句) 。

- `~/logs/user/stderr/<ip_address>/<process_name>.stderr.txt`：這些檔案是 entry_script stderr 的記錄。

若要概略了解指令碼中的錯誤，可以參考：

- `~/logs/user/error.txt`:此檔案會嘗試彙總指令碼中的錯誤。

如需指令碼中所含錯誤的詳細資訊，可以參考：

- `~/logs/user/error/`：包含載入和執行輸入腳本時擲回之例外狀況的完整堆疊追蹤。

如果您需要完整了解每個節點執行分數指令碼的方式，請查看每個節點的個別程序記錄。 程序記錄會位於 `sys/node` 資料夾中，依背景工作節點分組：

- `~/logs/sys/node/<ip_address>/<process_name>.txt`：此檔案會提供每個迷你批次的詳細資訊，因為它是由背景工作挑選或完成。 針對每個迷你批次，此檔案會包含：

    - 背景工作程序的 IP 位址和 PID。 
    - 項目總數、成功處理的項目計數，以及失敗的項目計數。
    - 開始時間、持續時間、處理時間和執行方法時間。

您也可以找到每個背景工作程序的資源使用量資訊。 這項資訊採用 CSV 格式，位於 `~/logs/sys/perf/<ip_address>/node_resource_usage.csv` 中。 每個處理常式的相關資訊可在下取得 `~logs/sys/perf/<ip_address>/processes_resource_usage.csv` 。

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>如何從遠端內容中的使用者指令碼進行記錄？

ParallelRunStep 可根據 process_count_per_node，在一個節點上執行多個進程。 若要從節點上的每個進程整理記錄，併合並 print 和 log 語句，我們建議使用 ParallelRunStep 記錄器，如下所示。 您會從 EntryScript 取得記錄器，並讓記錄顯示在入口網站的 [ **記錄檔/使用者** ] 資料夾中。

**使用記錄器的範例輸入指令碼：**
```python
from azureml_user.parallel_run import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>如何將端輸入 (例如，包含查閱資料表的一或多個檔案) 傳至我所有的背景工作角色？

使用者可以使用 ParalleRunStep 的 side_inputs 參數，將參考資料傳遞給腳本。 提供做為 side_inputs 的所有資料集都會掛接在每個背景工作節點上。 使用者可以藉由傳遞引數來取得掛接的位置。

建立包含參考資料的 [資料集](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py) ，並向您的工作區註冊該資料集。 將其傳至您 `ParallelRunStep` 的 `side_inputs` 參數。 此外，您可以在區段中新增其路徑， `arguments` 以輕鬆存取其掛接的路徑：

```python
label_config = label_ds.as_named_input("labels_input")
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

之後，您可以在推斷指令碼中加以存取 (例如，在 init() 方法中)，如下所示：

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

### <a name="how-to-use-input-datasets-with-service-principal-authentication"></a>如何使用輸入資料集搭配服務主體驗證？

使用者可以傳遞輸入資料集和工作區中使用的服務主體驗證。 在 ParallelRunStep 中使用此類資料集需要註冊資料集，才能建立 ParallelRunStep 設定。

```python
service_principal = ServicePrincipalAuthentication(
    tenant_id="**_",
    service_principal_id="_*_",
    service_principal_password="_*_")
 
ws = Workspace(
    subscription_id="_*_",
    resource_group="_*_",
    workspace_name="_*_",
    auth=service_principal
    )
 
default_blob_store = ws.get_default_datastore() # or Datastore(ws, '_*_datastore-name_*_') 
ds = Dataset.File.from_files(default_blob_store, '_*path**_')
registered_ds = ds.register(ws, '_*_dataset-name_*_', create_new_version=True)
```

## <a name="next-steps"></a>後續步驟

_ 請參閱 [示範 Azure Machine Learning 管線的這些 Jupyter 筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)

* 請參閱 SDK 參考以取得 [azureml 管線-步驟](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py) 套件的說明。 ParallelRunStep 類別的視圖參考 [檔](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep?preserve-view=true&view=azure-ml-py) 。

* 遵循搭配 ParallelRunStep 使用管線的 [advanced 教學](tutorial-pipeline-batch-scoring-classification.md) 課程。 本教學課程會示範如何將另一個檔案作為端輸入傳遞。