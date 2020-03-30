---
title: 創建可重用的 ML 環境
titleSuffix: Azure Machine Learning
description: 創建和管理用於模型培訓和部署的環境。 管理 Python 包和環境的其他設置。
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 3b1fc5dc427a8a9a1987b0ef916b99edb25e292a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063973"
---
# <a name="reuse-environments-for-training-and-deployment-by-using-azure-machine-learning"></a>使用 Azure 機器學習重用用於培訓和部署的環境
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，瞭解如何創建和管理 Azure 機器學習[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)。 使用環境跟蹤和重現專案的軟體依賴關係，並隨著專案的發展而變化。

軟體依賴項管理是開發人員的常見任務。 您希望確保在未進行大量手動軟體配置的情況下可重現生成。 Azure 機器學習`Environment`類考慮本地開發解決方案（如 pip 和 Conda），它為本地和分散式雲開發提供了解決方案。

本文中的示例演示如何：

* 創建環境並指定包依賴項。
* 檢索和更新環境。
* 使用環境進行培訓。
* 使用環境進行 Web 服務部署。

有關環境在 Azure 機器學習中工作方式的高級概述，請參閱什麼是[ML 環境？](concept-environments.md)

## <a name="prerequisites"></a>Prerequisites

* [用於 Python 的 Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Azure 機器學習工作區](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>建立環境

以下各節探討了為實驗創建環境的多種方法。

### <a name="use-a-curated-environment"></a>使用精心策劃的環境

您可以選擇一個精心策劃的環境，以便從以下開始： 

* _AzureML-最小_環境包含一組最少的包，用於啟用運行跟蹤和資產上載。 您可以將其用作您自己的環境的起點。

* _AzureML-教程_環境包含通用的資料科學包。 這些包包括 Scikit-學習、熊貓、Matplotlib 和一組更大的 azureml-sdk 包。

精選環境由緩存的 Docker 映射支援。 此備份降低了運行準備成本。

使用`Environment.get`方法選擇其中一個已計算的環境：

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

您可以使用以下代碼列出已策劃的環境及其包：

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  不要使用_AzureML_首碼啟動自己的環境名稱。 此首碼保留給已策劃的環境。

### <a name="instantiate-an-environment-object"></a>具現化環境物件

要手動創建環境，`Environment`請從 SDK 導入類。 然後使用以下代碼具現化環境物件。

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-conda-and-pip-specification-files"></a>使用康達和點規格檔

您還可以從 Conda 規範或 pip 要求檔創建環境。 使用[`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-)方法或[`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-)方法。 在方法參數中，包括環境名稱和所需的檔的檔路徑。

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="use-existing-conda-environments"></a>使用現有的康達環境

如果本地電腦上存在現有的 Conda 環境，則可以使用該服務創建環境物件。 通過使用此策略，您可以在遠端運行時重用本地互動式環境。

以下代碼從現有的 Conda 環境`mycondaenv`創建一個環境物件。 它使用[`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-)方法。

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="create-environments-automatically"></a>自動創建環境

通過提交培訓運行自動創建環境。 使用`submit()`方法提交運行。 提交培訓運行時，新環境的構建可能需要幾分鐘時間。 生成持續時間取決於所需依賴項的大小。 

如果在提交運行之前未在回合組態中指定環境，則會為您創建預設環境。

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attach training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Show each step of run 
run.wait_for_completion(show_output=True)
```

同樣，如果將[`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)物件用於訓練，則可以直接提交估計器實例作為運行，而無需指定環境。 物件`Estimator`已封裝環境和計算目標。

## <a name="add-packages-to-an-environment"></a>將包添加到環境

使用 Conda、pip 或專用滾輪檔將包添加到環境中。 使用[`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)類指定每個包依賴項。 將其添加到環境 。 `PythonSection`

### <a name="conda-and-pip-packages"></a>康達和點包

如果 Conda 包存儲庫中有可用的包，我們建議您使用 Conda 安裝而不是 pip 安裝。 Conda 套裝軟體通常附帶預構建的二進位檔案，使安裝更可靠。

下面的示例增加了環境。 它添加了 版本 0.21.3 的`scikit-learn`。 它還添加了`pillow`包。 `myenv` 該示例分別使用[`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-)方法和方法[`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-)。

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs scikit-learn version 0.21.3 conda package
conda_dep.add_conda_package("scikit-learn==0.21.3")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

>[!IMPORTANT]
> 如果對另一次運行使用相同的環境定義，Azure 機器學習服務將重用環境的緩存映射。 例如```numpy```，如果創建具有未固定包依賴項的環境，則該環境將繼續使用_創建環境時安裝的_包版本。 此外，任何具有匹配定義的將來環境都將繼續使用舊版本。 有關詳細資訊，請參閱[環境構建、緩存和重用](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)。

### <a name="private-wheel-files"></a>私人車輪檔

您可以通過首先將它們上載到工作區存儲來使用專用 pip 輪檔。 使用靜態[`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-)方法上載它們。 然後捕獲存儲 URL 並將 URL 傳遞給`add_pip_package()`方法。

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>管理環境

管理環境，以便跨計算目標和工作區的其他使用者更新、跟蹤和重用它們。

### <a name="register-environments"></a>註冊環境

當您提交運行或部署 Web 服務時，環境將自動註冊到工作區。 您還可以使用[`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-)方法手動註冊環境。 此操作將環境變成在雲中跟蹤和個版本的實體。 實體可以在工作區使用者之間共用。

以下代碼將`myenv`環境註冊到`ws`工作區。

```python
myenv.register(workspace=ws)
```

首次在訓練或部署中使用環境時，該環境已註冊到工作區。 然後，它將在計算目標上生成並部署。 服務緩存環境。 與使用新服務或已更新的服務相比，重用緩存環境所需的時間要少得多。

### <a name="get-existing-environments"></a>獲取現有環境

該`Environment`類提供了允許您檢索工作區中現有環境的方法。 您可以按名稱、清單或特定培訓運行檢索環境。 此資訊有助於故障排除、審核和重現性。

#### <a name="view-a-list-of-environments"></a>查看環境清單

使用[`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-)類查看工作區中的環境。 然後選擇要重用的環境。

#### <a name="get-an-environment-by-name"></a>按名稱獲取環境

您還可以按名稱和版本獲取特定環境。 以下代碼使用 方法[`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-)檢索`1``myenv``ws`工作區上環境的版本。

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>訓練特定于運行的環境

要獲取在訓練完成後用於特定運行的環境，請使用 類中[`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--)`Run`的方法。

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>更新現有環境

假設您通過添加 Python 包來更改現有環境。 然後，在提交運行、部署模型或手動註冊環境時，將創建環境的新版本。 版本控制允許您查看環境隨時間的變化。

要在現有環境中更新 Python 包版本，請指定該包的版本號。 如果不使用確切的版本號，則 Azure 機器學習將重用現有環境及其原始包版本。

### <a name="debug-the-image-build"></a>調試映射生成

下面的示例使用 方法[`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace--image-build-compute-none-)手動創建環境作為 Docker 映射。 它使用[`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-)監視映射生成中的輸出日誌。 然後，生成的映射將顯示在工作區的 Azure 容器註冊表實例中。 此資訊有助於調試。

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="enable-docker"></a>啟用 Docker

 Azure[`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py)機器學習`Environment`類允許您精細自訂和控制運行訓練的客體作業系統。

啟用 Docker 時，該服務將生成 Docker 映射。 它還創建一個 Python 環境，該環境在 Docker 容器中使用您的規範。 此功能為您的訓練運行提供了額外的隔離和重現性。

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

預設情況下，新生成的 Docker 映射將顯示在與工作區關聯的容器註冊表中。  存儲庫名稱具有表單*azureml/azureml_\<uuid\>*。 名稱的唯一識別碼 *（uuid*） 部分對應于從環境配置計算的雜湊。 此通信允許服務確定給定環境的映射是否存在以供重用。

此外，該服務會自動使用基於 Ubuntu Linux[的基本映射](https://github.com/Azure/AzureML-Containers)之一。 它安裝指定的 Python 包。 基本映射具有 CPU 版本和 GPU 版本。 Azure 機器學習會自動檢測要使用的版本。

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

您還可以指定自訂 Dockerfile。 最簡單的方法是使用 Docker```FROM```命令從 Azure 機器學習基本映射之一開始，然後添加自己的自訂步驟。 如果需要將非 Python 包作為依賴項，請使用此方法。

```python
# Specify docker steps as a string. Alternatively, load the string from a file.
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
```

### <a name="use-user-managed-dependencies"></a>使用使用者管理的依賴項

在某些情況下，自訂基本映射可能已包含包含要使用的包的 Python 環境。

預設情況下，Azure 機器學習服務將構建具有您指定的依賴項的 Conda 環境，並將在該環境中執行運行，而不是使用在基本映射上安裝的任何 Python 庫。 

要使用自己的已安裝包，請設置`Environment.python.user_managed_dependencies = True`參數 。 確保基本映射包含 Python 解譯器，並具有訓練腳本所需的包。

例如，要在安裝了 NumPy 包的基本 Miniconda 環境中運行，請先指定具有安裝包步驟的 Dockerfile。 然後，將使用者託管的依賴項設置為`True`。 

還可以通過設置變數來指定圖像中特定 Python 解譯器的`Environment.python.interpreter_path`路徑。

```python
dockerfile = """
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN conda install numpy
"""

myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
myenv.python.user_managed_dependencies=True
myenv.python.interpreter_path = "/opt/miniconda/bin/python"
```

## <a name="use-environments-for-training"></a>使用環境進行培訓

要提交訓練運行，您需要將環境、[計算目標](concept-compute-target.md)和培訓 Python 腳本合併到回合組態中。 此配置是用於提交運行的包裝物件。

提交培訓運行時，構建新環境可能需要幾分鐘時間。 持續時間取決於所需依賴項的大小。 環境由服務緩存。 因此，只要環境定義保持不變，您只產生一次完整設置時間。

以下本地腳本運行示例顯示用作[`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py)包裝物件的位置。

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.environment import Environment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Add training script to run config
runconfig = ScriptRunConfig(source_directory=".", script="train.py")

# Attach compute target to run config
runconfig.run_config.target = "local"

# Attach environment to run config
runconfig.run_config.environment = myenv

# Submit run 
run = exp.submit(runconfig)
```

> [!NOTE]
> 要禁用執行歷程記錄或運行快照，請使用 下`ScriptRunConfig.run_config.history`設置。

如果不在回合組態中指定環境，則服務會在提交運行時創建預設環境。

### <a name="use-an-estimator-for-training"></a>使用估計器進行培訓

如果您使用[估計器](how-to-train-ml-models.md)進行培訓，則可以直接提交估計器實例。 它已經封裝了環境和計算目標。

以下代碼使用估計器進行單節點訓練運行。 它在`scikit-learn`模型的遠端計算上運行。 它假定您以前創建了計算目標物件`compute_target`和資料存儲物件 。 `ds`

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])

# Submit the run 
run = experiment.submit(sk_est)
```

## <a name="use-environments-for-web-service-deployment"></a>將環境用於 Web 服務部署

在將模型部署為 Web 服務時，可以使用環境。 此功能支援可重現的互聯工作流。 在此工作流中，您可以在訓練計算和推理計算中使用相同的庫來訓練、測試和部署模型。

要部署 Web 服務，請將環境、推理計算、評分腳本和已註冊的模型合併到部署物件[`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)中。 有關詳細資訊，請參閱[如何部署模型以及部署模型的位置](how-to-deploy-and-where.md)。

在此示例中，假設您已完成培訓運行。 現在，您希望將該模型部署到 Azure 容器實例。 生成 Web 服務時，模型和評分檔將安裝在映射上，Azure 機器學習推理堆疊將添加到映射中。

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference, & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>Notebook 範例

[此示例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments)擴展了本文中演示的概念和方法。

[使用自訂 Docker 基本映射部署模型](how-to-deploy-custom-docker-image.md)演示如何使用自訂 Docker 基本映射部署模型。

[此示例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark)演示如何將 Spark 模型部署為 Web 服務。

## <a name="create-and-manage-environments-with-the-cli"></a>使用 CLI 創建和管理環境

[Azure 機器學習 CLI](reference-azure-machine-learning-cli.md)反映了 Python SDK 的大部分功能。 您可以使用它創建和管理環境。 本節中討論的命令演示了基本功能。

以下命令將指定目錄中預設環境定義的檔架上支架。 這些檔是JSON檔。 它們的工作方式類似于 SDK 中的相應類。 您可以使用這些檔創建具有自訂設置的新環境。 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

運行以下命令以從指定的目錄註冊環境。

```azurecli-interactive
az ml environment register -d myenvdir
```

運行以下命令以列出所有已註冊的環境。

```azurecli-interactive
az ml environment list
```

使用以下命令下載已註冊的環境。

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>後續步驟

* 要使用託管計算目標來訓練模型，請參閱[教程：訓練模型](tutorial-train-models-with-aml.md)。
* 獲得經過訓練的模型後，[瞭解如何部署模型以及部署模型的位置](how-to-deploy-and-where.md)。
* 查看[`Environment`類 SDK 引用](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)。
* 有關本文中描述的概念和方法的詳細資訊，請參閱[示例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments)。
