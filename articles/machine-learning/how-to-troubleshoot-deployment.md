---
title: Docker 部署疑難排解
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning Azure Kubernetes Service 和 Azure 容器實例來解決、解決常見的 Docker 部署錯誤，並對其進行疑難排解。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 03/05/2020
ms.topic: conceptual
ms.custom: troubleshooting, contperfq4, tracking-python
ms.openlocfilehash: dcb2a50a91bec70dfe5d9adda7518f3510a8c973
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373193"
---
# <a name="troubleshoot-docker-deployment-of-models-with-azure-kubernetes-service-and-azure-container-instances"></a>針對使用 Azure Kubernetes Service 和 Azure 容器實例的模型進行 Docker 部署進行疑難排解 

瞭解如何使用 Azure Machine Learning，針對 Azure 容器實例（ACI）和 Azure Kubernetes Service （AKS）的常見 Docker 部署錯誤進行疑難排解和解決或解決。

## <a name="prerequisites"></a>必要條件

* **Azure 訂用帳戶**。 如果您沒有訂用帳戶，則可[試用免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
* [適用於 Azure Machine Learning 的 CLI 擴充功能](reference-azure-machine-learning-cli.md)。
* 若要在本機偵錯，您必須在本機系統上擁有正常運作的 Docker 安裝。

    若要驗證您的 Docker 安裝，請從終端或命令提示字元使用命令 `docker run hello-world`。 如需有關安裝 Docker 或針對 Docker 錯誤進行疑難排解的資訊，請參閱 [Docker 文件](https://docs.docker.com/)。

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>Docker 部署機器學習模型的步驟

在 Azure Machine Learning 中部署模型時，系統就會執行數項工作。

模型部署的建議方法是透過[model. deploy （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) API，使用[環境](how-to-use-environments.md)物件做為輸入參數。 在此情況下，服務會在部署階段建立基底 docker 映射，並在一次呼叫中裝載所需的模型。 基礎部署工作包含：

1. 在工作區模型登錄中註冊模型。

2. 定義推斷組態：
    1. 根據您在環境 YAML 檔中指定的相依性，建立[環境](how-to-use-environments.md)物件，或使用我們的其中一個環境。
    2. 根據環境和評分指令碼建立推斷組態 (InferenceConfig 物件)。

3. 將模型部署至 Azure 容器執行個體 (ACI) 服務或 Azure Kubernetes Service (AKS)。

在[模型管理](concept-model-management-and-deployment.md)簡介中深入了解此程序。

## <a name="before-you-begin"></a>開始之前

如果您遇到任何問題時，首先要做的事就是將部署工作 (先前所述) 分成個別步驟，以將問題隔離。

假設您透過 [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) API 與[環境](how-to-use-environments.md)物件作為輸入參數，並使用新的/建議方法，您的程式碼可以分成三個主要步驟：

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

將部署程序分成個別的工作後，我們可以查看一些最常見的錯誤。

## <a name="debug-locally"></a>在本機執行偵錯

如果您在將模型部署至 ACI 或 AKS 時遇到問題，請嘗試將其部署為本機 Web 服務。 使用本機 Web 服務可讓您更輕鬆地針對問題進行疑難排解。 系統會下載包含模型的 Docker 映像並在您的本機系統上啟動。

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

如果您要定義自己的 conda 規格 YAML，您必須以 pip 相依性的版本 >= 1.0.45 來列出 azureml-預設值。 此套件包含將模型裝載為 Web 服務所需的功能。

此時，您可以照常使用服務。 例如，下列程式碼示範如何將資料傳送至服務：

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
> `reload` 方法僅可用於本機部署。 如需將部署更新至另一個計算目標的詳細資訊，請參閱[部署模型](how-to-deploy-and-where.md#update)的更新一節。

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> 此指令碼會從服務所使用的 `InferenceConfig` 物件指定的位置重新載入。

若要變更模型、Conda 相依性或部署組態，請使用 [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-)。 下列範例會更新服務所使用的模型：

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>刪除服務

要刪除服務，請使用 [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--)。

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> 檢查 Docker 記錄

您可以從服務物件中列印詳細的 Docker 引擎記錄訊息。 您可以檢視 ACI、AKS 和本機部署記錄。 下列範例示範如何列印記錄。

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```
如果您在 `Booting worker with pid: <pid>` 記錄中看到出現多次的行，則表示沒有足夠的記憶體可啟動背景工作。
您可以藉由增加中的值來解決此錯誤 `memory_gb``deployment_config`
 
## <a name="container-cannot-be-scheduled"></a>無法排程容器

將服務部署至 Azure Kubernetes Service 計算目標時，Azure Machine Learning 會嘗試使用要求的資源量來排程服務。 如果在5分鐘之後，叢集中沒有可用的適當資源量的節點，部署將會失敗並顯示訊息 `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` 。 您可以藉由新增更多節點、變更節點的 SKU 或變更服務的資源需求，來解決這個錯誤。 

錯誤訊息通常會指出您需要更多的資源-例如，如果您看到一則錯誤訊息，表示 `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` 服務需要 gpu，且叢集中有三個節點沒有可用的 gpu。 如果您使用 GPU SKU，請新增更多節點來解決此問題，如果您不是，請切換至已啟用 GPU 的 SKU，或將環境變更為不需要 GPU。  

## <a name="service-launch-fails"></a>服務啟動失敗

成功建置映像後，系統就會嘗試使用您的部署組態啟動容器。 作為容器啟動程序的一部分，系統會叫用評分指令碼中的 `init()` 函式。 如果 `init()` 函式中有無法攔截的例外狀況，您可能會在錯誤訊息中看到 **CrashLoopBackOff** 錯誤。

請使用[檢查 Docker 記錄](#dockerlog)一節中的資訊來檢查記錄。

## <a name="function-fails-get_model_path"></a>函式錯誤：get_model_path()

通常，在評分指令碼 [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) 的 `init()` 函式中，呼叫該函式是為了找出容器中的模型檔案或模型檔案資料夾。 如果找不到模型檔案或資料夾，則函式會失敗。 若要對此錯誤進行偵錯，最簡單方式是在容器殼層中執行下列 Python 程式碼：

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

Azure Kubernetes Service 部署支援自動調整，可讓您新增複本以支援額外的負載。 不過，自動調整程式的設計訴求是處理負載中**細微**的變更。 如果您每秒收到非常大量的要求數，用戶端可能會收到 HTTP 狀態碼 503。

有兩個方法可協助防止出現 503 狀態碼：

* 變更自動調整建立新複本的使用率層級。
    
    根據預設，自動調整目標使用率會設定為 70%，這表示服務可以處理突然增加的每秒要求數 (RPS)，最高可達 30%。 您可以將 `autoscale_target_utilization` 設定為較低的值，來調整使用率目標。

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

如需有關設定 `autoscale_target_utilization`、`autoscale_max_replicas` 和 `autoscale_min_replicas` 的詳細資訊，請參閱 [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) 模組參考。

## <a name="http-status-code-504"></a>HTTP 狀態碼 504

504 狀態碼表示要求已逾時。預設的逾時值為 1 分鐘。

您可以修改 score.py 來移除不必要的呼叫，以增加逾時時間或嘗試加快服務速度。 如果這些動作無法修正問題，請使用本文中的資訊來偵錯 score.py 檔案。 程式碼可能處於未回應狀態或無限迴圈。

## <a name="advanced-debugging"></a>進階偵錯

在某些情況下，您可能需要以互動方式來對模型部署中包含的 Python 程式碼進行偵錯。 例如，如果輸入腳本失敗，而且無法由其他記錄來判斷原因。 藉由使用 Visual Studio Code 和適用於 Visual Studio 的 Python 工具 (PTVSD)，您可以附加至在 Docker 容器內執行的程式碼。

> [!IMPORTANT]
> 使用 `Model.deploy()` 和 `LocalWebservice.deploy_configuration` 在本機部署模型時，無法使用這種偵錯方法。 相反地，您必須使用 [Model.package()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-) 方法來建立映像。

本機 Web 服務部署需要在您的本機系統上執行正常的 Docker 安裝。 如需使用 Docker 的詳細資訊，請參閱 [Docker Azure 文件](https://docs.docker.com/)。

### <a name="configure-development-environment"></a>設定開發環境

1. 若要在您的本機 VS Code 開發環境上安裝適用於 Visual Studio 的 Python 工具 (PTVSD)，請使用下列命令：

    ```
    python -m pip install --upgrade ptvsd
    ```

    如需搭配 VS Code 使用 PTVSD 的詳細資訊，請參閱[遠端偵錯](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)。

1. 若要設定 VS Code 以與 Docker 映像通訊，請建立新的偵錯組態：

    1. 從 VS Code 選取 [偵錯] 功能表，然後選取 [開啟組態]。 隨即開啟名為 launch.json 的檔案。

    1. 在 launch.json 檔案中，尋找包含 `"configurations": [` 的那一行，並在其後插入下列文字：

        ```json
        {
            "name": "Azure Machine Learning: Docker Debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "localhost",
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > 如果組態區段中已經有其他項目，請在您插入的程式碼後面新增逗號 (,)。

        本節會使用連接埠 5678 附加至 Docker 容器。

    1. 儲存 launch.json檔案。

### <a name="create-an-image-that-includes-ptvsd"></a>建立包含 PTVSD 的映像

1. 修改部署的 Conda 環境，使其包含 PTVSD。 以下範例示範如何使用 `pip_packages` 參數新增：

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.45', 'ptvsd'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. 若要在服務啟動時啟動 PTVSD 並等候連線，請將下列內容新增至 `score.py` 檔案的頂端：

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. 建立以環境定義為基礎的映像，並將映像提取到本機登錄。 在偵錯過程中，建議您變更映像中的檔案，而不需要重新建立。 若要在 Docker 映像中安裝文字編輯器 (vim)，請使用 `Environment.docker.base_image` 和 `Environment.docker.base_dockerfile` 屬性：

    > [!NOTE]
    > 這個範例假設 `ws` 指向您的 Azure Machine Learning 工作區，而且該 `model` 是要部署的模型。 `myenv.yml` 檔案包含在步驟 1 中建立的 Conda 相依性。

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04\nRUN apt-get update && apt-get install vim -y"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    建立並下載映像之後，映像路徑 (在此案例中也會包含存放庫、名稱和標籤，也就是其摘要) 會顯示在類似下列的訊息中：

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. 若要更輕鬆地使用映像，請使用下列命令來新增標籤。 使用先前步驟中的位置值取代 `myimagepath`。

    ```bash
    docker tag myimagepath debug:1
    ```

    在其餘的步驟中，您可以將本機映像參照為 `debug:1`，而不是完整的映像路徑值。

### <a name="debug-the-service"></a>服務偵錯

> [!TIP]
> 如果您在 `score.py` 檔案中設定 PTVSD 連線的逾時時間，則必須在逾時時間到期之前，將 VS Code 連線到偵錯工作階段。 啟動 VS Code 並開啟 `score.py` 的本機複本，然後設定中斷點，並在使用本節中的步驟之前做好準備。
>
> 如需有關偵錯和設定中斷點的詳細資訊，請參閱[偵錯](https://code.visualstudio.com/Docs/editor/debugging)。

1. 若要使用映像啟動 Docker 容器，請使用下列命令：

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. 若要將 VS Code 附加至容器內的 PTVSD，請開啟 VS Code 並使用 F5 鍵，或選取 [偵錯]。 出現提示時，選取平台 Azure Machine Learning：Socker 偵錯組態。 您也可以從側邊列選取偵錯圖示，位於 Azure Machine Learning：Docker 偵錯項目 (偵錯下拉式功能表中) 中，然後使用綠色箭頭來附加偵錯工具。

    ![偵錯圖示、啟動偵錯按鈕和組態選取器](./media/how-to-troubleshoot-deployment/start-debugging.png)

此時，VS Code 會連線到 Docker 容器內的 PTVSD，並在您先前設定的中斷點停止。 您現在可以在程式碼執行時逐步執行、檢視變數等。

如需使用 VS Code 來偵錯 Python 的詳細資訊，請參閱[偵錯您的 Python 程式碼](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019)。

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>修改容器檔案

若要變更映像中的檔案，您可以附加至執行中的容器，並執行 Bash 殼層。 您可以從該處使用 VIM 來編輯檔案：

1. 若要連線到執行中的容器，並在容器中啟動 Bash 殼層，請使用下列命令：

    ```bash
    docker exec -it debug /bin/bash
    ```

1. 若要尋找服務所使用的檔案，如果預設目錄與 `/var/azureml-app`不同，請從容器中的 Bash 殼層使用下列命令：

    ```bash
    cd /var/azureml-app
    ```

    您可以從該處使用 VIM 來編輯 `score.py` 檔案。 如需有關使用 VIM 的詳細資訊，請參閱[使用 VIM 編輯器](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html)。

1. 容器的變更通常不會保存。 若要儲存您所做的所有變更，請在您結束上述步驟 (也就是另一個殼層) 啟動的殼層之前使用下列命令：

    ```bash
    docker commit debug debug:2
    ```

    此命令會建立名為 `debug:2` 的新映像，其中包含您的編輯內容。

    > [!TIP]
    > 您必須停止目前的容器，並開始使用新的版本，變更才會生效。

1. 請務必將您對容器中檔案所做的變更，與 VS Code 使用的本機檔案保持同步。 否則，偵錯工具體驗將無法如預期般運作。

### <a name="stop-the-container"></a>停止容器

若要停止容器，請使用下列命令：

```bash
docker stop debug
```

## <a name="next-steps"></a>後續步驟

深入了解部署：

* [部署方式及位置](how-to-deploy-and-where.md)
* [教學課程：定型與部署模型](tutorial-train-models-with-aml.md)
