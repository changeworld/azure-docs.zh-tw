---
title: Docker 部署疑難排解
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning Azure Kubernetes Service 和 Azure 容器實例來解決、解決和疑難排解常見的 Docker 部署錯誤。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 08/06/2020
ms.topic: conceptual
ms.custom: troubleshooting, contperfq4, devx-track-python, deploy
ms.openlocfilehash: 7ed025bc5b7e59a28fcb2f03eeea56628e2cde0d
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91998620"
---
# <a name="troubleshoot-docker-deployment-of-models-with-azure-kubernetes-service-and-azure-container-instances"></a>使用 Azure Kubernetes Service 和 Azure 容器實例針對模型的 Docker 部署進行疑難排解 

瞭解如何針對 Azure 容器實例 (ACI) 和 Azure Kubernetes Service (AKS) 使用 Azure Machine Learning，來進行疑難排解並解決問題，或解決這些常見的 Docker 部署錯誤。

## <a name="prerequisites"></a>必要條件

* **Azure 訂用帳戶**。 試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)。
* [適用於 Azure Machine Learning 的 CLI 擴充功能](reference-azure-machine-learning-cli.md)。
* 若要在本機偵錯，您必須在本機系統上擁有正常運作的 Docker 安裝。

    若要驗證您的 Docker 安裝，請從終端或命令提示字元使用命令 `docker run hello-world`。 如需有關安裝 Docker 或針對 Docker 錯誤進行疑難排解的資訊，請參閱 [Docker 文件](https://docs.docker.com/)。

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>Docker 部署機器學習模型的步驟

在 Azure Machine Learning 中部署模型時，您會使用 [模型。部署 ( # B1 ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) API 和 [環境](how-to-use-environments.md) 物件。 服務會在部署階段建立基底 docker 映射，並在單一呼叫中裝載所需的模型。 基礎部署工作包含：

1. 在工作區模型登錄中註冊模型。

2. 定義推斷組態：
    1. 建立 [環境](how-to-use-environments.md) 物件。 此物件可以使用環境 yaml 檔中的相依性，這是我們其中一個策劃環境。
    2. 根據環境和評分指令碼建立推斷組態 (InferenceConfig 物件)。

3. 將模型部署至 Azure 容器執行個體 (ACI) 服務或 Azure Kubernetes Service (AKS)。

在[模型管理](concept-model-management-and-deployment.md)簡介中深入了解此程序。

## <a name="before-you-begin"></a>開始之前

如果您遇到任何問題時，首先要做的事就是將部署工作 (先前所述) 分成個別步驟，以將問題隔離。

使用 Model 時，請使用[環境](how-to-use-environments.md)物件將[ ( # B1 部署](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)為輸入參數，您的程式碼可以分為三個主要步驟：

1. 註冊模型。 以下是一些範例程式碼：

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. 定義部署推斷組態：

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. 使用在上一個步驟中建立的推斷組態來部署模型：

    ```python
    from azureml.core.webservice import AciWebservice


    # deploy the model
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)
    aci_service = Model.deploy(workspace=ws,
                           name='my-service',
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aci_config)
    aci_service.wait_for_deployment(show_output=True)
    ```

將模型下列部署程式細分為個別的工作，可讓您更輕鬆地識別一些較常見的錯誤。

## <a name="debug-locally"></a>在本機執行偵錯

如果您在將模型部署到 ACI 或 AKS 時遇到問題，請將它部署為本機 web 服務。 使用本機 Web 服務可讓您更輕鬆地針對問題進行疑難排解。

您可以在[MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks)存放庫中找到範例[本機部署筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb)，以探索可執行檔範例。

> [!WARNING]
> 針對生產案例，不支援本機 Web 服務部署。

若要在本機部署，請修改您的程式碼，以使用 `LocalWebservice.deploy_configuration()` 建立部署組態。 然後使用 `Model.deploy()` 來部署服務。 下列範例會將模型 (包含在模型變數中) 部署為本機 Web 服務：

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

如果您要定義自己的 conda 規格 YAML，請列出 azureml-預設版本 >= 1.0.45 版 azureml-defaults 作為 pip 相依性。 需要此套件才能將模型裝載為 web 服務。

此時，您可以照常使用服務。 下列程式碼示範如何將資料傳送至服務：

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

如需自訂 Python 環境的詳細資訊，請參閱[建立和管理用於訓練和部署的環境](how-to-use-environments.md)。 

### <a name="update-the-service"></a>更新服務

在本機測試期間，您可能需要更新 `score.py` 檔案，以新增記錄或嘗試解決您發現的任何問題。 若要重新載入 `score.py` 檔案的變更，請使用 `reload()`。 例如，下列程式碼會重新載入服務的指令碼，然後將資料過去。 資料會使用更新後的 `score.py` 檔案進行評分：

> [!IMPORTANT]
> `reload` 方法僅可用於本機部署。 如需將部署更新至另一個計算目標的相關資訊，請參閱 [如何更新您的 webservice](how-to-deploy-update-web-service.md)。

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> 此指令碼會從服務所使用的 `InferenceConfig` 物件指定的位置重新載入。

若要變更模型、Conda 相依性或部署組態，請使用 [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=trueupdate--args-)。 下列範例會更新服務所使用的模型：

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>刪除服務

要刪除服務，請使用 [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedelete--)。

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> 檢查 Docker 記錄

您可以從服務物件中列印詳細的 Docker 引擎記錄訊息。 您可以檢視 ACI、AKS 和本機部署記錄。 下列範例示範如何列印記錄。

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```
如果您 `Booting worker with pid: <pid>` 在記錄中看到一行出現多次，則表示沒有足夠的記憶體可啟動背景工作。
您可以藉由增加中的值來解決錯誤。 `memory_gb``deployment_config`
 
## <a name="container-cannot-be-scheduled"></a>無法排程容器

將服務部署至 Azure Kubernetes Service 計算目標時，Azure Machine Learning 會嘗試使用要求的資源量來排程服務。 如果叢集中沒有任何節點在5分鐘後具有適當的資源數量，部署將會失敗。 失敗訊息為 `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` 。 您可以藉由新增更多節點、變更節點的 SKU，或變更服務的資源需求，來解決此錯誤。 

錯誤訊息通常會指出您需要更多的資源，例如，如果您看到錯誤訊息，表示 `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` 服務需要 gpu，且叢集中有三個節點沒有可用的 gpu。 如果您使用 GPU SKU，請新增更多節點來解決此問題，如果您不是，請切換至已啟用 GPU 的 SKU，或將環境變更為不需要 GPU。  

## <a name="service-launch-fails"></a>服務啟動失敗

成功建置映像後，系統就會嘗試使用您的部署組態啟動容器。 作為容器啟動程序的一部分，系統會叫用評分指令碼中的 `init()` 函式。 如果 `init()` 函式中有無法攔截的例外狀況，您可能會在錯誤訊息中看到 **CrashLoopBackOff** 錯誤。

請使用[檢查 Docker 記錄](#dockerlog)一節中的資訊來檢查記錄。

## <a name="function-fails-get_model_path"></a>函式錯誤：get_model_path()

通常，在評分指令碼 [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) 的 `init()` 函式中，呼叫該函式是為了找出容器中的模型檔案或模型檔案資料夾。 如果找不到模型檔案或資料夾，則函式會失敗。 若要對此錯誤進行偵錯，最簡單方式是在容器殼層中執行下列 Python 程式碼：

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

此範例會列印出容器中的本機路徑 (相對於 `/var/azureml-app`)，而您的評分指令碼預期會在其中找到模型檔案或資料夾。 然後您可以確認該檔案或資料夾是否確實是您需要的。

將記錄層級設定為 [偵錯] 可記錄額外資訊，這在要找出失敗原因時可能很實用。

## <a name="function-fails-runinput_data"></a>函式失敗：run(input_data)

如果已成功部署服務，但此服務在您發佈資料到評分端點時發生損毀，您可以在 `run(input_data)` 函式中新增錯誤攔截陳述式，以傳回詳細的錯誤訊息。 例如：

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**注意**：從 `run(input_data)` 呼叫傳回錯誤訊息的方式應僅用於偵錯目的。 基於安全性理由，您不應該在生產環境中以這種方式傳回錯誤訊息。

## <a name="http-status-code-502"></a>HTTP 狀態碼 502

502 狀態碼表示服務已在 score.py 檔案的 `run()` 方法中擲回例外狀況或損毀。 使用本文中的資訊來偵錯檔案。

## <a name="http-status-code-503"></a>HTTP 狀態碼 503

Azure Kubernetes Service 部署支援自動調整，可讓您新增複本以支援額外的負載。 自動調整程式是設計來處理負載中的 **漸進** 式變更。 如果您每秒收到非常大量的要求數，用戶端可能會收到 HTTP 狀態碼 503。 即使自動調整程式迅速做出反應，還是需要 AKS 很長的時間來建立額外的容器。

相應增加/減少的決策是以目前容器複本的使用量為基礎。 處理要求時忙碌 (的複本數目) 除以目前複本的總數目是目前的使用率。 如果此數位超過 `autoscale_target_utilization` ，則會建立更多複本。 如果較低，則會降低複本。 新增複本的決策是立即且快速 (大約1秒的) 。 移除複本的決策是保守 (大約1分鐘的) 。 根據預設，自動調整目標使用率會設定為 **70%**，這表示服務可以處理每秒要求的尖峰 (RPS) （ **最多 30%**）。

有兩個方法可協助防止出現 503 狀態碼：

> [!TIP]
> 這兩種方法可以個別使用或合併使用。

* 變更自動調整建立新複本的使用率層級。 您可以將 `autoscale_target_utilization` 設定為較低的值，來調整使用率目標。

    > [!IMPORTANT]
    > 此變更不會「加快」建立複本的速度。 相反地，其會以較低的使用率閾值建立複本。 若不等到服務使用率達 70% 就將值變更為 30%，會在使用率達 30% 時建立複本。
    
    如果 Web 服務已使用目前的最大複本，而您仍看到 503 狀態碼，請增加 `autoscale_max_replicas` 值以增加複本的最大數目。

* 變更最低複本數目。 增加最少複本數可提供較大的集區來處理傳入尖峰。

    若要增加最少的複本數目，請將 `autoscale_min_replicas` 設定為較高的值。 您可以使用下列程式碼來計算所需的複本數，並使用專案的特定值來取代這些值：

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > 如果您收到的要求尖峰超過新的最小複本可以處理的數目，您可能會再次收到 503 狀態碼。 例如，隨著服務的流量增加，您可能需要增加最小複本數。

如需有關設定 `autoscale_target_utilization`、`autoscale_max_replicas` 和 `autoscale_min_replicas` 的詳細資訊，請參閱 [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true) 模組參考。

## <a name="http-status-code-504"></a>HTTP 狀態碼 504

504 狀態碼表示要求已逾時。預設的逾時值為 1 分鐘。

您可以修改 score.py 來移除不必要的呼叫，以增加逾時時間或嘗試加快服務速度。 如果這些動作無法修正問題，請使用本文中的資訊來偵錯 score.py 檔案。 程式碼可能處於無回應狀態或無限迴圈。

## <a name="advanced-debugging"></a>進階偵錯

您可能需要以互動方式對模型部署中包含的 Python 程式碼進行偵測。 例如，如果輸入腳本失敗，而且無法由其他記錄來判斷原因。 藉由使用 Visual Studio Code 和 debugpy，您可以附加至在 Docker 容器內執行的程式碼。

如需詳細資訊，請造訪 [VS Code 指南中的互動式調試](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments)程式。

## <a name="model-deployment-user-forum"></a>[模型部署使用者論壇](https://docs.microsoft.com/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>後續步驟

深入了解部署：

* [部署方式及位置](how-to-deploy-and-where.md)
* [教學課程：定型與部署模型](tutorial-train-models-with-aml.md)
