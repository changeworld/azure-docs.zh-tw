---
title: 部署模型的方式和位置
titleSuffix: Azure Machine Learning
description: 瞭解部署 Azure Machine Learning 模型的方式和位置，包括 Azure 容器實例、Azure Kubernetes Service、Azure IoT Edge 和可現場程式化閘道陣列。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 09/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 40d8d4596e7d93b589e44f2dde2d1cb453566d71
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999224"
---
# <a name="deploy-models-with-azure-machine-learning"></a>使用 Azure Machine Learning 部署模型

瞭解如何將您的機器學習模型部署為 Azure 雲端中的 web 服務，或 Azure IoT Edge 裝置。

無論部署模型的位置為何，工作流程都十分類似：

1.  (選擇性註冊模型，請參閱下面的) 。
1. 準備推斷設定 (，除非使用 [無程式碼部署](./how-to-deploy-no-code-deployment.md)) 。
1. 準備輸入腳本 (，除非使用 [無程式碼部署](./how-to-deploy-no-code-deployment.md)) 。
1. 選擇計算目標。
1. 將模型部署至計算目標。
1. 測試所產生的 web 服務。

如需部署工作流程相關概念的詳細資訊，請參閱 [使用 Azure Machine Learning 管理、部署和監視模型](concept-model-management-and-deployment.md)。

## <a name="prerequisites"></a>必要條件

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

