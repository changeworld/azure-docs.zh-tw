---
title: 部署疑難排解指南
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Kubernetes 服務和 Azure 容器實例使用 Azure 機器學習解決、解決和排除常見的 Docker 部署錯誤。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: fab46f7d7ae74ad643ce3f122b27b0dc767f5a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399677"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>故障排除 Azure 機器學習 Azure 庫伯奈斯服務和 Azure 容器實例部署

瞭解如何使用 Azure 機器學習使用 Azure 容器實例 （ACI） 和 Azure Kubernetes 服務 （AKS） 解決或解決常見的 Docker 部署錯誤。

在 Azure 機器學習中部署模型時，系統執行許多工。

模型部署的建議和最新方法是使用[環境](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments)物件作為輸入參數的[Model.deploy（）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) API。 在這種情況下，我們的服務將在部署階段為您創建基本 Docker 映射，並在一個調用中裝載所需的模型。 基本部署任務包括：

1. 在工作區模型登錄中註冊模型。

2. 定義推理配置：
    1. 基於您在環境 yaml 檔中指定的依賴項創建[環境](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments)物件，或使用我們採購的環境之一。
    2. 根據環境和評分腳本創建推理配置（推理設定物件）。

3. 將模型部署到 Azure 容器實例 （ACI） 服務或 Azure 庫伯奈斯服務 （AKS）。

在[模型管理](concept-model-management-and-deployment.md)簡介中深入了解此程序。

## <a name="prerequisites"></a>Prerequisites

