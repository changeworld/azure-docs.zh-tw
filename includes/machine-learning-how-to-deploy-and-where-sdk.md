---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 624824f5b6b8f7154ccd7b50da49f3f4bb179bb9
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542759"
---
## <a name="prerequisites"></a>Prerequisites

- Azure Machine Learning 工作區。 如需詳細資訊，請參閱[建立 Azure Machine Learning 工作區](../articles/machine-learning/how-to-manage-workspace.md)。
- 模型。 如果您沒有定型的模型，您可以使用[本教學](https://aka.ms/azml-deploy-cloud)課程中提供的模型和相依性檔案。
- [適用于 Python 的 Azure Machine Learning 軟體發展工具組（SDK）](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)


## <a name="connect-to-your-workspace"></a>連線到您的工作區

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

如需使用 SDK 連線到工作區的詳細資訊，請參閱[適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace)檔。

## <a name="register-your-model"></a><a id="registermodel"></a>註冊您的模型

已註冊的模型是組成模型的一或多個檔案所在的邏輯容器。 例如，如果您有儲存在多個檔案中的模型，您可以在工作區中將其註冊為單一模型。 註冊檔案之後，您就可以下載或部署已註冊的模型，並接收您註冊的所有檔案。

> [!TIP]
> 當您註冊模型時，您會提供雲端位置（從定型回合）或本機目錄的路徑。 此路徑只是為了在註冊過程中，尋找要上傳的檔案。 它不需要符合輸入腳本中使用的路徑。 如需詳細資訊，請參閱[在您的輸入腳本中尋找模型](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models)檔案。

機器學習模型會在您的 Azure Machine Learning 工作區中註冊。 模型可以來自 Azure Machine Learning 或其他地方。 註冊模型時，您可以選擇性地提供有關模型的中繼資料。 `tags`接著， `properties` 您可以將套用至模型註冊的和字典用於篩選模型。

下列範例示範如何註冊模型。

### <a name="register-a-model-from-an-azure-ml-training-run"></a>從 Azure ML 訓練執行註冊模型

  當您使用 SDK 來定型模型時，您可以接收[執行](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py)物件或[AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun)物件，視您如何訓練模型而定。 每個物件都可以用來註冊實驗執行所建立的模型。

  + 從物件註冊模型 `azureml.core.Run` ：
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path`參數會參考模型的雲端位置。 在此範例中，會使用單一檔案的路徑。 若要在模型註冊中包含多個檔案，請將設定 `model_path` 為包含檔案的資料夾路徑。 如需詳細資訊，請參閱[Run. register_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-)檔。

  + 從物件註冊模型 `azureml.train.automl.run.AutoMLRun` ：

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    在此範例中， `metric` `iteration` 未指定和參數，因此將會註冊具有最佳主要度量的反復專案。 `model_id`會使用從執行傳回的值，而不是模型名稱。

    如需詳細資訊，請參閱[AutoMLRun。 register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-)檔。


### <a name="register-a-model-from-a-local-file"></a>從本機檔案註冊模型

您可以藉由提供模型的本機路徑來註冊模型。 您可以提供資料夾或單一檔案的路徑。 您可以使用這個方法來註冊使用 Azure Machine Learning 定型的模型，然後下載。 您也可以使用這個方法來註冊在 Azure Machine Learning 外部定型的模型。

[!INCLUDE [trusted models](machine-learning-service-trusted-model.md)]

+ **使用 SDK 和 ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  若要在模型註冊中包含多個檔案，請將設定 `model_path` 為包含檔案的資料夾路徑。

如需詳細資訊，請參閱[模型類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)的檔。

如需使用 Azure Machine Learning 外部定型模型的詳細資訊，請參閱[如何部署現有的模型](../articles/machine-learning/how-to-deploy-existing-model.md)。


## <a name="define-an-entry-script"></a>定義專案腳本

[!INCLUDE [write entry script](machine-learning-entry-script.md)]

## <a name="define-an-inference-configuration"></a>定義推斷設定

推斷設定會說明如何設定包含模型的 web 服務。 稍後當您部署模型時，就會用到它。

推斷設定會使用 Azure Machine Learning 環境來定義您的部署所需的軟體相依性。 環境可讓您建立、管理及重複使用定型和部署所需的軟體相依性。 您可以從自訂相依性檔案建立環境，或使用其中一個策劃 Azure Machine Learning 環境。 下列 YAML 是用於推斷的 Conda 相依性檔案範例。 請注意，您必須以 pip 相依性的形式來表示版本 >= 1.0.45 的 azureml 預設值，因為它包含裝載模型做為 web 服務所需的功能。 如果您想要使用自動產生架構，您的輸入腳本也必須匯入 `inference-schema` 封裝。

```YAML

name: project_environment
dependencies:
    - python=3.6.2
    - scikit-learn=0.20.0
    - pip:
        # You must list azureml-defaults as a pip dependency
    - azureml-defaults>=1.0.45
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> 如果您的相依性可透過 Conda 和 pip （來自 PyPi）取得，Microsoft 建議使用 Conda 版本，因為 Conda 套件通常會隨附預先建立的二進位檔，讓安裝更可靠。
>
> 如需詳細資訊，請參閱[瞭解 Conda 和 Pip](https://www.anaconda.com/understanding-conda-and-pip/)。
>
> 若要檢查您的相依性是否可透過 Conda 取得，請使用 `conda search <package-name>` 命令，或使用和中的封裝索引 [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo) 。

您可以使用相依性檔案來建立環境物件，並將它儲存到您的工作區，以供日後使用：

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                                file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

如需使用和自訂具有 Azure Machine Learning 之 Python 環境的完整討論，請參閱[在 Azure Machine Learning 中建立 & 使用軟體環境](../articles/machine-learning/how-to-use-environments.md)

如需有關使用自訂 Docker 映射搭配推斷設定的詳細資訊，請參閱[如何使用自訂 docker 映射部署模型](../articles/machine-learning/how-to-deploy-custom-docker-image.md)。


下列範例示範如何從您的工作區載入環境，然後將它與推斷設定搭配使用：

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=myenv)
```

如需環境的詳細資訊，請參閱[建立和管理用於定型和部署的環境](../articles/machine-learning/how-to-use-environments.md)。

如需有關推斷設定的詳細資訊，請參閱[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)類別檔。

## <a name="choose-a-compute-target"></a>選擇計算目標


[!INCLUDE [aml-compute-target-deploy](aml-compute-target-deploy.md)]



## <a name="define-a-deployment-configuration"></a>定義部署設定

在部署模型之前，您必須先定義部署設定。 *部署設定適用于將裝載 web 服務的計算目標。* 例如，當您在本機部署模型時，您必須指定服務接受要求的埠。 部署設定不屬於您的輸入腳本。 它是用來定義將裝載模型和專案腳本之計算目標的特性。

您可能也需要建立計算資源（例如，您的工作區尚未有相關聯的 Azure Kubernetes Service （AKS）實例）。

下表提供建立每個計算目標之部署設定的範例：

| 計算目標 | 部署設定範例 |
| ----- | ----- |
| 本機 | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

本機、Azure 容器實例和 AKS web 服務的類別可以從下列來源匯入 `azureml.core.webservice` ：

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```


## <a name="deploy-your-model"></a>部署模型

您現在已準備好部署您的模型。 下列範例示範本機部署。 語法會根據您在上一個步驟中選擇的計算目標而有所不同。

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

如需詳細資訊，請參閱[LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py)、 [Model. deploy （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)和[Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py)的檔。


## <a name="delete-resources"></a>刪除資源

若要刪除已部署的 Web 服務，請使用 `service.delete()`。
若要刪除已註冊的模型，請使用 `model.delete()`。

如需詳細資訊，請參閱[WebService. delete （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--)和[Model. delete （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--)的檔。