- Azure Machine Learning 工作區。 如需詳細資訊，請參閱 [建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。
- 模型。 如果您沒有定型的模型，您可以使用 [本教學](https://aka.ms/azml-deploy-cloud)課程中提供的模型和相依性檔案。
- [適用于 Machine Learning 服務的 Azure 命令列介面 (CLI) 擴充](reference-azure-machine-learning-cli.md)功能。

# <a name="python"></a>[Python](#tab/python)

- Azure Machine Learning 工作區。 如需詳細資訊，請參閱 [建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。
- 模型。 如果您沒有定型的模型，您可以使用 [本教學](https://aka.ms/azml-deploy-cloud)課程中提供的模型和相依性檔案。
- [適用于 Python 的 Azure Machine Learning 軟體發展工具組 (SDK) ](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)。

---

## <a name="connect-to-your-workspace"></a>連線到您的工作區

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

遵循 Azure CLI 檔中的指示來 [設定您的訂](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription)用帳戶內容。

然後，執行下列動作：

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

以查看您有權存取的工作區。

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

如需使用 SDK 連線至工作區的詳細資訊，請參閱 [適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true#&preserve-view=trueworkspace) 檔。


---


## <a name="register-your-model-optional"></a><a id="registermodel"></a> (選用) 註冊您的模型

已註冊的模型是組成模型的一或多個檔案所在的邏輯容器。 例如，如果您的模型儲存在多個檔案中，您可以在工作區中將其註冊為單一模型。 註冊檔案之後，您可以下載或部署已註冊的模型，並接收您註冊的所有檔案。

> [!TIP] 
> 建議為版本追蹤註冊模型，但不需要。 如果您想要在不註冊模型的情況下繼續進行，您將需要在 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py&preserve-view=true) 中指定來原始目錄或 [inferenceconfig.js](./reference-azure-machine-learning-cli.md#inference-configuration-schema) ，並確定您的模型位於該來原始目錄中。

> [!TIP]
> 當您註冊模型時，您可以從定型回合) 或本機目錄提供雲端位置 (的路徑。 此路徑只是為了在註冊過程中尋找要上傳的檔案。 它不需要符合輸入腳本中使用的路徑。 如需詳細資訊，請參閱 [在您的輸入腳本中找出模型](./how-to-deploy-advanced-entry-script.md#load-registered-models)檔案。

下列範例示範如何註冊模型。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>從 Azure ML 訓練回合註冊模型

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

`--asset-path`參數會參考模型的雲端位置。 在此範例中，會使用單一檔案的路徑。 若要在模型註冊中包含多個檔案，請將設定 `--asset-path` 為包含檔案的資料夾路徑。

### <a name="register-a-model-from-a-local-file"></a>從本機檔案註冊模型

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

若要在模型註冊中包含多個檔案，請將設定 `-p` 為包含檔案的資料夾路徑。

如需的詳細資訊 `az ml model register` ，請參閱 [參考檔](/cli/azure/ext/azure-cli-ml/ml/model)。

# <a name="python"></a>[Python](#tab/python)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>從 Azure ML 訓練回合註冊模型

  當您使用 SDK 來定型模型時，您可以根據定型模型的方式，接收 [執行](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&preserve-view=true) 物件或 [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) 物件。 每個物件都可以用來註冊實驗執行所建立的模型。

  + 從物件註冊模型 `azureml.core.Run` ：
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path`參數會參考模型的雲端位置。 在此範例中，會使用單一檔案的路徑。 若要在模型註冊中包含多個檔案，請將設定 `model_path` 為包含檔案的資料夾路徑。 如需詳細資訊，請參閱 [Run.register_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&preserve-view=true#&preserve-view=trueregister-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) 檔。

  + 從物件註冊模型 `azureml.train.automl.run.AutoMLRun` ：

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    在此範例中， `metric` `iteration` 不會指定和參數，因此將會註冊具有最佳主要度量的反復專案。 `model_id`會使用從執行傳回的值，而不是模型名稱。

    如需詳細資訊，請參閱 [AutoMLRun.register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) 檔。

### <a name="register-a-model-from-a-local-file"></a>從本機檔案註冊模型

您可以藉由提供模型的本機路徑來註冊模型。 您可以提供資料夾或單一檔案的路徑。 您可以使用這個方法來註冊以 Azure Machine Learning 定型的模型，然後下載。 您也可以使用這個方法來註冊在 Azure Machine Learning 外部定型的模型。

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

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

如需詳細資訊，請參閱 [模型類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true)的檔。

如需使用在 Azure Machine Learning 之外定型之模型的詳細資訊，請參閱 [如何部署現有的模型](how-to-deploy-existing-model.md)。

---

## <a name="define-an-entry-script"></a>定義專案腳本

[!INCLUDE [write entry script](../../includes/machine-learning-entry-script.md)]


## <a name="define-an-inference-configuration"></a>定義推斷設定


推斷設定描述如何設定包含模型的 web 服務。 稍後當您部署模型時，就會使用它。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

您可以撰寫基本的推斷設定，如下所示：

```json
{
    "entryScript": "score.py",
    "sourceDirectory": "./working_dir"
}
```

這會指定部署會使用 `score.py` 目錄中的檔案 `./working_dir` 來處理傳入的要求。

[請參閱這篇文章](./reference-azure-machine-learning-cli.md#inference-configuration-schema) ，以取得更詳盡的推斷設定討論。 

# <a name="python"></a>[Python](#tab/python)

下列範例示範：

1. 從您的工作區載入[策劃環境](resource-curated-environments.md)
1. 複製環境
1. 指定 `scikit-learn` 為相依性。
1. 使用環境建立 InferenceConfig

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


env = Environment.get(workspace, "AzureML-Minimal").clone(env_name)

for pip_package in ["scikit-learn"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=env)
```

如需環境的詳細資訊，請參閱 [建立和管理用於定型和部署的環境](how-to-use-environments.md)。

如需有關推斷設定的詳細資訊，請參閱 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py&preserve-view=true) 類別檔。

---

> [!TIP] 
> 如需有關使用具有推斷設定的自訂 Docker 映射的詳細資訊，請參閱 [如何使用自訂 docker 映射部署模型](how-to-deploy-custom-docker-image.md)。

## <a name="choose-a-compute-target"></a>選擇計算目標

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>定義部署設定

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

部署設定可用的選項會根據您所選擇的計算目標而有所不同。

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

如需詳細資訊，請參閱 [此參考](./reference-azure-machine-learning-cli.md#deployment-configuration-schema)。

# <a name="python"></a>[Python](#tab/python)

部署模型之前，您必須定義部署設定。 *部署設定是將裝載 web 服務的計算目標。* 例如，當您在本機部署模型時，您必須指定服務接受要求的埠。 部署設定不是您輸入腳本的一部分。 它是用來定義將裝載模型和專案腳本之計算目標的特性。

您也可能需要建立計算資源，例如，如果您還沒有 Azure Kubernetes Service (AKS 與您的工作區相關聯的) 實例。

下表提供建立每個計算目標之部署設定的範例：

| 計算目標 | 部署設定範例 |
| ----- | ----- |
| 本機 | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure 容器執行個體 | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

您可以從下列來源匯入本機、Azure 容器實例和 AKS web 服務的類別 `azureml.core.webservice` ：

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

---

## <a name="deploy-your-model"></a>部署模型

您現在已準備好部署您的模型。 

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

### <a name="using-a-registered-model"></a>使用已註冊的模型

如果您在 Azure Machine Learning 工作區中註冊模型，請以您的模型名稱和其版本號碼取代 "mymodel>： 1"。

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>使用本機模型

如果您不想要註冊您的模型，您可以在 inferenceconfig.js中傳遞 "sourceDirectory" 參數，以指定要用來為模型提供服務的本機目錄。

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json
```

# <a name="python"></a>[Python](#tab/python)

下列範例示範本機部署。 語法會根據您在上一個步驟中選擇的計算目標而有所不同。

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

如需詳細資訊，請參閱 [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py&preserve-view=true)、 [Model. 部署 ( # B1 ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)和 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py&preserve-view=true)的檔。

---

### <a name="understanding-service-state"></a>瞭解服務狀態

在模型部署期間，您可能會在完全部署時看到服務狀態變更。

下表說明不同的服務狀態：

| Webservice 狀態 | 描述 | 最終狀態？
| ----- | ----- | ----- |
| 過渡 | 服務正在進行部署。 | 否 |
| Unhealthy | 服務已部署，但目前無法連線。  | 否 |
| 設無法排程 | 因為缺少資源，所以目前無法部署服務。 | 否 |
| Failed | 因為發生錯誤或損毀，所以服務無法部署。 | 是 |
| Healthy | 服務狀況良好，且端點可供使用。 | 是 |


### <a name="batch-inference"></a><a id="azuremlcompute"></a> 批次推斷
Azure Machine Learning 計算目標是由 Azure Machine Learning 所建立和管理。 它們可用於從 Azure Machine Learning 管線進行批次預測。

如需使用 Azure Machine Learning 計算進行批次推斷的逐步解說，請參閱 [如何執行批次預測](tutorial-pipeline-batch-scoring-classification.md)。

### <a name="iot-edge-inference"></a><a id="iotedge"></a> IoT Edge 推斷
支援部署至 edge 的支援處於預覽階段。 如需詳細資訊，請參閱 [將 Azure Machine Learning 部署為 IoT Edge 模組](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)。

## <a name="delete-resources"></a>刪除資源

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

若要刪除已部署的 webservice，請使用 `az ml service <name of webservice>` 。

若要從您的工作區中刪除已註冊的模型，請使用 `az ml model delete <model id>`

深入瞭解如何 [刪除 webservice](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) 和 [刪除模型](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete)。

# <a name="python"></a>[Python](#tab/python)

若要刪除已部署的 Web 服務，請使用 `service.delete()`。
若要刪除已註冊的模型，請使用 `model.delete()`。

如需詳細資訊，請參閱 WebService 的檔集 [。刪除 ( # B1 ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedelete--) 和 [Model. 刪除 ( # B3 ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truedelete--)。

---


## <a name="next-steps"></a>後續步驟

* [針對失敗的部署進行疑難排解](how-to-troubleshoot-deployment.md)
* [部署到 Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [建立用戶端應用程式以使用 web 服務](how-to-consume-web-service.md)
* [更新 Web 服務](how-to-deploy-update-web-service.md)
* [如何使用自訂 Docker 映射部署模型](how-to-deploy-custom-docker-image.md)
* [使用 TLS 來透過 Azure Machine Learning 保護 Web 服務](how-to-secure-web-service.md)
* [使用 Application Insights 監視您的 Azure Machine Learning 模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)
* [建立模型部署的事件警示和觸發程式](how-to-use-event-grid.md)

