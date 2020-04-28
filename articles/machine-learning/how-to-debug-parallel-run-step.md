---
title: ParallelRunStep 的調試和疑難排解
titleSuffix: Azure Machine Learning
description: 在適用于 Python 的 Azure Machine Learning SDK 中，針對機器學習管線中的 ParallelRunStep 進行調試和疑難排解。 瞭解使用管線進行開發的常見陷阱，以及協助您在遠端執行之前和期間進行腳本的秘訣。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: ca50d70965d5edc4e31606e542ddf163fe3b0741
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76122960"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>ParallelRunStep 的調試和疑難排解
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何從[AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)進行[ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py)類別的調試和疑難排解。

## <a name="testing-scripts-locally"></a>在本機測試腳本

如需機器學習管線，請參閱在[本機測試腳本一節](how-to-debug-pipelines.md#testing-scripts-locally)。 您的 ParallelRunStep 會在 ML 管線中以步驟的方式執行，因此相同的答案也適用于兩者。

## <a name="debugging-scripts-from-remote-context"></a>從遠端內容調試腳本

從本機對計分腳本進行的轉換，到在實際管線中對計分腳本進行的程式碼，可能是一項艱難的飛躍。 如需在入口網站中尋找記錄檔的相關資訊，請查看[從遠端內容對腳本進行偵錯工具的機器學習管線一節](how-to-debug-pipelines.md#debugging-scripts-from-remote-context)。 該區段中的資訊也適用于平行步驟執行。

例如，記錄檔`70_driver_log.txt`包含從啟動平行執行步驟程式碼的控制器所提供的資訊。

因為平行執行作業的分散本質，所以有來自數個不同來源的記錄。 不過，系統會建立兩個可提供高階資訊的合併檔案：

- `~/logs/overview.txt`：此檔案提供到目前為止所建立之迷你批次（也稱為工作）數目的高階資訊，以及到目前為止已處理的迷你批次數目。 在此結束時，它會顯示作業的結果。 如果作業失敗，則會顯示錯誤訊息，以及開始進行疑難排解的位置。

- `~/logs/sys/master.txt`：此檔案會提供執行中作業的主要節點（也稱為協調器）。 包括工作建立、進度監視、執行結果。

使用 EntryScript 從輸入腳本產生的記錄，將會在下列檔案中找到：

- `~/logs/user/<ip_address>/Process-*.txt`：此檔案包含使用 EntryScript 從 entry_script 所寫入的記錄。 它也包含來自 entry_script 的 print 語句（stdout）。

當您需要完整瞭解每個節點執行分數腳本的方式時，請查看每個節點的個別進程記錄檔。 進程記錄可以在`sys/worker`資料夾中找到，依背景工作節點分組：

- `~/logs/sys/worker/<ip_address>/Process-*.txt`：此檔案會提供有關每個迷你批次的詳細資訊，因為它是由工作者所挑選或完成。 針對每個迷你批次，此檔案包含：

    - 工作者進程的 IP 位址和 PID。 
    - 專案總數、已成功處理的專案計數，以及失敗的專案計數。
    - 開始時間、持續時間、進程時間和執行方法時間。

您也可以找到每個背景工作進程的資源使用量資訊。 這項資訊是 CSV 格式，位於`~/logs/sys/perf/<ip_address>/`。 若為單一節點，則會在下`~logs/sys/perf`提供工作檔案。 例如，檢查資源使用量時，請查看下列檔案：

- `Process-*.csv`：每個工作者進程資源使用量。 
- `sys.csv`：每個節點記錄。

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>如何? 從遠端內容中的使用者腳本記錄嗎？
您可以從 EntryScript 取得記錄器，如下列範例程式碼所示，使記錄顯示在入口網站的**logs/user**資料夾中。

**使用記錄器的範例專案腳本：**
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

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>如何將端輸入（例如，包含查閱資料表的檔案或檔案）傳遞給我的所有背景工作角色？

建立包含端輸入的[資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)物件，並向您的工作區註冊。 之後，您可以在推斷腳本中存取它（例如，在 init （）方法中），如下所示：

```python
from azureml.core.run import Run
from azureml.core.dataset import Dataset

ws = Run.get_context().experiment.workspace
lookup_ds = Dataset.get_by_name(ws, "<registered-name>")
lookup_ds.download(target_path='.', overwrite=True)
```

## <a name="next-steps"></a>後續步驟

* 如需[contrib-管線步驟](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py)套件和 ParallelRunStep 類別的[檔](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py)，請參閱 SDK 參考。

* 遵循使用管線與平行執行步驟中的[advanced 教學](tutorial-pipeline-batch-scoring-classification.md)課程。
