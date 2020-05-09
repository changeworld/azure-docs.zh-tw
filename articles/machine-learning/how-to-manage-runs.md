---
title: 在 Python 中啟動、監視和取消定型執行
titleSuffix: Azure Machine Learning
description: 瞭解如何開始、設定狀態、標記和組織您的機器學習實驗。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 01/09/2020
ms.openlocfilehash: d6dc2eeb572eeed17281677945c93067bbadee94
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628566"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>在 Python 中啟動、監視和取消定型執行
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

[適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)、 [Machine Learning CLI](reference-azure-machine-learning-cli.md)和[Azure Machine Learning studio](https://ml.azure.com)提供各種方法來監視、組織及管理您的回合，以進行訓練和實驗。

本文說明下列工作的範例：

* 監視執行效能。
* [取消] 或 [失敗] 執行。
* 建立子執行。
* 標記和尋找執行。

## <a name="prerequisites"></a>Prerequisites

您將需要下列專案：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* [Azure Machine Learning 工作區](how-to-manage-workspace.md)。

* 適用于 Python 的 Azure Machine Learning SDK （1.0.21 版或更新版本）。 若要安裝或更新為最新版本的 SDK，請參閱[安裝或更新 sdk](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

    若要檢查您的 Azure Machine Learning SDK 版本，請使用下列程式碼：

    ```python
    print(azureml.core.VERSION)
    ```

* Azure Machine Learning 的[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)和[CLI 擴充](reference-azure-machine-learning-cli.md)功能。

## <a name="start-a-run-and-its-logging-process"></a>開始執行和其記錄程式

### <a name="using-the-sdk"></a>使用 SDK

從[azureml](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py)套件匯入[工作區](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)、[實驗](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)、[執行](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)和[ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py)類別，以設定您的實驗。

```python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

使用[`start_logging()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-)方法來啟動執行和其記錄處理常式。

```python
notebook_run = exp.start_logging()
notebook_run.log(name="message", value="Hello from run!")
```

### <a name="using-the-cli"></a>使用 CLI

若要開始執行您的實驗，請使用下列步驟：

1. 從 shell 或命令提示字元，使用 Azure CLI 向您的 Azure 訂用帳戶進行驗證：

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

1. 將工作區設定附加至包含定型腳本的資料夾。 將`myworkspace`取代為您的 Azure Machine Learning 工作區。 將`myresourcegroup`取代為包含您工作區的 Azure 資源群組：

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    此命令會建立`.azureml`一個子目錄，其中包含範例 runconfig 和 conda 環境檔案。 它也包含`config.json`用來與您的 Azure Machine Learning 工作區通訊的檔案。

    如需詳細資訊，請參閱[az ml folder attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)。

2. 若要開始執行，請使用下列命令。 使用此命令時，請指定 runconfig 檔案的名稱（如果您要查看\*檔案系統，則為 runconfig），並針對-c 參數。

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach`命令已建立一個`.azureml`子目錄，其中包含兩個範例 runconfig 檔案。
    >
    > 如果您有以程式設計方式建立回合設定物件的 Python 腳本，您可以使用[RunConfig （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-)將它儲存為 RunConfig 檔案。
    >
    > 如需更多範例 runconfig 檔案[https://github.com/MicrosoftDocs/pipelines-azureml/](https://github.com/MicrosoftDocs/pipelines-azureml/)，請參閱。

    如需詳細資訊，請參閱[az ml 執行提交-腳本](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)。

### <a name="using-azure-machine-learning-studio"></a>使用 Azure Machine Learning studio

若要開始在設計工具（預覽）中提交管線執行，請使用下列步驟：

1. 為您的管線設定預設計算目標。

1. 選取管線畫布頂端的 [**執行**]。

1. 選取要分組管線執行的實驗。

## <a name="monitor-the-status-of-a-run"></a>監視執行的狀態

### <a name="using-the-sdk"></a>使用 SDK

使用[`get_status()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--)方法取得執行的狀態。

