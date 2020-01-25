---
title: 建立可重複使用的 ML 環境
titleSuffix: Azure Machine Learning
description: 建立和管理用於模型定型和部署的環境。 管理環境的 Python 套件和其他設定。
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 9d6c823b5dc85676b15188c8f1783e50e5ed441b
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717825"
---
# <a name="reuse-environments-for-training-and-deployment-by-using-azure-machine-learning"></a>使用 Azure Machine Learning，重複使用環境進行定型和部署
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何建立和管理 Azure Machine Learning[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)。 使用環境來追蹤和重現專案的軟體相依性。

軟體相依性管理是適用于開發人員的一般工作。 您想要確保組建可以重現，而不需要大量手動軟體設定。 Azure Machine Learning `Environment` 類別帳戶來進行本機開發解決方案（例如 pip 和 Conda），並提供本機和分散式雲端開發的解決方案。

本文中的範例示範如何：

* 建立環境並指定套件相依性。
* 取出和更新環境。
* 使用環境進行定型。
* 使用 web 服務部署的環境。

如需環境如何在 Azure Machine Learning 中工作的高階總覽，請參閱[什麼是 ML 環境？](concept-environments.md)。

## <a name="prerequisites"></a>必要條件

* [適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Azure Machine Learning 工作區](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>建立環境

下列各節將探索您可為實驗建立環境的多種方式。

### <a name="use-a-curated-environment"></a>使用策劃環境

您可以選取其中一個策劃環境以開始使用： 

* _AzureML-最小_環境包含一組最少的封裝，可啟用執行追蹤和資產上傳。 您可以使用它作為您自己環境的起點。

* _AzureML-教學_課程環境包含常見的資料科學套件。 這些套件包括 Scikit-learn-學習、Pandas、Matplotlib，以及一組較大的 azureml sdk 套件。

策劃環境是由快取的 Docker 映射所支援。 這種支援可減少執行準備成本。

使用 `Environment.get` 方法來選取其中一個策劃環境：

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

您可以使用下列程式碼來列出策劃環境及其封裝：

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  請勿以_AzureML_前置詞啟動您自己的環境名稱。 這個前置詞是保留給策劃環境。

### <a name="instantiate-an-environment-object"></a>具現化環境物件

若要手動建立環境，請從 SDK 匯入 `Environment` 類別。 然後使用下列程式碼來具現化環境物件。

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-conda-and-pip-specification-files"></a>使用 Conda 和 pip 規格檔案

您也可以從 Conda 規格或 pip 需求檔案建立環境。 請使用[`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-)方法或[`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-)方法。 在方法引數中，包含您的環境名稱和所需檔案的檔案路徑。

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="use-existing-conda-environments"></a>使用現有的 Conda 環境

如果您的本機電腦上有現有的 Conda 環境，則可以使用此服務來建立環境物件。 藉由使用此策略，您可以在遠端執行上重複使用您的本機互動式環境。

下列程式碼會從現有的 Conda 環境中建立環境物件 `mycondaenv`。 它會使用[`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-)方法。

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="create-environments-automatically"></a>自動建立環境

藉由提交定型回合，自動建立環境。 使用 `submit()` 方法提交執行。 當您提交定型回合時，新環境的建立可能需要幾分鐘的時間。 組建持續時間視所需相依性的大小而定。 

如果您在提交執行之前未在回合設定中指定環境，則會為您建立預設環境。

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

同樣地，如果您使用[`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)物件進行定型，您可以直接提交估計工具實例作為執行，而不需要指定環境。 `Estimator` 物件已封裝環境和計算目標。

## <a name="add-packages-to-an-environment"></a>將套件新增至環境

使用 Conda、pip 或私用滾輪檔案，將封裝新增至環境。 使用[`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)類別來指定每個封裝相依性。 將它新增至環境的 `PythonSection`。

### <a name="conda-and-pip-packages"></a>Conda 和 pip 套件

如果封裝可在 Conda 套件存放庫中使用，則建議您使用 Conda 安裝，而不是 pip 安裝。 Conda 套件通常會隨附預先建立的二進位檔，讓安裝更可靠。

下列範例會將加入至環境。 它會新增 `scikit-learn`的版本0.21.3。 它也會新增 `pillow` 封裝，`myenv`。 此範例會分別使用[`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-)方法和[`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-)方法。

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

### <a name="private-wheel-files"></a>私人滾輪檔案

您可以使用私用的 pip 滾輪檔案，方法是先將它們上傳到您的工作區儲存體。 您可以使用靜態[`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-)方法來上傳它們。 然後您會捕捉儲存體 URL，並將 URL 傳遞給 `add_pip_package()` 方法。

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>管理環境

管理環境，讓您可以跨計算目標和工作區的其他使用者來更新、追蹤及重複使用它們。

### <a name="register-environments"></a>註冊環境

當您提交執行或部署 web 服務時，環境會自動向您的工作區註冊。 您也可以使用[`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-)方法，手動註冊環境。 這項作業會讓環境進入在雲端中追蹤和建立版本的實體。 實體可以在工作區使用者之間共用。

下列程式碼會向 `ws` 工作區註冊 `myenv` 環境。

```python
myenv.register(workspace=ws)
```

當您第一次在訓練或部署中使用環境時，它會向工作區註冊。 然後它會建立並部署在計算目標上。 服務會快取環境。 重複使用快取的環境所花的時間比使用新的服務或已更新的環境少很多。

### <a name="get-existing-environments"></a>取得現有的環境

`Environment` 類別提供的方法可讓您在工作區中取得現有的環境。 您可以依名稱、清單或特定定型執行來抓取環境。 這種資訊有助於疑難排解、審核和重現性。

#### <a name="view-a-list-of-environments"></a>查看環境清單

使用[`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-)類別來查看工作區中的環境。 然後選取要重複使用的環境。

#### <a name="get-an-environment-by-name"></a>依名稱取得環境

您也可以依名稱和版本取得特定的環境。 下列程式碼會使用[`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-)方法來抓取 `ws` 工作區上 `myenv` 環境的版本 `1`。

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>訓練執行特定環境

若要取得在定型完成後用於特定執行的環境，請使用 `Run` 類別中的[`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--)方法。

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>更新現有的環境

假設您變更了現有的環境，例如，藉由新增 Python 套件。 然後，當您提交執行、部署模型或手動註冊環境時，就會建立新版本的環境。 版本控制可讓您在一段時間後查看環境的變更。

若要更新現有環境中的 Python 套件版本，請指定該封裝的版本號碼。 如果您未使用確切的版本號碼，Azure Machine Learning 將會重複使用現有的環境及其原始套件版本。

### <a name="debug-the-image-build"></a>對映射組建進行 Debug

下列範例會使用[`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace-)方法，以手動方式將環境建立為 Docker 映射。 它會使用[`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-)來監視映射組建中的輸出記錄。 然後，建立的映射會出現在工作區的 Azure Container Registry 實例中。 這種資訊對偵錯工具很有説明。

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="enable-docker"></a>啟用 Docker

 Azure Machine Learning `Environment` 類別的[`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py)可讓您精確地自訂和控制執行訓練的客體作業系統。

當您 `enable` Docker 時，服務會建立 Docker 映射。 It also creates a Python environment that uses your specifications within that Docker container. This functionality provides additional isolation and reproducibility for your training runs.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

By default, the newly built Docker image appears in the container registry that's associated with the workspace.  The repository name has the form *azureml/azureml_\<uuid\>* . The unique identifier (*uuid*) part of the name corresponds to a hash that's computed from the environment configuration. This correspondence allows the service to determine whether an image for the given environment already exists for reuse.

Additionally, the service automatically uses one of the Ubuntu Linux-based [base images](https://github.com/Azure/AzureML-Containers). It installs the specified Python packages. The base image has CPU versions and GPU versions. Azure Machine Learning automatically detects which version to use.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
# Alternatively, you can specify the contents of dockerfile of your base image
with open("docker_file_of_your_base_image", "r") as f:
    dockerfile_contents_of_your_base_image=f.read()
myenv.docker.base_dockerfile=dockerfile_contents_of_your_base_image 
```

> [!NOTE]
> If you specify `environment.python.user_managed_dependencies=False` while you're using a custom Docker image, then the service will build a Conda environment within the image. It will execute the run in that environment instead of using any Python libraries that you installed on the base image. Set the parameter to `True` to use your own installed packages.

## <a name="use-environments-for-training"></a>Use environments for training

To submit a training run, you need to combine your environment, [compute target](concept-compute-target.md), and your training Python script into a run configuration. This configuration is a wrapper object that's used for submitting runs.

When you submit a training run, the building of a new environment can take several minutes. The duration depends on the size of the required dependencies. The environments are cached by the service. So as long as the environment definition remains unchanged, you incur the full setup time only once.

The following local script run example shows where you would use [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) as your wrapper object.

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
> To disable the run history or run snapshots, use the setting under `ScriptRunConfig.run_config.history`.

If you don't specify the environment in your run configuration, then the service creates a default environment when you submit your run.

### <a name="use-an-estimator-for-training"></a>Use an estimator for training

If you use an [estimator](how-to-train-ml-models.md) for training, then you can submit the estimator instance directly. It already encapsulates the environment and the compute target.

The following code uses an estimator for a single-node training run. It runs on a remote compute for a `scikit-learn` model. It assumes that you previously created a compute target object, `compute_target`, and a datastore object, `ds`.

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

## <a name="use-environments-for-web-service-deployment"></a>Use environments for web service deployment

You can use environments when you deploy your model as a web service. This capability enables a reproducible, connected workflow. In this workflow, you can train, test, and deploy your model by using the same libraries in both your training compute and your inference compute.

To deploy a web service, combine the environment, inference compute, scoring script, and registered model in your deployment object, [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-). For more information, see [How and where to deploy models](how-to-deploy-and-where.md).

In this example, assume that you've completed a training run. Now you want to deploy that model to Azure Container Instances. When you build the web service, the model and scoring files are mounted on the image, and the Azure Machine Learning inference stack is added to the image.

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

This [example notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) expands upon concepts and methods demonstrated in this article.

[Deploy a model using a custom Docker base image](how-to-deploy-custom-docker-image.md) demonstrates how to deploy a model using a custom Docker base image.

This [example notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) demonstrates how to deploy a Spark model as a web service.

## <a name="create-and-manage-environments-with-the-cli"></a>Create and manage environments with the CLI

The [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) mirrors most of the functionality of the Python SDK. You can use it to create and manage environments. The commands that we discuss in this section demonstrate basic functionality.

The following command scaffolds the files for a default environment definition in the specified directory. These files are JSON files. They work like the corresponding class in the SDK. You can use the files to create new environments that have custom settings. 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

Run the following command to register an environment from a specified directory.

```azurecli-interactive
az ml environment register -d myenvdir
```

執行下列命令以列出所有已註冊的環境。

```azurecli-interactive
az ml environment list
```

使用下列命令下載已註冊的環境。

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>後續步驟

* 若要使用受控計算目標來定型模型，請參閱[教學課程：將模型定型](tutorial-train-models-with-aml.md)。
* 在您擁有定型模型之後，請瞭解[部署模型的方式和位置](how-to-deploy-and-where.md)。
* 查看[`Environment` 類別 SDK 參考](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)。
* 如需本文所述概念和方法的詳細資訊，請參閱[範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments)。
