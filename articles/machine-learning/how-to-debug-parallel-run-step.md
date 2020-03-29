---
title: 調試和排除並行運行步驟
titleSuffix: Azure Machine Learning
description: 在適用于 Python 的 Azure 機器學習 SDK 中的機器學習管道中調試和排除並行運行步驟。 瞭解使用管道開發的常見陷阱，以及説明您在遠端執行之前和期間調試腳本的提示。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: ca50d70965d5edc4e31606e542ddf163fe3b0741
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122960"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>調試和排除並行運行步驟
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何從[Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)調試和排除[ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py)類。

## <a name="testing-scripts-locally"></a>在本地測試腳本

有關機器學習管道，請參閱[本地測試腳本部分](how-to-debug-pipelines.md#testing-scripts-locally)。 並行 RunStep 作為 ML 管道中的一個步驟運行，因此相同的答案適用于兩者。

## <a name="debugging-scripts-from-remote-context"></a>從遠端上下文調試腳本

從本地調試評分腳本到在實際管道中調試評分腳本的過渡可能是一個艱難的飛躍。 有關在門戶中查找日誌的資訊，[機器學習管道部分將介紹從遠端上下文中調試腳本](how-to-debug-pipelines.md#debugging-scripts-from-remote-context)。 該部分中的資訊也適用于並行步驟運行。

例如，日誌檔`70_driver_log.txt`包含來自啟動並行運行步驟代碼的控制器的資訊。

由於並行運行作業的分散式特性，因此存在來自多個不同源的日誌。 但是，將創建兩個提供高級資訊的合併檔：

- `~/logs/overview.txt`：此檔提供有關到目前為止創建的微型批次處理（也稱為任務）數以及到目前為止處理的小型批次處理數的高級資訊。 在此，它顯示作業的結果。 如果作業失敗，它將顯示錯誤訊息以及從何處開始故障排除。

- `~/logs/sys/master.txt`：此檔提供正在運行的作業的主節點（也稱為協調器）視圖。 包括任務創建、進度監視、運行結果。

使用 EntryScript.logger 和列印語句從條目腳本生成的日誌將在以下檔中找到：

- `~/logs/user/<ip_address>/Process-*.txt`：此檔包含使用 entryScript.logger 從 entry_script編寫的日誌。 它還包含entry_script的列印語句（stdout）。

當您需要充分瞭解每個節點執行評分腳本的方式時，請查看每個節點的單個進程日誌。 進程日誌可以在`sys/worker`資料夾中找到，按輔助節點分組：

- `~/logs/sys/worker/<ip_address>/Process-*.txt`：此檔提供有關每個微型批次處理的詳細資訊，因為每個小批量是由工作人員拾取或完成的。 對於每個微型批次處理，此檔包括：

    - 輔助進程的 IP 位址和 PID。 
    - 專案總數、成功處理的專案計數和失敗項計數。
    - 開始時間、持續時間、處理時間和運行方法時間。

您還可以找到有關每個工作人員進程的資源使用方式的資訊。 此資訊採用 CSV 格式，位於`~/logs/sys/perf/<ip_address>/`。 對於單個節點，作業檔將在 下`~logs/sys/perf`可用。 例如，在檢查資源利用率時，請查看以下檔：

- `Process-*.csv`：每個工作人員處理資源使用方式。 
- `sys.csv`：每個節點日誌。

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>如何從遠端上下文從使用者腳本中登錄？
您可以從 EntryScript 獲取記錄器，如下示例代碼所示，以使日誌顯示在門戶中的**日誌/使用者**資料夾中。

**使用記錄器的示例條目腳本：**
```python
from entry_script import EntryScript

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

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>如何將包含查閱資料表的檔或檔等側輸入傳遞給所有工作人員？

構造包含側輸入的[資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)物件，並將其註冊到工作區。 之後，您可以在推理腳本中訪問它（例如，在 init（） 方法中，如下所示：

```python
from azureml.core.run import Run
from azureml.core.dataset import Dataset

ws = Run.get_context().experiment.workspace
lookup_ds = Dataset.get_by_name(ws, "<registered-name>")
lookup_ds.download(target_path='.', overwrite=True)
```

## <a name="next-steps"></a>後續步驟

* 有關[azureml-contrib-管道步驟](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py)包的説明以及 ParallelRunStep 類[的文檔](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py)，請參閱 SDK 參考。

* 請按照有關使用具有並行運行步驟的管道[的高級教程](tutorial-pipeline-batch-scoring-classification.md)。