```python
print(notebook_run.get_status())
```

若要取得回合識別碼、執行時間，以及有關執行的其他詳細資料，請[`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--)使用方法。

```python
print(notebook_run.get_details())
```

當您的[`complete()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-)回合成功完成時，請使用方法將其標示為已完成。

```python
notebook_run.complete()
print(notebook_run.get_status())
```

如果您使用 Python 的`with...as`設計模式，執行會在執行超出範圍時，自動將其本身標示為已完成。 您不需要手動將執行標示為已完成。

```python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print(notebook_run.get_status())

print(notebook_run.get_status())
```

### <a name="using-the-cli"></a>使用 CLI

1. 若要查看實驗的執行清單，請使用下列命令。 將`experiment`取代為您的實驗名稱：

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    此命令會傳回 JSON 檔，其中列出此實驗執行的相關資訊。

    如需詳細資訊，請參閱[az ml 實驗清單](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)。

2. 若要查看特定執行的資訊，請使用下列命令。 以`runid`執行的識別碼取代：

    ```azurecli-interactive
    az ml run show -r runid
    ```

    此命令會傳回 JSON 檔，其中列出執行的相關資訊。

    如需詳細資訊，請參閱[az ml run show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show)。


### <a name="using-azure-machine-learning-studio"></a>使用 Azure Machine Learning studio

在 studio 中，為您的實驗查看使用中的執行數目。

1. 流覽至**實驗**區段。 

1. 選取實驗。

    在 [實驗] 頁面中，您可以看到使用中計算目標的數目，以及每次執行的持續時間。 

1. 選取特定的執行編號。

1. 在 [**記錄**檔] 索引標籤中，您可以找到管線執行的診斷和錯誤記錄檔。


## <a name="cancel-or-fail-runs"></a>取消或失敗的執行

如果您注意到錯誤，或是您的執行時間太長而無法完成，您可以取消執行。

### <a name="using-the-sdk"></a>使用 SDK

若要使用 SDK 取消執行，請使用[`cancel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--)方法：

```python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_script_run = exp.submit(run_config)
print(local_script_run.get_status())

local_script_run.cancel()
print(local_script_run.get_status())
```

如果您的執行已完成，但它包含錯誤（例如，使用了不正確的定型腳本），您可以使用[`fail()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)#fail-error-details-none--error-code-none---set-status-true-)方法將其標示為失敗。

```python
local_script_run = exp.submit(run_config)
local_script_run.fail()
print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>使用 CLI

若要使用 CLI 取消執行，請使用下列命令。 取代`runid`為執行的識別碼

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

如需詳細資訊，請參閱[az ml run cancel](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel)。

### <a name="using-azure-machine-learning-studio"></a>使用 Azure Machine Learning studio

若要在 studio 中取消執行，請使用下列步驟：

1. 在 [**實驗**] 或 [**管線**] 區段中，移至執行中的管線。 

1. 選取您想要取消的管線執行編號。

1. 在工具列中，選取 [**取消**]


## <a name="create-child-runs"></a>建立子執行

建立子回合以將相關的執行群組在一起，例如用於不同的超參數微調反復專案。

> [!NOTE]
> 只能使用 SDK 來建立子系執行。

此程式碼範例會`hello_with_children.py`使用腳本，從已提交的執行中，使用[`child_run()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-)方法來建立五個子回合的批次：

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
> 當它們移出範圍時，子回合會自動標示為已完成。

若要有效率地建立多個子執行， [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-)請使用方法。 因為每個建立都會導致網路呼叫，所以建立一批回合比逐一建立執行更有效率。

### <a name="submit-child-runs"></a>提交子回合

子系執行也可以從父執行提交。 這可讓您建立父系和子系執行的階層。 

