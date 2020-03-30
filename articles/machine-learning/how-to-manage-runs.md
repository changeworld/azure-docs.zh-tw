---
title: 在 Python 中啟動、監視和取消訓練運行
titleSuffix: Azure Machine Learning
description: 瞭解如何開始、設置狀態、標記和組織機器學習實驗。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 01/09/2020
ms.openlocfilehash: 8c261a010a1e8f4d1be9b3883510eb38c37a15ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296893"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>在 Python 中啟動、監視和取消訓練運行
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

[用於 Python、](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)機器學習[CLI](reference-azure-machine-learning-cli.md)和 Azure[機器學習工作室](https://ml.azure.com)的 Azure 機器學習 SDK 提供了各種方法來監視、組織和管理運行以進行培訓和實驗。

本文顯示了以下任務的示例：

* 監視運行性能。
* 取消或失敗運行。
* 創建子運行。
* 標記並查找運行。

## <a name="prerequisites"></a>Prerequisites

您需要以下專案：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* [Azure 機器學習工作區](how-to-manage-workspace.md)。

* 適用于 Python 的 Azure 機器學習 SDK（版本 1.0.21 或更高版本）。 要安裝或更新到最新版本的 SDK，請參閱[安裝或更新 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

    要檢查 Azure 機器學習 SDK 的版本，請使用以下代碼：

    ```python
    print(azureml.core.VERSION)
    ```

* Azure 機器學習的[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)和[CLI 擴展。](reference-azure-machine-learning-cli.md)

## <a name="start-a-run-and-its-logging-process"></a>啟動運行及其日誌記錄過程

### <a name="using-the-sdk"></a>使用 SDK

通過從[azureml.core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py)包導入[工作區](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)、[實驗](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)、[運行](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)和[腳本Runfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py)類來設置實驗。

```python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

使用[`start_logging()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-)方法啟動運行及其日誌記錄過程。

```python
notebook_run = exp.start_logging()
notebook_run.log(name="message", value="Hello from run!")
```

### <a name="using-the-cli"></a>使用 CLI

要開始運行實驗，請使用以下步驟：

1. 從 shell 或命令提示符中，使用 Azure CLI 對 Azure 訂閱進行身份驗證：

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

1. 將工作區配置附加到包含訓練腳本的資料夾。 替換為`myworkspace`Azure 機器學習工作區。 替換為`myresourcegroup`包含工作區的 Azure 資源組：

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    此命令創建一`.azureml`個子目錄，其中包含示例 runconfig 和 conda 環境檔。 它還包含用於與`config.json`Azure 機器學習工作區通信的檔。

    有關詳細資訊，請參閱[az ml 資料夾附加](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)。

2. 要啟動運行，請使用以下命令。 使用此命令時，請針對 -c 參數指定 runconfig 檔\*的名稱（如果查看檔案系統，則 .runconfig 之前的文本）。

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > 該`az ml folder attach`命令創建了一`.azureml`個子目錄，其中包含兩個示例 runconfig 檔。
    >
    > 如果您有一個以程式設計方式創建回合組態物件的 Python 腳本，則可以使用[RunConfig.save（）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-)將其另存為 runconfig 檔。
    >
    > 有關更多示例回合組態檔，請參閱[https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml)。

    有關詳細資訊，請參閱[az ml 運行提交腳本](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)。

### <a name="using-azure-machine-learning-studio"></a>使用 Azure 機器學習工作室

要啟動在設計器（預覽）中運行的提交管道，請使用以下步驟：

1. 為管道設置預設計算目標。

1. 選擇"在管道畫布頂部**運行**"。

1. 選擇一個實驗來對管道運行進行分組。

## <a name="monitor-the-status-of-a-run"></a>監視運行狀態

### <a name="using-the-sdk"></a>使用 SDK

使用[`get_status()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--)方法獲取運行的狀態。

```python
print(notebook_run.get_status())
```

要獲取運行 ID、執行時間和有關運行的其他詳細資訊，請使用 方法[`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--)。

```python
print(notebook_run.get_details())
```

運行成功完成後，請使用 方法[`complete()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-)將其標記為已完成。

```python
notebook_run.complete()
print(notebook_run.get_status())
```

如果使用 Python`with...as`的設計模式，則當運行範圍外時，運行將自動將自已標幟為已完成。 您無需手動將運行標記為已完成。

```python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print(notebook_run.get_status())

print(notebook_run.get_status())
```

### <a name="using-the-cli"></a>使用 CLI

1. 要查看實驗的運行清單，請使用以下命令。 替換為`experiment`實驗的名稱：

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    此命令返回一個 JSON 文檔，該文檔列出有關此實驗的運行的資訊。

    有關詳細資訊，請參閱[az ml 實驗清單](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)。

2. 要查看特定運行的資訊，請使用以下命令。 替換為`runid`運行的 ID：

    ```azurecli-interactive
    az ml run show -r runid
    ```

    此命令返回一個 JSON 文檔，該文檔列出有關運行的資訊。

    有關詳細資訊，請參閱[az ml 運行顯示](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show)。


### <a name="using-azure-machine-learning-studio"></a>使用 Azure 機器學習工作室

在工作室中查看實驗的活動運行數。

1. 導航到 **"實驗"** 部分。 

1. 選擇實驗。

    在實驗頁中，您可以看到活動計算目標的數量和每次運行的持續時間。 

1. 選擇特定的運行編號。

1. 在 **"日誌"** 選項卡中，可以找到管道運行的診斷和錯誤日誌。


## <a name="cancel-or-fail-runs"></a>取消或失敗運行

如果您發現錯誤，或者跑步時間過長，可以取消運行。

### <a name="using-the-sdk"></a>使用 SDK

要使用 SDK 取消運行，請使用[`cancel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--)以下方法：

```python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_script_run = exp.submit(run_config)
print(local_script_run.get_status())

local_script_run.cancel()
print(local_script_run.get_status())
```

如果運行完成，但它包含錯誤（例如，使用了不正確的訓練腳本），則可以使用[`fail()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)#fail-error-details-none--error-code-none---set-status-true-)方法將其標記為失敗。

```python
local_script_run = exp.submit(run_config)
local_script_run.fail()
print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>使用 CLI

要使用 CLI 取消運行，請使用以下命令。 替換為`runid`運行的 ID

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

有關詳細資訊，請參閱[az ml 運行取消](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel)。

### <a name="using-azure-machine-learning-studio"></a>使用 Azure 機器學習工作室

要取消演播室中的運行，請使用以下步驟：

1. 轉到 **"實驗**"或"管道"部分中的正在運行的**管道**。 

1. 選擇要取消的管道運行編號。

1. 在工具列中，選擇 **"取消"**


## <a name="create-child-runs"></a>創建子運行

創建子運行以將相關運行組合在一起，例如對於不同的超參數調整反覆運算。

> [!NOTE]
> 只能使用 SDK 創建子運行。

此代碼示例使用`hello_with_children.py`腳本使用[`child_run()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-)方法從提交的運行中創建一批五個子運行：

```python
!more hello_with_children.py
run_config = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_script_run = exp.submit(run_config)
local_script_run.wait_for_completion(show_output=True)
print(local_script_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> 當子運行移出範圍時，子運行將自動標記為已完成。

要有效地創建許多子級運行，請使用[`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-)方法。 因為每次創建都會產生一個網路調用，因此創建一批運行比逐個創建運行更有效。

### <a name="submit-child-runs"></a>提交子運行

子運行也可以從父運行提交。 這允許您創建父級和子運行層次結構，每個層次結構都在不同的計算目標上運行，由公共父運行 ID 連接。

使用["submit_child（）"](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#submit-child-config--tags-none----kwargs-)方法從父運行中提交子運行。 要在父運行腳本中執行此操作，請使用上下文實例``submit_child``的方法獲取運行上下文並提交子運行。

```python
## In parent run script
parent_run = Run.get_context()
child_run_config = ScriptRunConfig(source_directory='.', script='child_script.py')
parent_run.submit_child(child_run_config)
```

在子運行中，您可以查看父運行 ID：

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>查詢子級運行

要查詢特定父級的子運行，請使用 方法[`get_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-)。 該``recursive = True``參數允許您查詢子孫的嵌套樹。

```python
print(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>標記和查找運行

在 Azure 機器學習中，可以使用屬性和標記來説明組織和查詢運行以獲取有關重要資訊。

### <a name="add-properties-and-tags"></a>添加屬性和標記

#### <a name="using-the-sdk"></a>使用 SDK

要將可搜索中繼資料添加到運行中，請使用[`add_properties()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-)方法。 例如，以下代碼將`"author"`屬性添加到運行中：

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

屬性是不可改變的，因此它們會創建用於審核的永久記錄。 以下代碼示例會導致錯誤，因為我們已經在前面的代碼中添加`"azureml-user"`為`"author"`屬性值：

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

與屬性不同，標記是可變的。 要為實驗的消費者添加可搜索且有意義的資訊，請使用 方法[`tag()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-)。

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

您還可以添加簡單的字串標記。 當這些標記作為鍵顯示在標記字典中時，它們的值為`None`。

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>使用 CLI

> [!NOTE]
> 使用 CLI，您只能添加或更新標記。

要添加或更新標記，請使用以下命令：

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

有關詳細資訊，請參閱[az ml 運行更新](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update)。

### <a name="query-properties-and-tags"></a>查詢屬性和標記

可以在實驗中查詢運行，以返回與特定屬性和標記匹配的運行清單。

#### <a name="using-the-sdk"></a>使用 SDK

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>使用 CLI

Azure CLI 支援[JMESPath](http://jmespath.org)查詢，可用於根據屬性和標記篩選運行。 要將 JMESPath 查詢與 Azure CLI 一起`--query`使用，請使用 參數指定它。 以下示例顯示了使用屬性和標記的基本查詢：

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

有關查詢 Azure CLI 結果的詳細資訊，請參閱[查詢 Azure CLI 命令輸出](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest)。

### <a name="using-azure-machine-learning-studio"></a>使用 Azure 機器學習工作室

1. 導航到**管道**部分。

1. 使用搜索欄使用標記、說明、實驗名稱和提交者名稱篩選管道。

## <a name="example-notebooks"></a>Notebook 範例

以下筆記本演示了本文中的概念：

* 要瞭解有關日誌記錄 API 的更多資訊，請參閱日誌記錄[API 筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb)。

* 有關使用 Azure 機器學習 SDK 管理運行的詳細資訊，請參閱[管理運行筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb)。

## <a name="next-steps"></a>後續步驟

* 要瞭解如何記錄實驗指標，請參閱[在訓練運行期間記錄指標](how-to-track-experiments.md)。
* 要瞭解如何監視 Azure 機器學習的資源和日誌，請參閱監視[Azure 機器學習](monitor-azure-machine-learning.md)。