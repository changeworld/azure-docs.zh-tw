---
title: 使用軟體環境
titleSuffix: Azure Machine Learning
description: 建立及管理模型定型和部署的環境。 管理環境的 Python 套件和其他設定。
services: machine-learning
author: saachigopal
ms.author: sagopal
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 07/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: e1285d62d036ebda46348ae1c1a3f8aa0db50243
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90882748"
---
# <a name="create--use-software-environments-in-azure-machine-learning"></a>在 Azure Machine Learning 中建立 & 使用軟體環境


在本文中，您將瞭解如何建立和管理 Azure Machine Learning [環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true)。 使用環境來追蹤和重現專案的軟體相依性。

軟體相依性管理是適用于開發人員的常見工作。 您想要確保組建可重現，而不需要大量的手動軟體配置。 Azure Machine Learning `Environment` 類別會針對本機開發解決方案（例如 pip 和 Conda），以及透過 Docker 功能的分散式雲端開發等帳戶。

本文中的範例示範如何：

* 建立環境並指定套件相依性。
* 取得和更新環境。
* 使用環境進行定型。
* 使用 web 服務部署的環境。

如需有關環境如何在 Azure Machine Learning 中運作的概要資訊，請參閱 [什麼是 ML 環境？](concept-environments.md) 如需設定開發環境的詳細資訊，請參閱 [這裡](how-to-configure-environment.md)。

## <a name="prerequisites"></a>必要條件