您可能想要讓子系執行使用與父執行不同的回合設定。 例如，您可以針對父系使用較不強大的 CPU 型設定，同時針對您的子系使用 GPU 型設定。 另一個常見的需求是傳遞每個子系不同的引數和資料。 若要自訂子回合，請`RunConfiguration`將物件傳遞至子`ScriptRunConfig`系的函式。 這個程式碼範例是父`ScriptRunConfig`物件腳本的一部分：

- 建立`RunConfiguration`正在抓取已命名計算資源的`"gpu-compute"`
- 逐一查看要傳遞至子`ScriptRunConfig`物件的不同引數值
- 使用自訂計算資源和引數，建立並提交新的子系執行
- 封鎖直到所有子系執行完成

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig, RunConfiguration

run_config_for_aml_compute = RunConfiguration()
run_config_for_aml_compute.target = "gpu-compute"
run_config_for_aml_compute.environment.docker.enabled = True 

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], run_config = run_config_for_aml_compute)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

若要有效率地建立具有相同設定、引數和輸入的許多子回合[`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-) ，請使用方法。 因為每個建立都會導致網路呼叫，所以建立一批回合比逐一建立執行更有效率。

在子執行中，您可以查看父代執行識別碼：

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>查詢子執行

若要查詢特定父系的子執行，請使用[`get_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-)方法。 ``recursive = True``引數可讓您查詢子系和孫系的嵌套樹狀結構。

```python
print(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>標記和尋找執行

在 Azure Machine Learning 中，您可以使用屬性和標記來協助組織和查詢您的執行，以取得重要資訊。

### <a name="add-properties-and-tags"></a>新增屬性和標記

#### <a name="using-the-sdk"></a>使用 SDK

若要將可搜尋的中繼資料加入至[`add_properties()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-)執行中，請使用方法。 例如，下列程式碼會將`"author"`屬性新增至執行：

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

屬性是不可變的，因此它們會建立永久記錄以供審核之用。 下列程式碼範例會產生錯誤，因為我們已經在前面`"azureml-user"`的程式`"author"`代碼中新增做為屬性值：

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

不同于屬性，標記是可變動的。 若要為實驗的取用者新增可搜尋且有意義的[`tag()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-)資訊，請使用方法。

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

您也可以加入簡單的字串標記。 當這些標記以索引鍵的形式出現在標記字典中時，它們`None`的值為。

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>使用 CLI

> [!NOTE]
> 使用 CLI，您只能新增或更新標記。

若要新增或更新標記，請使用下列命令：

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

如需詳細資訊，請參閱[az ml run update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update)。

### <a name="query-properties-and-tags"></a>查詢屬性和標記

您可以在實驗內查詢執行，以傳回符合特定屬性和標記的執行清單。

#### <a name="using-the-sdk"></a>使用 SDK

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>使用 CLI

Azure CLI 支援[JMESPath](http://jmespath.org)查詢，可以用來根據屬性和標記篩選執行。 若要搭配 Azure CLI 使用 JMESPath 查詢，請使用`--query`參數來指定它。 下列範例顯示使用屬性和標記的基本查詢：

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

如需查詢 Azure CLI 結果的詳細資訊，請參閱[查詢 Azure CLI 命令輸出](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest)。

### <a name="using-azure-machine-learning-studio"></a>使用 Azure Machine Learning studio

1. 流覽至 [**管線**] 區段。

1. 使用 [搜尋] 列來篩選使用標記、描述、實驗名稱和提交者名稱的管線。

## <a name="example-notebooks"></a>Notebook 範例

下列筆記本示範本文中的概念：

* 若要深入瞭解記錄 Api，請參閱[記錄 api 筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb)。

* 如需使用 Azure Machine Learning SDK 管理執行的詳細資訊，請參閱[管理執行筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb)。

## <a name="next-steps"></a>後續步驟

* 若要瞭解如何為您的實驗記錄計量，請參閱[定型執行期間的記錄計量](how-to-track-experiments.md)。
* 若要瞭解如何從 Azure Machine Learning 監視資源和記錄，請參閱[監視 Azure Machine Learning](monitor-azure-machine-learning.md)。