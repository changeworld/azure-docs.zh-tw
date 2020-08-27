---
title: ParallelRunStep 的偵錯和疑難排解
titleSuffix: Azure Machine Learning
description: 在適用於 Python 的 Azure Machine Learning SDK 中對機器學習管線中的 ParallelRunStep 進行偵錯和疑難排解。 了解開發管線時的常見陷阱，以及可協助您在遠端執行之前和期間進行指令碼偵錯的秘訣。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: troubleshooting
ms.reviewer: jmartens, larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 07/16/2020
ms.openlocfilehash: 0b914059567e9a513b605ce733407465b625a909
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950012"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>ParallelRunStep 的偵錯和疑難排解
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將了解如何在 [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 中對 [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) 類別進行偵錯和疑難排解。

## <a name="testing-scripts-locally"></a>在本機測試指令碼

如需機器學習管線的相關資訊，請參閱[在本機測試指令碼](how-to-debug-pipelines.md#debug-scripts-locally)一節。 您的 ParallelRunStep 會在 ML 管線中以步驟的形式執行，因此相同的答案對這兩種情況均適用。

## <a name="debugging-scripts-from-remote-context"></a>從遠端內容偵錯指令碼

從在本機對評分指令碼進行偵錯轉換到在實際管線中對評分指令碼進行偵錯，可能是困難的一大步驟。 如需在入口網站中尋找記錄的詳細資訊，請[參閱機器學習管線一節，以了解如何從遠端內容偵錯指令碼](how-to-debug-pipelines.md#finding-and-reading-pipeline-log-files)。 該結中的資訊也適用於 ParallelRunStep。

例如，記錄檔 `70_driver_log.txt` 包含啟動 ParallelRunStep 程式碼的控制器所產生的資訊。

ParallelRunStep 作業具有分散的特性，因此會有來自數個不同來源的記錄。 不過，系統會建立兩個提供高階資訊的合併檔案：

- `~/logs/overview.txt`:此檔案會提供關於目前為止所建立的迷你批次 (也稱為工作) 數目，以及目前為止處理的迷你批次數目的高階資訊。 在這部分，會顯示作業的結果。 如果作業失敗，則會顯示錯誤訊息，以及應從何處開始進行疑難排解。

- `~/logs/sys/master.txt`：此檔案會提供主要節點 (也稱為執行中工作的 orchestrator) view。 其中包含工作建立、進度監視和執行結果。

使用 EntryScript 協助程式和 print 陳述式從輸入指令碼產生的記錄，將位於下列檔案中：

- `~/logs/user/<ip_address>/<node_name>.log.txt`：這些檔案是使用 EntryScript helper 從 entry_script 寫入的記錄。 此外也包含 entry_script 中的 print 陳述式 (stdout)。

若要概略了解指令碼中的錯誤，可以參考：

- `~/logs/user/error.txt`:此檔案會嘗試彙總指令碼中的錯誤。

如需指令碼中所含錯誤的詳細資訊，可以參考：

- `~/logs/user/error/`:包含所有擲回的錯誤和依節點組織的完整堆疊追蹤。

如果您需要完整了解每個節點執行分數指令碼的方式，請查看每個節點的個別程序記錄。 程序記錄會位於 `sys/node` 資料夾中，依背景工作節點分組：

- `~/logs/sys/node/<node_name>.txt`：此檔案會提供每個迷你批次的詳細資訊，因為它是由背景工作挑選或完成。 針對每個迷你批次，此檔案會包含：

    - 背景工作程序的 IP 位址和 PID。 
    - 項目總數、成功處理的項目計數，以及失敗的項目計數。
    - 開始時間、持續時間、處理時間和執行方法時間。

您也可以找到每個背景工作程序的資源使用量資訊。 這項資訊採用 CSV 格式，位於 `~/logs/sys/perf/overview.csv` 中。 每個處理常式的相關資訊可在下取得 `~logs/sys/processes.csv` 。

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

建立包含參考資料的 [資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) ，並向您的工作區註冊該資料集。 將其傳至您 `ParallelRunStep` 的 `side_inputs` 參數。 此外，您可以在區段中新增其路徑， `arguments` 以輕鬆存取其掛接的路徑：

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

## <a name="next-steps"></a>後續步驟

* 請參閱 SDK 參考以取得 [azureml 管線-步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) 套件的說明。 ParallelRunStep 類別的視圖參考 [檔](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep?view=azure-ml-py) 。

* 遵循搭配 ParallelRunStep 使用管線的 [advanced 教學](tutorial-pipeline-batch-scoring-classification.md) 課程。 本教學課程會示範如何將另一個檔案作為端輸入傳遞。 
