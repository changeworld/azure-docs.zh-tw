---
title: 針對遠端 Web 服務部署進行疑難排解
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Kubernetes Service 和 Azure 容器實例來解決常見的 Docker 部署錯誤，並對其進行疑難排解。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.reviewer: jmartens
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: contperf-fy20q4, devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: 92cd70e864ae0490ce3f9e7435d9518241f93c8e
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031499"
---
# <a name="troubleshoot-model-deployment"></a>針對模型部署進行疑難排解

瞭解如何針對 Azure 容器實例 (ACI) 和 Azure Kubernetes Service (AKS) 使用 Azure Machine Learning，進行疑難排解和解決，或解決這些常見的遠端 Docker 部署錯誤。

## <a name="prerequisites"></a>必要條件

* **Azure 訂用帳戶**。 試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。
* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)。
* [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)。
* [適用於 Azure Machine Learning 的 CLI 擴充功能](reference-azure-machine-learning-cli.md)。

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>Docker 部署機器學習模型的步驟

當您在 Azure Machine Learning 中將模型部署至非本機計算時，會發生下列情況：

1. 您在 InferenceConfig 中的環境物件中指定的 Dockerfile 會傳送至雲端，以及來原始目錄的內容。
1. 如果您的容器登錄中無法使用先前建立的映射，則會在雲端中建立新的 Docker 映射，並將其儲存在您工作區的預設容器登錄中。
1. 容器登錄中的 Docker 映射會下載至您的計算目標。
1. 您工作區的預設 Blob 存放區會掛接到您的計算目標，讓您能夠存取已註冊的模型
1. 藉由執行輸入腳本的函式來初始化您的 web 伺服器 `init()`
1. 當您已部署的模型收到要求時，您的函式 `run()` 會處理該要求

使用本機部署時的主要差異在於，容器映射是在本機電腦上建立的，這也是為什麼您需要安裝 Docker 以進行本機部署的原因。

瞭解這些高階步驟應可協助您瞭解發生錯誤的位置。

## <a name="get-deployment-logs"></a>取得部署記錄

偵錯工具錯誤的第一個步驟是取得您的部署記錄。 首先，請遵循 [此處的指示](how-to-deploy-and-where.md#connect-to-your-workspace) 來連接至您的工作區。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

若要從已部署的 webservice 取得記錄，請執行：

```bash
az ml service get-logs --verbose --workspace-name <my workspace name> --name <service name>
```

# <a name="python"></a>[Python](#tab/python)


假設您有一個名為的 `azureml.core.Workspace` 物件 `ws` ，您可以執行下列動作：

```python
print(ws.webservices)

# Choose the webservice you are interested in

from azureml.core import Webservice

service = Webservice(ws, '<insert name of webservice>')
print(service.get_logs())
```

---

## <a name="debug-locally"></a>在本機執行偵錯

如果您在將模型部署到 ACI 或 AKS 時遇到問題，請將它部署為本機 web 服務。 使用本機 Web 服務可讓您更輕鬆地針對問題進行疑難排解。 若要在本機針對部署進行疑難排解，請參閱 [本機疑難排解文章](./how-to-troubleshoot-deployment-local.md)。

## <a name="container-cannot-be-scheduled"></a>無法排程容器

將服務部署至 Azure Kubernetes Service 計算目標時，Azure Machine Learning 會嘗試使用要求的資源量來排程服務。 如果叢集中沒有任何節點在5分鐘後具有適當的資源數量，部署將會失敗。 失敗訊息為 `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` 。 您可以藉由新增更多節點、變更節點的 SKU，或變更服務的資源需求，來解決此錯誤。 

錯誤訊息通常會指出您需要更多的資源，例如，如果您看到錯誤訊息，表示 `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` 服務需要 gpu，且叢集中有三個節點沒有可用的 gpu。 如果您使用 GPU SKU，請新增更多節點來解決此問題，如果您不是，請切換至已啟用 GPU 的 SKU，或將環境變更為不需要 GPU。  

## <a name="service-launch-fails"></a>服務啟動失敗

成功建置映像後，系統就會嘗試使用您的部署組態啟動容器。 作為容器啟動程序的一部分，系統會叫用評分指令碼中的 `init()` 函式。 如果 `init()` 函式中有無法攔截的例外狀況，您可能會在錯誤訊息中看到 **CrashLoopBackOff** 錯誤。

使用 [ [檢查 Docker 記錄](how-to-troubleshoot-deployment-local.md#dockerlog) 檔] 文章中的資訊。

## <a name="function-fails-get_model_path"></a>函式錯誤：get_model_path()

通常，在評分指令碼 [Model.get_model_path()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) 的 `init()` 函式中，呼叫該函式是為了找出容器中的模型檔案或模型檔案資料夾。 如果找不到模型檔案或資料夾，則函式會失敗。 若要對此錯誤進行偵錯，最簡單方式是在容器殼層中執行下列 Python 程式碼：

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

如需有關設定 `autoscale_target_utilization`、`autoscale_max_replicas` 和 `autoscale_min_replicas` 的詳細資訊，請參閱 [AksWebservice](/python/api/azureml-core/azureml.core.webservice.akswebservice?preserve-view=true&view=azure-ml-py) 模組參考。

## <a name="http-status-code-504"></a>HTTP 狀態碼 504

504 狀態碼表示要求已逾時。預設的逾時值為 1 分鐘。

您可以修改 score.py 來移除不必要的呼叫，以增加逾時時間或嘗試加快服務速度。 如果這些動作無法修正問題，請使用本文中的資訊來偵錯 score.py 檔案。 程式碼可能處於無回應狀態或無限迴圈。

## <a name="advanced-debugging"></a>進階偵錯

您可能需要以互動方式來對模型部署中包含的 Python 程式碼進行偵錯。 例如，如果輸入腳本失敗，而且無法由其他記錄來判斷原因。 藉由使用 Visual Studio Code 和 debugpy，您可以附加至在 Docker 容器內執行的程式碼。

如需詳細資訊，請造訪 [VS Code 指南中的互動式調試](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments)程式。

## <a name="model-deployment-user-forum"></a>[模型部署使用者論壇](/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>後續步驟

深入了解部署：

* [部署方式及位置](how-to-deploy-and-where.md)
* [教學課程：定型與部署模型](tutorial-train-models-with-aml.md)
* [如何在本機執行和調試實驗](./how-to-debug-visual-studio-code.md)