* **Azure 訂閱**。 如果沒有，請嘗試[Azure 機器學習的免費或付費版本](https://aka.ms/AMLFree)。
* [Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
* [Azure 機器學習的 CLI 擴展。](reference-azure-machine-learning-cli.md)
* 要在本地調試，必須在本地系統上安裝工作 Docker。

    要驗證 Docker 安裝，請使用來自`docker run hello-world`終端或命令提示符的命令。 有關安裝 Docker 或排除 Docker 錯誤故障的資訊，請參閱[Docker 文檔](https://docs.docker.com/)。

## <a name="before-you-begin"></a>開始之前

如果您遇到任何問題時，首先要做的事就是將部署工作 (先前所述) 分成個別步驟，以將問題隔離。

假設您正在通過[Model.deploy（）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) API 使用新的/推薦的部署方法，將[環境](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments)物件作為輸入參數，則可以將代碼分解為三個主要步驟：

1. 註冊模型。 下面是一些示例代碼：

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. 定義部署的推理配置：

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. 使用上一步中創建的推理配置部署模型：

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

如果在將模型部署到 ACI 或 AKS 時遇到問題，請嘗試將其部署為本地 Web 服務。 使用本地 Web 服務可以更輕鬆地解決問題。 包含該模型的 Docker 映射在本地系統上下載並開始。

> [!WARNING]
> 生產方案不支援本地 Web 服務部署。

要在本地部署，請修改代碼以用於`LocalWebservice.deploy_configuration()`創建部署配置。 然後使用`Model.deploy()`來部署服務。 以下示例將模型（包含在模型變數中）部署為本地 Web 服務：

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

請注意，如果要定義自己的 conda 規範 YAML，則必須將版本>= 1.0.45 的 azureml 預設值列為點依賴項。 此套件包含將模型裝載為 Web 服務所需的功能。

此時，您可以正常使用該服務。 例如，以下代碼演示向服務發送資料：

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

有關自訂 Python 環境的詳細資訊，請參閱[創建和管理用於培訓和部署的環境](how-to-use-environments.md)。 

### <a name="update-the-service"></a>更新服務

在本地測試期間，您可能需要更新`score.py`檔以添加日誌記錄或嘗試解決已發現的任何問題。 要重新載入對檔的更改`score.py`，請使用`reload()`。 例如，以下代碼重新載入服務的腳本，然後將資料發送到該服務。 使用更新`score.py`的檔對資料進行評分：

> [!IMPORTANT]
> 該方法`reload`僅適用于本地部署。 有關將部署更新到其他計算目標的資訊，請參閱[部署模型](how-to-deploy-and-where.md#update)的更新部分。

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> 腳本從服務使用`InferenceConfig`的物件指定的位置重新載入。

要更改模型、Conda 依賴項或部署配置，請使用[update（）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-)。 以下示例更新服務使用的模型：

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>刪除服務

要刪除服務，請使用[delete（）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--)。

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a>檢查 Docker 日誌

您可以從服務物件中列印詳細的 Docker 引擎記錄訊息。 您可以查看 ACI、AKS 和本地部署的日誌。 下面的示例演示如何列印日誌。

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>服務啟動失敗

成功生成映射後，系統將嘗試使用部署配置啟動容器。 作為容器啟動程序的一部分，系統會叫用評分指令碼中的 `init()` 函式。 如果 `init()` 函式中有無法攔截的例外狀況，您可能會在錯誤訊息中看到 **CrashLoopBackOff** 錯誤。

使用["檢查 Docker 日誌](#dockerlog)"部分中的資訊檢查日誌。

## <a name="function-fails-get_model_path"></a>函式錯誤：get_model_path()

通常，在評分`init()`腳本中的函數中，調用[Model.get_model_path（）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-)函數來查找容器中的模型檔或模型檔的資料夾。 如果找不到模型檔或資料夾，則函數將失敗。 若要對此錯誤進行偵錯，最簡單方式是在容器殼層中執行下列 Python 程式碼：

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

此示例列印出容器中的本地路徑（相對於`/var/azureml-app`），其中計分腳本希望找到模型檔或資料夾。 然後您可以確認該檔案或資料夾是否確實是您需要的。

將日誌記錄級別設置為 DEBUG 可能會導致記錄其他資訊，這在識別故障時可能很有用。

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

**附註**：從 `run(input_data)` 呼叫傳回錯誤訊息的方式應僅用於偵錯目的。 出於安全原因，不應在生產環境中以這種方式返回錯誤訊息。

## <a name="http-status-code-502"></a>HTTP 狀態碼 502

502 狀態碼指示服務在score.py檔`run()`的方法中引發異常或崩潰。 使用本文中的資訊調試檔。

## <a name="http-status-code-503"></a>HTTP 狀態碼 503

Azure 庫伯奈斯服務部署支援自動縮放，允許添加副本以支援其他負載。 但是，自動縮放器旨在處理負載的**逐漸**變化。 如果每秒收到大量請求高峰，用戶端可能會收到 HTTP 狀態碼 503。

有兩件事情可以説明防止 503 狀態碼：

* 更改自動縮放創建新副本的利用率級別。
    
    預設情況下，自動縮放目標利用率設置為 70%，這意味著服務每秒可以處理高達 30% 的請求峰值 （RPS）。 您可以通過將 的值`autoscale_target_utilization`設置為較低的值來調整利用率目標。

    > [!IMPORTANT]
    > 此更改不會導致複製副本的創建*速度更快*。 而是在較低的利用率閾值創建它們。 而不是等到服務被利用 70%，將值更改為 30% 會導致在 30% 的利用率發生時棄置站台。
    
    如果 Web 服務已在使用當前最大副本，但仍看到 503 個狀態碼，則增加`autoscale_max_replicas`該值以增加副本的最大數量。

* 更改副本的最小數量。 增加最小副本可提供更大的池來處理傳入的峰值。

    要增加副本的最小數量，應`autoscale_min_replicas`設置為較高的值。 您可以使用以下代碼計算所需的副本，將值替換為特定于專案的值：

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
    > 如果您收到的請求峰值大於新最小副本可以處理的高峰，您可能會再次收到 503。 例如，隨著服務流量的增加，您可能需要增加最小副本。

有關`autoscale_target_utilization`設置`autoscale_max_replicas`和`autoscale_min_replicas`的詳細資訊，請參閱[AksWeb 服務](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py)模組引用。

## <a name="http-status-code-504"></a>HTTP 狀態碼 504

504 狀態碼指示請求已超時。預設超時為 1 分鐘。

您可以通過修改score.py來刪除不必要的呼叫來增加超時或嘗試加快服務速度。 如果這些操作不能解決問題，請使用本文中的資訊調試score.py檔。 代碼可能處於掛起狀態或無限迴圈。

## <a name="advanced-debugging"></a>高級調試

在某些情況下，您可能需要以對話模式調試模型部署中包含的 Python 代碼。 例如，如果條目腳本失敗，並且無法通過其他日誌記錄確定原因。 通過使用 Visual Studio 代碼和 Visual Studio 的 Python 工具 （PTVSD），可以附加到 Docker 容器內運行的代碼。

> [!IMPORTANT]
> 在本地使用`Model.deploy()`和`LocalWebservice.deploy_configuration`部署模型時，此方法的調試方法不起作用。 相反，您必須使用[Model.package（）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-)方法創建圖像。

本地 Web 服務部署需要在本地系統上安裝工作 Docker。 有關使用 Docker 的詳細資訊，請參閱[Docker 文檔](https://docs.docker.com/)。

### <a name="configure-development-environment"></a>設定開發環境

1. 要在本地 VS 代碼開發環境中安裝 Visual Studio 的 Python 工具 （PTVSD），請使用以下命令：

    ```
    python -m pip install --upgrade ptvsd
    ```

    有關將 PTVSD 與 VS 代碼一起使用的詳細資訊，請參閱[遠端偵錯](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)。

1. 要配置 VS 代碼以與 Docker 映射通信，請創建新的調試配置：

    1. 從 VS 代碼中，選擇__調試__功能表，然後選擇 __"打開配置__"。 將打開名為 __"啟動.json"__ 的檔。

    1. 在__啟動.json__檔中，查找包含 的`"configurations": [`行，並在它之後插入以下文本：

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
        > 如果配置部分中已有其他條目，請在插入的代碼後添加逗號 （，）。

        本節使用埠 5678 連接到 Docker 容器。

    1. 保存__啟動.json__檔。

### <a name="create-an-image-that-includes-ptvsd"></a>創建包含 PTVSD 的圖像

1. 修改部署的 conda 環境，以便它包含 PTVSD。 下面的示例演示了使用`pip_packages`參數添加它：

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

1. 要啟動 PTVSD 並在服務啟動時等待連接，請向`score.py`檔頂部添加以下內容：

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. 基於環境定義創建映射，並將映射拉至本地註冊表。 在調試期間，您可能希望對映射中的檔進行更改，而無需重新創建它。 要在 Docker 映射中安裝文字編輯器 （vim），請使用`Environment.docker.base_image``Environment.docker.base_dockerfile`和 屬性：

    > [!NOTE]
    > 此示例假定該`ws`位置指向 Azure 機器學習工作區，`model`即正在部署的模型。 該檔`myenv.yml`包含步驟 1 中創建的 conda 依賴項。

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

    創建和下載映射後，圖像路徑（包括存儲庫、名稱和標記（在本例中也是其摘要）將顯示在類似于以下內容的消息中：

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. 為了更輕鬆地處理圖像，請使用以下命令委任標記。 替換為`myimagepath`上一步驟中的位置值。

    ```bash
    docker tag myimagepath debug:1
    ```

    對於其餘步驟，可以將本地圖像稱為`debug:1`而不是完整圖像路徑值。

### <a name="debug-the-service"></a>調試服務

> [!TIP]
> 如果在`score.py`檔中為 PTVSD 連接設置了超時，則必須在超時過期之前將 VS 代碼連接到調試會話。 啟動 VS 代碼，打開 的`score.py`本機複本，設置中斷點，並在使用本節中的步驟之前準備好它。
>
> 有關調試和設置中斷點的詳細資訊，請參閱[調試](https://code.visualstudio.com/Docs/editor/debugging)。

1. 要使用映射啟動 Docker 容器，請使用以下命令：

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. 要將 VS 代碼附加到容器內的 PTVSD，請打開 VS 代碼並使用 F5 金鑰或選擇__調試__。 當出現提示時，選擇__Azure 機器學習：Docker 調試__配置。 您還可以從側欄中選擇調試圖示，__即 Azure 機器學習：Docker 調試__條目從"調試"下拉式功能表中，然後使用綠色箭頭附加調試器。

    ![調試圖示、啟動調試按鈕和配置選擇器](./media/how-to-troubleshoot-deployment/start-debugging.png)

此時，VS 代碼連接到 Docker 容器內的 PTVSD，並停在您以前設置的中斷點處。 現在，您可以在代碼運行時單一步驟代碼、查看變數等。

有關使用 VS 代碼調試 Python 的詳細資訊，請參閱[調試 Python 代碼](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019)。

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>修改容器檔案

要更改映射中的檔，可以附加到正在運行的容器，並執行 bash 外殼。 從那裡，您可以使用 vim 編輯檔：

1. 要連接到正在運行的容器並在容器中啟動 bash shell，請使用以下命令：

    ```bash
    docker exec -it debug /bin/bash
    ```

1. 要查找服務使用的檔，如果預設目錄與`/var/azureml-app`不同，請使用容器中的 bash shell 中的以下命令：

    ```bash
    cd /var/azureml-app
    ```

    在此處，您可以使用 vim 編輯`score.py`檔。 有關使用 vim 的詳細資訊，請參閱[使用 Vim 編輯器](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html)。

1. 容器的更改通常不會持久化。 要保存所做的任何更改，請使用以下命令，然後再退出在上述步驟中啟動的 shell（即在另一個 shell 中）：

    ```bash
    docker commit debug debug:2
    ```

    此命令創建一個名為的新`debug:2`圖像，其中包含您的編輯內容。

    > [!TIP]
    > 您需要停止當前容器，並在更改生效之前開始使用新版本。

1. 請確保對容器中的檔所做的更改與 VS Code 使用的本地檔保持同步。 否則，調試器體驗將不能按預期工作。

### <a name="stop-the-container"></a>停止容器

要停止容器，請使用以下命令：

```bash
docker stop debug
```

## <a name="next-steps"></a>後續步驟

深入了解部署：

* [部署方式及位置](how-to-deploy-and-where.md)
* [教程：培訓&部署模型](tutorial-train-models-with-aml.md)