* [適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)
* [Azure Machine Learning 工作區](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>建立環境

下列各節將探索您可以為實驗建立環境的多種方式。

### <a name="instantiate-an-environment-object"></a>具現化環境物件

若要手動建立環境，請 `Environment` 從 SDK 匯入類別。 然後使用下列程式碼來具現化環境物件。

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-a-curated-environment"></a>使用策劃環境

策劃環境包含 Python 套件的集合，預設可在您的工作區中使用。 這些環境是由快取的 Docker 映射所支援，可減少執行準備成本。 您可以選取下列其中一個常用的策劃環境來開始： 

* _AzureML-最基本_的環境包含一組最基本的套件，可讓您執行追蹤和資產上傳。 您可以使用它做為您自己環境的起點。

* AzureML 教學課程環境包含常見 _的_ 資料科學套件。 這些套件包括 Scikit-learn-學習、Pandas、Matplotlib 和一組較大的 azureml sdk 套件。

如需策劃環境的清單，請參閱 [策劃環境文章](resource-curated-environments.md)。

使用 `Environment.get` 方法來選取其中一個策劃環境：

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

您可以使用下列程式碼列出策劃環境及其套件：

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  請勿以 _AzureML_ 首碼啟動您自己的環境名稱。 這個前置詞是保留給策劃環境。

### <a name="use-conda-dependencies-or-pip-requirements-files"></a>使用 Conda 相依性或 pip 需求檔案

您可以從 Conda 規格或 pip 需求檔案建立環境。 使用 [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#&preserve-view=truefrom-conda-specification-name--file-path-) 方法或 [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#&preserve-view=truefrom-pip-requirements-name--file-path-) 方法。 在方法引數中，加入您的環境名稱和您想要之檔案的檔案路徑。 

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")                                          
```

### <a name="enable-docker"></a>啟用 Docker

當您啟用 Docker 時，Azure Machine Learning 會根據您的規格建立 Docker 映射，並在該容器內建立 Python 環境。 系統會快取並重複使用 Docker 映射：新環境中的第一次執行通常需要較長的時間來建立映射。

Azure Machine Learning 類別的，可 [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py&preserve-view=true) `Environment` 讓您精確地自訂和控制執行定型的客體作業系統。 `arguments`變數可以用來指定要傳遞至 Docker run 命令的額外引數。

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

根據預設，新建立的 Docker 映射會出現在與工作區相關聯的容器登錄中。  存放庫名稱的形式為*azureml/azureml_ \<uuid\> *。 名稱 (*uuid*) 部分的唯一識別碼，會對應至從環境設定計算的雜湊。 此對應可讓服務判斷指定環境的映射是否已存在，以供重複使用。

#### <a name="use-a-prebuilt-docker-image"></a>使用預建的 Docker 映射

服務會自動使用其中一個以 Ubuntu Linux 為基礎的 [基底映射](https://github.com/Azure/AzureML-Containers)。 它會安裝指定的 Python 套件。 基底映射具有 CPU 版本和 GPU 版本。 Azure Machine Learning 會自動偵測要使用的版本。 您也可以使用 [自訂的 Docker 基底映射](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image#create-a-custom-base-image)。

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

>[!IMPORTANT]
> Azure Machine Learning 僅支援提供下列軟體的 Docker 映射：
> * Ubuntu 16.04 或更新版本。
> * Conda 4.5. # 或更高的版本。
> * Python 3.5. #、3.6. # 或 3.7. #。

#### <a name="use-your-own-dockerfile"></a>使用您自己的 Dockerfile 

您也可以指定自訂 Dockerfile。 使用 Docker 命令從 Azure Machine Learning 的基底映射開始最簡單 ```FROM``` ，然後新增您自己的自訂步驟。 如果您需要安裝非 Python 套件作為相依性，請使用此方法。 請記得將基礎映射設定為 [無]。

```python
# Specify docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = "./Dockerfile"
```

#### <a name="specify-your-own-python-interpreter"></a>指定您自己的 Python 解譯器

在某些情況下，您的自訂基底映射可能已經包含您想要使用之套件的 Python 環境。

若要使用您自己安裝的封裝並停用 Conda，請設定參數 `Environment.python.user_managed_dependencies = True` 。 確定基底映射包含 Python 解譯器，而且具有您定型腳本所需的套件。

例如，若要在已安裝 NumPy 套件的基底 Miniconda 環境中執行，請先使用安裝套件的步驟來指定 Dockerfile。 然後將 [使用者管理的相依性] 設定為 `True` 。 

您也可以藉由設定變數，在映射中指定特定 Python 解譯器的路徑 `Environment.python.interpreter_path` 。

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

> [!WARNING]
> 如果您在 Docker 映射中安裝一些 Python 相依性，但忘了設定 user_managed_dependencies = True，這些封裝將不會存在於執行環境中，因此會造成執行時間失敗。 根據預設，Azure ML 會使用您指定的相依性來建立 Conda 環境，並將在該環境中執行執行，而不是使用您在基底映射上安裝的任何 Python 程式庫。

#### <a name="retrieve-image-details"></a>取出映射詳細資料

針對已註冊的環境，您可以使用下列程式碼抓取映射詳細資料，其中 `details` 是 [DockerImageDetails](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockerimagedetails?view=azure-ml-py&preserve-view=true) (AzureML Python SDK >=) 1.11 的實例，並提供有關環境映射的所有資訊，例如 dockerfile、登錄和映射名稱。

```python
details = environment.get_image_details(workspace=ws)
```

### <a name="use-existing-environments"></a>使用現有的環境

如果您的本機電腦上有現有的 Conda 環境，則可以使用此服務來建立環境物件。 藉由使用此策略，您可以在遠端執行時重複使用本機互動式環境。

下列程式碼會從現有的 Conda 環境建立環境物件 `mycondaenv` 。 它會使用 [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#&preserve-view=truefrom-existing-conda-environment-name--conda-environment-name-) 方法。

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

您可以使用方法，以易於編輯的格式將環境定義儲存至目錄 [`save_to_directory()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#&preserve-view=truesave-to-directory-path--overwrite-false-) 。 修改之後，您可以從目錄載入檔案來具現化新的環境。

```python
myenv = Environment.save_to_directory(path = "path-to-destination-directory", overwrite = False)
# modify the environment definition
newenv = Environment.load_from_directory(path = "path-to-source-directory")
```

### <a name="create-environments-automatically"></a>自動建立環境

提交定型回合以自動建立環境。 使用方法來提交執行 `submit()` 。 當您提交定型回合時，新環境的建立可能需要幾分鐘的時間。 組建持續時間取決於所需相依性的大小。 

如果您在提交回合之前未在回合組態中指定環境，則會為您建立預設環境。

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

## <a name="add-packages-to-an-environment"></a>將套件新增至環境

使用 Conda、pip 或私人的 Wheel 檔案，將套件新增至環境。 使用類別來指定每個封裝相依性 [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py&preserve-view=true) 。 將它新增至環境的 `PythonSection` 。

### <a name="conda-and-pip-packages"></a>Conda 和 pip 套件

如果封裝在 Conda 套件存放庫中可供使用，則建議您使用 Conda 安裝，而不是 pip 安裝。 Conda 封裝通常隨附預建的二進位檔，讓安裝更可靠。

下列範例會新增至環境 `myenv` 。 其中會新增 1.17.0 版的 `numpy`。 它也會新增 `pillow` 套件。 此範例會分別使用 [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#&preserve-view=trueadd-conda-package-conda-package-) 方法和 [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#&preserve-view=trueadd-pip-package-pip-package-) 方法。

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs numpy version 1.17.0 conda package
conda_dep.add_conda_package("numpy==1.17.0")

# Installs pillow package
conda_dep.add_pip_package("pillow")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

您也可以將環境變數新增至您的環境。 然後，您可以使用環取得訓練腳本。

```python
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

>[!IMPORTANT]
> 如果您將相同的環境定義用於另一次執行，Azure Machine Learning 服務會重複使用您環境的快取映射。 例如，如果您建立的環境具有取消固定的套件相依性，則 ```numpy``` 該環境會繼續使用在 _建立環境時_所安裝的套件版本。 此外，任何符合定義的未來環境都將繼續使用舊版本。 如需詳細資訊，請參閱 [環境建立、快取和重複使用](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)。

### <a name="private-python-packages"></a>私人 Python 套件

若要私下且安全地使用 Python 套件，而不將它們公開給公用網際網路，請參閱文章 [如何使用私用 python 套件](how-to-use-private-python-packages.md)。

## <a name="manage-environments"></a>管理環境

管理環境，讓您可以在計算目標和工作區的其他使用者之間進行更新、追蹤和重複使用。

### <a name="register-environments"></a>註冊環境

當您提交執行或部署 web 服務時，環境會自動向您的工作區註冊。 您也可以使用方法手動註冊環境 [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#&preserve-view=trueregister-workspace-) 。 這項作業會讓環境成為在雲端中追蹤和建立版本的實體。 實體可以在工作區使用者之間共用。

下列程式碼會 `myenv` 向 `ws` 工作區註冊環境。

```python
myenv.register(workspace=ws)
```

當您第一次在定型或部署中使用環境時，它會向工作區註冊。 然後，它會建立並部署在計算目標上。 服務會快取環境。 重複使用快取的環境所需的時間比使用新服務或已更新的環境來得少。

### <a name="get-existing-environments"></a>取得現有的環境

`Environment`類別所提供的方法，可讓您在工作區中取出現有的環境。 您可以依名稱、清單或特定定型回合來取得環境。 這項資訊對於疑難排解、審核和重現性很有説明。

#### <a name="view-a-list-of-environments"></a>查看環境清單

使用類別來查看工作區中的環境 [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#&preserve-view=truelist-workspace-) 。 然後選取要重複使用的環境。

#### <a name="get-an-environment-by-name"></a>依名稱取得環境

您也可以依名稱和版本取得特定的環境。 下列程式碼會使用 [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#&preserve-view=trueget-workspace--name--version-none-) 方法，在 `1` `myenv` 工作區上取出環境的版本 `ws` 。

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>定型執行特定環境

若要取得定型完成後用於特定執行的環境，請使用 [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#&preserve-view=trueget-environment--) 類別中的方法 `Run` 。

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>更新現有的環境

例如，您可以藉由新增 Python 套件來變更現有的環境。 這需要一些時間來建立，因為當您提交執行、部署模型或手動註冊環境時，會建立新版本的環境。 版本控制可讓您在一段時間內查看環境的變更。 

若要更新現有環境中的 Python 套件版本，請指定該套件的版本號碼。 如果您未使用確切的版本號碼，Azure Machine Learning 將會重複使用現有的環境及其原始套件版本。

### <a name="debug-the-image-build"></a>將映射組建進行調試

下列範例會使用 [`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#&preserve-view=truebuild-workspace--image-build-compute-none-) 方法，以手動方式將環境建立為 Docker 映射。 它會使用來監視映射組建的輸出記錄檔 [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#&preserve-view=truewait-for-creation-show-output-false-) 。 然後，建立的映射會出現在工作區的 Azure Container Registry 實例中。 這項資訊有助於進行調試。

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

先使用方法在本機建立映射很有用 [`build_local()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#&preserve-view=truebuild-local-workspace--platform-none----kwargs-) 。 若要建立 docker 映射，請設定選用參數 `useDocker=True` 。 若要將產生的映射推送至 AzureML 工作區 container registry，請設定 `pushImageToWorkspaceAcr=True` 。

```python
build = env.build_local(workspace=ws, useDocker=True, pushImageToWorkspaceAcr=True)
```

> [!WARNING]
>  在環境中變更相依性或通道的順序，將會產生新的環境，並需要新的映射組建。 此外， `build()` 如果有新的版本，呼叫現有映射的方法將會更新其相依性。 

## <a name="use-environments-for-training"></a>使用環境進行訓練

若要提交訓練回合，您需要將您的環境、[計算目標](concept-compute-target.md)和訓練 Python 指令碼結合為回合組態。 此組態是用於提交回合的包裝函式物件。

提交訓練回合後，建立新環境可能需要幾分鐘的時間。 持續時間視所需相依性的大小而定。 服務會快取環境。 因此只要環境定義維持不變，您就只會遇到一次完整的安裝時間。

下列本機腳本執行範例會示範您要使用哪一個 [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py&preserve-view=true) 做為包裝函式物件。

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
> 若要停用執行歷程記錄或執行快照集，請使用下的設定 `ScriptRunConfig.run_config.history` 。

如果您未在回合設定中指定環境，則服務會在您提交執行時建立預設環境。

## <a name="use-environments-for-web-service-deployment"></a>使用 web 服務部署的環境

您可以在將模型部署為 Web 服務時使用環境。 這項功能可讓您以可重現的連接工作流程。 在此工作流程中，您可以使用定型計算和推斷計算中的相同程式庫，來定型、測試及部署您的模型。


如果您要為 web 服務部署定義自己的環境，您必須以 pip 相依性的 `azureml-defaults` 版本 >= 1.0.45 版 azureml-defaults 來列出。 此套件包含將模型裝載為 web 服務所需的功能。

若要部署 Web 服務，請在您的部署物件中結合環境、推斷計算、評分指令碼和已註冊的模型 ([`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-))。 如需詳細資訊，請參閱 [部署模型的方式和位置](how-to-deploy-and-where.md)。

在此範例中，假設您已完成定型回合。 現在您想要將該模型部署至 Azure 容器實例。 當您建立 web 服務時，模型和評分檔案會掛接在映射上，而 Azure Machine Learning 推斷堆疊會加入至映射。

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

## <a name="notebooks"></a>Notebooks

本文 [提供有關](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks#add-new-kernels) 如何在筆記本中安裝 Conda 環境作為核心的資訊。

[使用自訂的 docker 基底映射部署模型](how-to-deploy-custom-docker-image.md) 示範如何使用自訂的 docker 基底映射部署模型。

此 [範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) 示範如何將 Spark 模型部署為 web 服務。

## <a name="create-and-manage-environments-with-the-cli"></a>使用 CLI 建立和管理環境

[AZURE MACHINE LEARNING CLI](reference-azure-machine-learning-cli.md)會反映 Python SDK 的大部分功能。 您可以使用它來建立和管理環境。 我們在本節中討論的命令會示範基本功能。

下列命令會在指定的目錄中 scaffold 預設環境定義的檔案。 這些檔案是 JSON 檔案。 它們的運作方式類似 SDK 中的對應類別。 您可以使用這些檔案來建立具有自訂設定的新環境。 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

執行下列命令，以從指定的目錄註冊環境。

```azurecli-interactive
az ml environment register -d myenvdir
```

執行下列命令以列出所有已註冊的環境。

```azurecli-interactive
az ml environment list
```

使用下列命令來下載已註冊的環境。

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>下一步

* 若要使用受控計算目標來定型模型，請參閱 [教學課程：定型模型](tutorial-train-models-with-aml.md)。
* 在您擁有定型的模型之後，請瞭解 [部署模型的方式和位置](how-to-deploy-and-where.md)。
* 查看[ `Environment` 類別 SDK 參考](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py&preserve-view=true)。
