---
title: 在 Python 中啟動、監視及取消定型回合
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning Python SDK 來啟動、狀態及管理您的機器學習實驗執行。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 01/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 0da4127960450a13b64ec23908b4a4fd4c69bd7e
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542009"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>在 Python 中啟動、監視及取消定型回合

適用于 Python、 [MACHINE LEARNING CLI](reference-azure-machine-learning-cli.md)和[Azure Machine Learning STUDIO](https://ml.azure.com)的[Azure Machine Learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)提供各種方法來監視、組織及管理您的回合以進行定型和實驗。

本文說明下列工作的範例：

* 監視執行效能。
* 執行取消或失敗。
* 建立子執行。
* 標記和尋找執行。

> [!TIP]
> 如果您要尋找有關監視 Azure Machine Learning 服務和相關 Azure 服務的資訊，請參閱 [如何監視 Azure Machine Learning](monitor-azure-machine-learning.md)。
> 如果您要尋找部署為 web 服務或 IoT Edge 模組之模型的相關資訊，請參閱[使用 Application Insights](how-to-enable-app-insights.md)[收集模型資料](how-to-enable-data-collection.md)和監視。

## <a name="prerequisites"></a>必要條件

您將需要下列專案：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* [Azure Machine Learning 工作區](how-to-manage-workspace.md)。

* 適用于 Python 的 Azure Machine Learning SDK (1.0.21 版或更新版本的) 。 若要安裝或更新至最新版本的 SDK，請參閱 [安裝或更新 sdk](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)。

    若要檢查您的 Azure Machine Learning SDK 版本，請使用下列程式碼：

    ```python
    print(azureml.core.VERSION)
    ```

* [適用于 Azure Machine Learning](reference-azure-machine-learning-cli.md)的[Azure CLI](/cli/azure/?preserve-view=true&view=azure-cli-latest)和 CLI 擴充功能。

## <a name="monitor-run-performance"></a>監視執行效能

* 開始執行和其記錄處理常式

    # <a name="python"></a>[Python](#tab/python)
    
    1. 從[azureml](/python/api/azureml-core/azureml.core?preserve-view=true&view=azure-ml-py)套件匯入[工作區](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py)、[實驗](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py)、[執行](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py)及[ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py)類別，以設定您的實驗。
    
        ```python
        import azureml.core
        from azureml.core import Workspace, Experiment, Run
        from azureml.core import ScriptRunConfig
        
        ws = Workspace.from_config()
        exp = Experiment(workspace=ws, name="explore-runs")
        ```
    
    1. 使用方法啟動執行和其記錄處理常式 [`start_logging()`](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truestart-logging--args----kwargs-) 。
    
        ```python
        notebook_run = exp.start_logging()
        notebook_run.log(name="message", value="Hello from run!")
        ```
        
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    若要開始執行您的實驗，請使用下列步驟：
    
    1. 從 shell 或命令提示字元中，使用 Azure CLI 來驗證您的 Azure 訂用帳戶：
    
        ```azurecli-interactive
        az login
        ```
        
        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 
    
    1. 將工作區設定附加至包含訓練腳本的資料夾。 取代 `myworkspace` 為您的 Azure Machine Learning 工作區。 取代 `myresourcegroup` 為包含您工作區的 Azure 資源群組：
    
        ```azurecli-interactive
        az ml folder attach -w myworkspace -g myresourcegroup
        ```
    
        此命令會建立一個 `.azureml` 子目錄，其中包含範例 runconfig 和 conda 環境檔案。 它也包含用來與 Azure Machine Learning 工作區通訊的 `config.json` 檔案。
    
        如需詳細資訊，請參閱 [az ml folder attach](/cli/azure/ext/azure-cli-ml/ml/folder?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)。
    
    2. 若要開始執行，請使用下列命令。 使用此命令時，請對應 -c 參數指定 runconfig 檔案的名稱 (如果您是在檔案系統中查看，則為 \*.runconfig 前面的文字)。
    
        ```azurecli-interactive
        az ml run submit-script -c sklearn -e testexperiment train.py
        ```
    
        > [!TIP]
        > 此 `az ml folder attach` 命令會建立一個 `.azureml` 子目錄，其中包含兩個範例 runconfig 檔案。
        >
        > 如果您有使用程式設計方式建立執行設定物件的 Python 指令碼，則可以使用 [RunConfig.save()](/python/api/azureml-core/azureml.core.runconfiguration?preserve-view=true&view=azure-ml-py#&preserve-view=truesave-path-none--name-none--separate-environment-yaml-false-) 以將其儲存為 runconfig 檔案。
        >
        > 如需 runconfig 檔的詳細資訊，請參閱 [https://github.com/MicrosoftDocs/pipelines-azureml/](https://github.com/MicrosoftDocs/pipelines-azureml/) 。
    
        如需詳細資訊，請參閱 [az ml run submit-script](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)。
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    若要在設計工具中開始提交管線執行，請使用下列步驟：
    
    1. 為您的管線設定預設計算目標。
    
    1. 選取管線畫布頂端的 [ **執行** ]。
    
    1. 選取用來將管線執行分組的實驗。
    
    ---

* 監視執行的狀態

    # <a name="python"></a>[Python](#tab/python)
    
    * 使用方法取得執行的狀態 [`get_status()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-status--) 。
    
        ```python
        print(notebook_run.get_status())
        ```
    
    * 若要取得執行識別碼、執行時間，以及有關執行的其他詳細資料，請使用 [`get_details()`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-details--) 方法。
    
        ```python
        print(notebook_run.get_details())
        ```
    
    * 當您的執行成功完成時，請使用 [`complete()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecomplete--set-status-true-) 方法將它標示為已完成。
    
        ```python
        notebook_run.complete()
        print(notebook_run.get_status())
        ```
    
    * 如果您使用 Python 的 `with...as` 設計模式，執行會在執行超出範圍時，自動將本身標示為已完成。 您不需要手動將執行標示為已完成。
        
        ```python
        with exp.start_logging() as notebook_run:
            notebook_run.log(name="message", value="Hello from run!")
            print(notebook_run.get_status())
        
        print(notebook_run.get_status())
        ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    * 若要查看實驗的執行清單，請使用下列命令。 `experiment`以您的實驗名稱取代：
    
        ```azurecli-interactive
        az ml run list --experiment-name experiment
        ```
    
        此命令會傳回 JSON 檔，其中列出此實驗執行的相關資訊。
    
        如需詳細資訊，請參閱 [az ml experiment list](/cli/azure/ext/azure-cli-ml/ml/experiment?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)。
    
    * 若要查看特定執行的相關資訊，請使用下列命令。 取代 `runid` 為執行的識別碼：
    
        ```azurecli-interactive
        az ml run show -r runid
        ```
    
        此命令會傳回 JSON 檔，其中列出執行的相關資訊。
    
        如需詳細資訊，請參閱 [az ml run show](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show)。
    
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    若要在 studio 中查看實驗的作用中執行數目。
    
    1. 流覽至 [ **實驗** ] 區段。
    
    1. 選取實驗。
    
        在 [實驗] 頁面中，您可以看到使用中的計算目標數目，以及每次執行的持續時間。 
    
    1. 選取 [執行] 來比較、新增圖表或套用篩選，以對實驗進行自訂。 這些變更可以另存為 **自訂視圖** ，讓您可以輕鬆地返回您的工作。 具有工作區許可權的使用者可以編輯或查看自訂視圖。 此外，您可以在瀏覽器中複製並貼上 URL，以與其他人共用自訂視圖。  
    
        :::image type="content" source="media/how-to-manage-runs/custom-views.gif" alt-text="螢幕擷取畫面：建立自訂視圖":::
    
    1. 選取特定的執行編號。
    
    1. 在 [ **記錄** 檔] 索引標籤中，您可以找到管線執行的診斷和錯誤記錄檔。
    
    ---
    
## <a name="cancel-or-fail-runs"></a>取消或失敗的執行

如果您發現錯誤，或您的執行時間太長而無法完成，您可以取消執行。

# <a name="python"></a>[Python](#tab/python)

若要使用 SDK 取消執行，請使用 [`cancel()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecancel--) 方法：

```python
src = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_run = exp.submit(src)
print(local_run.get_status())

local_run.cancel()
print(local_run.get_status())
```

如果您的執行已完成，但它包含錯誤 (例如，使用了不正確的定型腳本) ，您可以使用 [`fail()`](/python/api/azureml-core/azureml.core.run%28class%29#fail-error-details-none--error-code-none---set-status-true-) 方法將它標示為失敗。

```python
local_run = exp.submit(src)
local_run.fail()
print(local_run.get_status())
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 CLI 取消執行，請使用下列命令。 取代 `runid` 為執行的識別碼

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

如需詳細資訊，請參閱 [az ml run cancel](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel)。

# <a name="studio"></a>[Studio](#tab/azure-studio)

若要取消在 studio 中執行，請使用下列步驟：

1. 在 [ **實驗** ] 或 [ **管線** ] 區段中，移至正在執行的管線。 

1. 選取您要取消的管線執行編號。

1. 在工具列中選取 [ **取消** ]

---

## <a name="create-child-runs"></a>建立子執行

建立子回合以將相關的執行群組在一起，例如用於不同的超參數微調反復專案。

> [!NOTE]
> 您只能使用 SDK 來建立子執行。

這個程式碼範例會使用下列方法，在提交的回合 `hello_with_children.py` 內使用腳本建立五個子回合的批次 [`child_run()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truechild-run-name-none--run-id-none--outputs-none-) ：

```python
!more hello_with_children.py
src = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_run = exp.submit(src)
local_run.wait_for_completion(show_output=True)
print(local_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> 當它們移出範圍時，子執行會自動標示為已完成。

若要有效率地建立許多子系執行，請使用 [`create_children()`](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-children-count-none--tag-key-none--tag-values-none-) 方法。 由於每個建立都會導致網路呼叫，建立批次的執行比逐一建立執行更有效率。

### <a name="submit-child-runs"></a>提交子執行

您也可以從父代執行提交子執行。 這可讓您建立父系和子執行的階層。 

您可能會想要讓子系執行的執行設定與父執行不同。 例如，您可能會針對父系使用較不強大、以 CPU 為基礎的設定，同時針對您的子女使用 GPU 型設定。 另一種常見的需求是傳遞每個子系不同的引數和資料。 若要自訂子執行，請建立 `ScriptRunConfig` 子執行的物件。 下列程式碼會執行下列動作：

- 從工作區中取出名為的計算資源 `"gpu-cluster"``ws`
- 反覆運算不同的引數值，以傳遞給子 `ScriptRunConfig` 物件
- 使用自訂計算資源和引數，建立並提交新的子執行
- 封鎖直到所有子系執行完成為止

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig

compute_target = ws.compute_targets["gpu-cluster"]

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], compute_target=compute_target)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

若要有效率地建立具有相同設定、引數和輸入的許多子系執行，請使用 [`create_children()`](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-children-count-none--tag-key-none--tag-values-none-) 方法。 由於每個建立都會導致網路呼叫，建立批次的執行比逐一建立執行更有效率。

在子系執行中，您可以查看父執行識別碼：

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>查詢子執行

若要查詢特定父系的子執行，請使用 [`get_children()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) 方法。 ``recursive = True``引數可讓您查詢子系和孫系的嵌套樹狀目錄。

```python
print(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>標記和尋找執行

在 Azure Machine Learning 中，您可以使用屬性和標籤來協助組織和查詢執行中的重要資訊。

* 新增屬性和標記

    # <a name="python"></a>[Python](#tab/python)
    
    若要將可搜尋的中繼資料新增至您的執行，請使用 [`add_properties()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueadd-properties-properties-) 方法。 例如，下列程式碼會將 `"author"` 屬性新增至執行：
    
    ```Python
    local_run.add_properties({"author":"azureml-user"})
    print(local_run.get_properties())
    ```
    
    屬性是不可變的，因此它們會建立用於進行審核的永久記錄。 下列程式碼範例會產生錯誤，因為我們已 `"azureml-user"` `"author"` 在上述程式碼中新增為屬性值：
    
    ```Python
    try:
        local_run.add_properties({"author":"different-user"})
    except Exception as e:
        print(e)
    ```
    
    不同于屬性，標記是可變動的。 若要為您實驗的取用者新增可搜尋且有意義的資訊，請使用 [`tag()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truetag-key--value-none-) 方法。
    
    ```Python
    local_run.tag("quality", "great run")
    print(local_run.get_tags())
    
    local_run.tag("quality", "fantastic run")
    print(local_run.get_tags())
    ```
    
    您也可以加入簡單的字串標記。 當這些標記以索引鍵的形式出現在標記字典中時，它們的值為 `None` 。
    
    ```Python
    local_run.tag("worth another look")
    print(local_run.get_tags())
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    > [!NOTE]
    > 使用 CLI，您只能新增或更新標記。
    
    若要新增或更新標記，請使用下列命令：
    
    ```azurecli-interactive
    az ml run update -r runid --add-tag quality='fantastic run'
    ```
    
    如需詳細資訊，請參閱 [az ml run update](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update)。
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    您可以在 studio 中查看屬性和標籤，但無法在該處修改它們。
    
    ---

* 查詢屬性和標記

    您可以查詢實驗內的執行，以傳回符合特定屬性和標記的執行清單。

    # <a name="python"></a>[Python](#tab/python)
    
    ```Python
    list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
    list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    Azure CLI 支援 [JMESPath](http://jmespath.org) 查詢，可用來根據屬性和標記篩選執行。 若要使用 JMESPath 查詢搭配 Azure CLI，請使用參數來指定 `--query` 。 下列範例顯示使用屬性和標記的一些查詢：
    
    ```azurecli-interactive
    # list runs where the author property = 'azureml-user'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user']
    # list runs where the tag contains a key that starts with 'worth another look'
    az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
    # list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
    ```
    
    如需查詢 Azure CLI 結果的詳細資訊，請參閱 [查詢 Azure CLI 命令輸出](/cli/azure/query-azure-cli?preserve-view=true&view=azure-cli-latest)。
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    1. 流覽至 [ **管線** ] 區段。
    
    1. 使用搜尋列，利用標記、描述、實驗名稱和提交者名稱來篩選管線。
    
    ---

## <a name="example-notebooks"></a>Notebook 範例

下列筆記本示範本文中的概念：

* 若要深入瞭解記錄 Api，請參閱 [記錄 api 筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb)。

* 如需使用 Azure Machine Learning SDK 來管理執行的詳細資訊，請參閱 [管理執行筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb)。

## <a name="next-steps"></a>後續步驟

* 若要瞭解如何記錄您的實驗的計量，請參閱 [定型回合期間的記錄計量](how-to-track-experiments.md)。
* 若要瞭解如何監視 Azure Machine Learning 的資源和記錄，請參閱 [監視 Azure Machine Learning](monitor-azure-machine-learning.md)。