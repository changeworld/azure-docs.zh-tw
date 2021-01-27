---
title: 疑難排解本機模型部署
titleSuffix: Azure Machine Learning
description: 在對模型部署錯誤進行疑難排解的第一個步驟中，請嘗試本機模型部署。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.reviewer: luquinta
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: 473b2e4ac3443d204b39331d9cefaa6c71122439
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881664"
---
# <a name="troubleshooting-with-a-local-model-deployment"></a>使用本機模型部署進行疑難排解

嘗試本機模型部署，作為針對 Azure 容器實例的部署進行疑難排解的第一個步驟， (ACI) 或 Azure Kubernetes Service (AKS) 。  使用本機 web 服務可讓您更輕鬆地找出並修正常見的 Azure Machine Learning Docker web 服務部署錯誤。

## <a name="prerequisites"></a>必要條件

* **Azure 訂用帳戶**。 試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。
* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)。
* [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)。
* [適用於 Azure Machine Learning 的 CLI 擴充功能](reference-azure-machine-learning-cli.md)。
* 若要在本機進行偵錯工具，您可以將模型部署到 [Azure Machine Learning 計算實例](./how-to-deploy-local-container-notebook-vm.md) ，或在您的本機系統上具有可運作的 Docker 安裝。

    若要驗證您的 Docker 安裝，請從終端或命令提示字元使用命令 `docker run hello-world`。 如需有關安裝 Docker 或針對 Docker 錯誤進行疑難排解的資訊，請參閱 [Docker 文件](https://docs.docker.com/)。

## <a name="debug-locally"></a>在本機執行偵錯

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

若要變更模型、Conda 相依性或部署組態，請使用 [update()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueupdate--args-)。 下列範例會更新服務所使用的模型：

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>刪除服務

要刪除服務，請使用 [delete()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--)。

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

## <a name="next-steps"></a>後續步驟

深入了解部署：

* [如何針對遠端部署進行疑難排解](how-to-troubleshoot-deployment.md)
* [部署方式及位置](how-to-deploy-and-where.md)
* [教學課程：定型與部署模型](tutorial-train-models-with-aml.md)
* [如何在本機執行和調試實驗](./how-to-debug-visual-studio-code.md)