---
title: 如何將模型部署到 Azure 容器實體
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure 容器實例將 Azure 機器學習模型部署為 Web 服務。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 12/27/2019
ms.openlocfilehash: d460112394d7c7b7d2da4e8af41c0085b67226ec
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475468"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>將模型部署到 Azure 容器執行個體
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

瞭解如何使用 Azure 機器學習在 Azure 容器實例 (ACI) 上將模型部署為 Web 服務。 如果以下條件之一為 true,請使用 Azure 容器實體:

- 您需要快速部署及驗證模型。 您無需提前創建 ACI 容器。 它們作為部署過程的一部分創建。
- 您正在測試處於開發狀態的模型。 

有關 ACI 的配額和地區可用性的資訊,請參閱[Azure 容器實例的配額和地區可用性](https://docs.microsoft.com/azure/container-instances/container-instances-quotas)一文。

## <a name="prerequisites"></a>Prerequisites

- Azure Machine Learning 工作區。 有關詳細資訊,請參閱創建[Azure 機器學習工作區](how-to-manage-workspace.md)。

- 在工作區中註冊的機器學習模型。 如果沒有註冊的模型,請參閱[如何部署模型以及部署模型的位置](how-to-deploy-and-where.md)。

- [機器學習服務的 Azure CLI](reference-azure-machine-learning-cli.md)擴充[、Azure 機器學習 Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)或 Azure[機器學習視覺化工作室代碼延伸](tutorial-setup-vscode-extension.md)。

- 本文中的__Python__代碼段假定設定了以下變數:

    * `ws`- 設置為工作區。
    * `model`- 設置為已註冊型號。
    * `inference_config`- 設置為模型的推理配置。

    有關設定這些變數的詳細資訊,請參閱[部署模型的方式和位置](how-to-deploy-and-where.md)。

- 本文中的__CLI__代碼段假定您`inferenceconfig.json`已建立 文檔。 有關建立此文件的詳細資訊,請參考[部署模型的方式和位置](how-to-deploy-and-where.md)。

## <a name="deploy-to-aci"></a>部署到 ACI

要將模型部署到 Azure 容器實體,請建立描述所需計算資源的__部署設定__。 例如,內核和記憶體的數量。 您還需要一個__推理配置__,它描述了託管模型和 Web 服務所需的環境。 有關建立推理設定的詳細資訊,請參閱[部署模型的方式和位置](how-to-deploy-and-where.md)。

### <a name="using-the-sdk"></a>使用 SDK

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

有關此範例中使用的類、方法和參數的詳細資訊,請參閱以下參考文檔:

* [AciWeb服務.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [模型.部署](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Web 服務.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>使用 CLI

要使用 CLI 進行部署,請使用以下命令。 替換為`mymodel:1`已註冊模型的名稱和版本。 取代為`myservice`名稱以提供此服務:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

有關詳細資訊,請參閱 az [ml 模型部署](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)引用。 

## <a name="using-vs-code"></a>使用 VS Code

請參考[目前使用 VS 碼部署模型](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)。

> [!IMPORTANT]
> 您無需創建 ACI 容器才能提前進行測試。 根據需要創建 ACI 容器。

## <a name="update-the-web-service"></a>更新 Web 服務

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>後續步驟

* [如何使用自訂 Docker 映像部署模型](how-to-deploy-custom-docker-image.md)
* [部署故障排除](how-to-troubleshoot-deployment.md)
* [使用 TLS 透過 Azure 機器學習保護 Web 服務](how-to-secure-web-service.md)
* [取用部署為 Web 服務的 ML 模型](how-to-consume-web-service.md)
* [使用應用程式見解監視 Azure 機器學習模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)
