---
title: 針對 ML 管線進行疑難排解
titleSuffix: Azure Machine Learning
description: 當您在執行機器學習管線時遇到錯誤時，如何進行疑難排解。 協助您在遠端執行前後進行腳本的常見陷阱和秘訣。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 10/22/2020
ms.topic: troubleshooting
ms.custom: troubleshooting, devx-track-python, contperf-fy21q2
ms.openlocfilehash: 0f27688e31f772cc8d784371aa570d55c41f5695
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98131809"
---
# <a name="troubleshooting-machine-learning-pipelines"></a>針對機器學習管線進行疑難排解

在本文中，您將瞭解如何在[AZURE MACHINE LEARNING SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)和[Azure Machine Learning 設計](./concept-designer.md)工具中的[機器學習管線](concept-ml-pipelines.md)發生錯誤時進行疑難排解。 

## <a name="troubleshooting-tips"></a>疑難排解秘訣

下表包含管線開發期間的常見問題，並提供潛在的解決方案。

| 問題 | 可能的解決方法 |
|--|--|
| 無法將資料傳遞至 `PipelineData` 目錄 | 確定您已在腳本中建立一個目錄，該目錄會對應至管線預期步驟輸出資料的位置。 在大部分的情況下，輸入引數會定義輸出目錄，然後您會明確建立目錄。 使用 `os.makedirs(args.output_dir, exist_ok=True)` 建立輸出目錄。 如需顯示此設計模式的評分腳本範例，請參閱 [教學](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) 課程。 |
| 相依性 bug | 如果您在本機測試時看到遠端管線中的相依性錯誤，請確認您的遠端環境相依性和版本符合您測試環境中的相依性錯誤。  (查看 [環境建立、快取和重複使用](./concept-environments.md#environment-building-caching-and-reuse)|
| 計算目標有不明確的錯誤 | 請嘗試刪除並重新建立計算目標。 重新建立計算目標很快速，可解決一些暫時性問題。 |
| 管線未重複使用步驟 | 預設會啟用步驟重複使用，但請確定您未在管線步驟中將它停用。 如果已停用重複使用， `allow_reuse` 則步驟中的參數會設定為 `False` 。 |
| 不必要地重新執行管線 | 為確保只有在基礎資料或腳本變更時才會重新執行步驟，請將每個步驟的原始程式碼目錄分離。 如果您使用相同的來原始目錄進行多個步驟，可能會遇到不必要的重新執行。 `source_directory`在管線步驟物件上使用參數，以指向該步驟的隔離目錄，並確保您不會 `source_directory` 針對多個步驟使用相同的路徑。 |
| 逐步減緩定型 epoch 或其他迴圈行為 | 請嘗試將任何檔案寫入（包括記錄）切換 `as_mount()` 至 `as_upload()` 。 **掛接** 模式使用遠端虛擬化檔案系統，並在每次將檔案附加至時，上傳整個檔案。 |
| 計算目標需要很長的時間才能啟動 | 計算目標的 Docker 映射是從 Azure Container Registry (ACR) 載入的。 根據預設，Azure Machine Learning 會建立使用 *基本* 服務層級的 ACR。 將工作區的 ACR 變更為「標準」或「進階層」，可減少建立和載入映射所需的時間。 如需詳細資訊，請參閱 [Azure Container Registry 服務層級](../container-registry/container-registry-skus.md)。 |

### <a name="authentication-errors"></a>驗證錯誤

如果您從遠端工作執行計算目標的管理作業，您會收到下列錯誤的其中一個： 

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

例如，如果您嘗試從 ML 管線建立或連結為遠端執行所提交的計算目標，您會收到錯誤。
## <a name="troubleshooting-parallelrunstep"></a>故障 排除 `ParallelRunStep` 

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
- `output`： `OutputFileDatasetConfig` 對應至輸出目錄的物件。
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

## <a name="debugging-techniques"></a>調試技術

有三種主要的方法可以用來調試管線： 

* 在本機電腦上進行個別管線步驟的偵錯工具
* 使用記錄和 Application Insights 來隔離及診斷問題來源
* 將遠端偵錯程式附加至在 Azure 中執行的管線

### <a name="debug-scripts-locally"></a>在本機調試腳本

管線中最常見的失敗之一，是網域腳本不會如預期執行，或在遠端計算內容中包含難以進行調試的執行階段錯誤。

管線本身無法在本機執行，但是在您的本機電腦上隔離執行腳本可讓您更快速地進行偵錯工具，因為您不需要等待計算和環境建立程式。 需要進行一些開發工作才能執行這項作業：

* 如果您的資料位於雲端資料存放區，您將需要下載資料，並將其提供給您的腳本。 使用較小的資料範例，是在執行時間上剪下並快速取得腳本行為意見的好方法
* 如果您嘗試模擬中繼管線步驟，您可能需要手動建立特定腳本預期來自先前步驟的物件類型。
* 您也必須定義自己的環境，並複寫您的遠端計算環境中定義的相依性

在您的本機環境中執行腳本設定之後，您就可以更輕鬆地執行偵錯工具，例如：

* 附加自訂的 debug configuration
* 暫停執行並檢查物件狀態
* 捕捉在執行時間之前不會公開的類型或邏輯錯誤

> [!TIP] 
> 一旦您可以驗證腳本是否如預期般執行，下一步就是在單一步驟管線中執行腳本，然後再嘗試使用多個步驟在管線中執行腳本。

## <a name="configure-write-to-and-review-pipeline-logs"></a>設定、寫入和審核管線記錄

在本機測試腳本是一個很好的方法，可在您開始建立管線之前，先將主要的程式碼片段和複雜邏輯進行偵錯工具，但在某些情況下，您可能需要在實際管線執行期間先行編譯腳本，尤其是在診斷管線步驟之間的互動時所發生的行為時。 建議您 `print()` 在步驟腳本中使用語句，讓您可以在遠端執行期間查看物件狀態和預期的值，類似于如何進行 JavaScript 程式碼的偵錯工具。

### <a name="logging-options-and-behavior"></a>記錄選項和行為

下表提供管線的不同偵錯工具選項的資訊。 這並不是詳盡的清單，因為除了此處顯示的 Azure Machine Learning、Python 和 OpenCensus 之外，還有其他選項存在。

| 程式庫                    | 類型   | 範例                                                          | Destination                                  | 資源                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Metric | `run.log(name, val)`                                             | Azure Machine Learning 入口網站 UI             | [如何追蹤實驗](how-to-track-experiments.md)<br>[azureml. 核心. 執行類別](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py)                                                                                                                                                 |
| Python 列印/記錄    | 記錄    | `print(val)`<br>`logging.info(message)`                          | 驅動程式記錄，Azure Machine Learning 設計工具 | [如何追蹤實驗](how-to-track-experiments.md)<br><br>[Python 記錄](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | 記錄    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights-追蹤                | [對 Application Insights 中的管線進行偵錯](./how-to-log-pipelines-application-insights.md)<br><br>[OpenCensus Azure 監視器匯出工具](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Python 記錄操作手冊](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>記錄選項範例

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning 設計工具

針對在設計工具中建立的管線，您可以在 [撰寫] 頁面或 [管線執行詳細資料] 頁面中找到 **70_driver_log** 檔案。

### <a name="enable-logging-for-real-time-endpoints"></a>啟用即時端點的記錄

為了在設計工具中疑難排解和偵測即時端點，您必須使用 SDK 啟用應用程式深入解析記錄。 記錄功能可讓您針對模型部署和使用問題進行疑難排解及進行調試。 如需詳細資訊，請參閱已 [部署模型的記錄](./how-to-enable-app-insights.md)。 

### <a name="get-logs-from-the-authoring-page"></a>從撰寫頁面取得記錄

當您提交管線執行並停留在 [撰寫中] 頁面時，您可以在每個模組執行完成時，找到針對每個模組產生的記錄檔。

1. 選取在撰寫畫布中完成執行的模組。
1. 在模組的右窗格中，移至 [  **輸出 + 記錄** ] 索引標籤。
1. 展開右窗格，然後選取 [ **70_driver_log.txt** ]，以在瀏覽器中查看檔案。 您也可以在本機下載記錄。

    ![設計工具中的展開輸出窗格](./media/how-to-debug-pipelines/designer-logs.png)？ view = azure-ml-.py&preserve-view = true) ？ view = azure-ml-.py&preserve = true) 

### <a name="get-logs-from-pipeline-runs"></a>從管線執行取得記錄

您也可以在 [管線執行詳細資料] 頁面中找到特定回合的記錄檔，這可以在 studio 的 [ **管線** ] 或 [ **實驗** ] 區段中找到。

1. 選取在設計工具中建立的管線執行。

    ![管線執行頁面](./media/how-to-debug-pipelines/designer-pipelines.png)

1. 在預覽窗格中選取模組。
1. 在模組的右窗格中，移至 [  **輸出 + 記錄** ] 索引標籤。
1. 展開右窗格以查看瀏覽器中的 **70_driver_log.txt** 檔案，或選取要在本機下載記錄檔的檔案。

> [!IMPORTANT]
> 若要從 [管線執行詳細資料] 頁面更新管線，您必須將管線執行 **複製** 到新的管線草稿。 管線執行是管線的快照。 它類似于記錄檔，而且無法變更。 

## <a name="application-insights"></a>Application Insights
如需以這種方式使用 OpenCensus Python 程式庫的詳細資訊，請參閱本指南： [在 Application Insights 中進行機器學習管線的偵錯工具和疑難排解](./how-to-log-pipelines-application-insights.md)

## <a name="interactive-debugging-with-visual-studio-code"></a>使用 Visual Studio Code 的互動式調試

在某些情況下，您可能需要以互動方式來偵測 ML 管線中使用的 Python 程式碼。 藉由使用 Visual Studio Code (VS Code) 和 debugpy，您可以在定型環境中執行時附加至程式碼。 如需詳細資訊，請造訪 [VS Code 指南中的互動式調試](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines)程式。

## <a name="next-steps"></a>後續步驟

* 如需使用的完整教學課程 `ParallelRunStep` ，請參閱 [教學課程：建立批次評分的 Azure Machine Learning 管線](tutorial-pipeline-batch-scoring-classification.md)。

* 如需示範 ML 管線中自動化機器學習的完整範例，請參閱 [在 Python 的 Azure Machine Learning 管線中使用自動化 ML](how-to-use-automlstep-in-pipelines.md)。

* 請參閱 SDK 參考以取得 [azureml-管線-核心](/python/api/azureml-pipeline-core/?preserve-view=true&view=azure-ml-py) 封裝和 [azureml 管線-步驟](/python/api/azureml-pipeline-steps/?preserve-view=true&view=azure-ml-py) 套件的說明。

* 請參閱設計工具 [例外狀況和錯誤碼](algorithm-module-reference/designer-error-codes.md)的清單。