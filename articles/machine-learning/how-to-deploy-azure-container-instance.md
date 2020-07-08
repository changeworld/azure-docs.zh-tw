---
title: 如何將模型部署至 Azure 容器實例
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure 容器實例，將您的 Azure Machine Learning 模型部署為 web 服務。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/12/2020
ms.openlocfilehash: 44c197b7d9935a7b0631c6cbcd96fde783c2fffe
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86087260"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>將模型部署到 Azure 容器執行個體
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

瞭解如何使用 Azure Machine Learning 將模型部署為 Azure 容器實例（ACI）上的 web 服務。 如果下列其中一個條件成立，請使用 Azure 容器實例：

- 您需要快速部署及驗證模型。 您不需要在一段時間之前建立 ACI 容器。 它們會在部署程式中建立。
- 您正在測試處於開發狀態的模型。 

如需有關 ACI 配額和區域可用性的詳細資訊，請參閱[Azure 容器實例的配額和區域可用性](https://docs.microsoft.com/azure/container-instances/container-instances-quotas)一文。

## <a name="prerequisites"></a>Prerequisites

- Azure Machine Learning 工作區。 如需詳細資訊，請參閱[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

- 在您的工作區中註冊的機器學習模型。 如果您沒有已註冊的模型，請參閱[部署模型的方式和位置](how-to-deploy-and-where.md)。

- [Machine Learning 服務的 Azure CLI 延伸](reference-azure-machine-learning-cli.md)模組， [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)，或[Azure Machine Learning Visual Studio Code 延伸](tutorial-setup-vscode-extension.md)模組。

- 本文中的__Python__程式碼片段假設已設定下列變數：

    * `ws`-設定為您的工作區。
    * `model`-設定為您已註冊的模型。
    * `inference_config`-設為模型的推斷設定。

    如需有關設定這些變數的詳細資訊，請參閱[如何和在何處部署模型](how-to-deploy-and-where.md)。

- 本文中的__CLI__程式碼片段假設您已建立 `inferenceconfig.json` 檔。 如需有關建立此檔的詳細資訊，請參閱[如何和部署模型的位置](how-to-deploy-and-where.md)。

## <a name="deploy-to-aci"></a>部署到 ACI

若要將模型部署到 Azure 容器實例，請建立__部署__設定，以描述所需的計算資源。 例如，核心和記憶體數目。 您也需要__推斷__設定，其中描述裝載模型和 web 服務所需的環境。 如需建立推斷設定的詳細資訊，請參閱[如何和部署模型的位置](how-to-deploy-and-where.md)。

> [!NOTE]
> * ACI 僅適用于大小 <1GB 的小型模型。 
> * 我們建議使用單一節點 AKS 來進行較大型模型的開發測試。

### <a name="using-the-sdk"></a>使用 SDK

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

如需此範例中使用之類別、方法和參數的詳細資訊，請參閱下列參考檔：

* [AciWebservice。 deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [模型。部署](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>使用 CLI

若要使用 CLI 進行部署，請使用下列命令。 將取代 `mymodel:1` 為已註冊模型的名稱和版本。 `myservice`以要提供此服務的名稱取代：

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

如需詳細資訊，請參閱[az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) reference。 

## <a name="using-vs-code"></a>使用 VS Code

請參閱[使用 VS Code 部署您的模型](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)。

> [!IMPORTANT]
> 您不需要事先建立 ACI 容器來進行測試。 ACI 容器會視需要建立。

## <a name="update-the-web-service"></a>更新 Web 服務

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>後續步驟

* [如何使用自訂 Docker 映射部署模型](how-to-deploy-custom-docker-image.md)
* [部署疑難排解](how-to-troubleshoot-deployment.md)
* [使用 TLS 來透過 Azure Machine Learning 保護 Web 服務](how-to-secure-web-service.md)
* [取用部署為 Web 服務的 ML 模型](how-to-consume-web-service.md)
* [使用 Application Insights 監視您的 Azure Machine Learning 模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)
