---
title: 如何將模型部署至 Azure 容器實例
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure 容器實例將 Azure Machine Learning 模型部署為 web 服務。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/12/2020
ms.openlocfilehash: fc20d59ca016e7c6be8dae9f50a84e4927449467
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91660729"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>將模型部署到 Azure 容器執行個體


瞭解如何使用 Azure Machine Learning 在 Azure 容器實例上將模型部署為 web 服務 (ACI) 。 如果下列其中一個條件成立，請使用 Azure 容器實例：

- 您需要快速部署及驗證模型。 您不需要事先建立 ACI 容器。 它們是在部署過程中建立的。
- 您正在測試處於開發狀態的模型。 

如需 ACI 的配額和區域可用性的資訊，請參閱 [Azure 容器實例的配額和區域可用性](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) 文章。

> [!IMPORTANT]
> 強烈建議您先在本機上進行偵錯工具，再部署至 web 服務。如需詳細資訊，請參閱[本機的調試](https://docs.microsoft.com/azure/machine-learning/how-to-troubleshoot-deployment#debug-locally)程式
>
> 您也可以參考 Azure Machine Learning - [部署至本機筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Prerequisites

- Azure Machine Learning 工作區。 如需詳細資訊，請參閱 [建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

- 在您的工作區中註冊的機器學習模型。 如果您沒有已註冊的模型，請參閱 [部署模型的方式和位置](how-to-deploy-and-where.md)。

- Machine Learning 服務、 [Azure Machine Learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)或[Azure Machine Learning Visual Studio Code 延伸](tutorial-setup-vscode-extension.md)模組的[Azure CLI 延伸](reference-azure-machine-learning-cli.md)模組。

- 本文中的 __Python__ 程式碼片段假設已設定下列變數：

    * `ws` -設定為您的工作區。
    * `model` -設定為已註冊的模型。
    * `inference_config` -設定為模型的推斷設定。

    如需有關設定這些變數的詳細資訊，請參閱 [部署模型的方式和位置](how-to-deploy-and-where.md)。

- 本文中的 __CLI__ 程式碼片段假設您已建立 `inferenceconfig.json` 檔。 如需有關建立這份檔的詳細資訊，請參閱 [部署模型的方式和位置](how-to-deploy-and-where.md)。

## <a name="limitations"></a>限制

* 使用虛擬網路中的 Azure 容器實例時，虛擬網路必須位於與您的 Azure Machine Learning 工作區相同的資源群組中。
* 使用虛擬網路內的 Azure 容器實例時，您工作區的 Azure Container Registry (ACR) 也不能在虛擬網路中。

如需詳細資訊，請參閱 [如何使用虛擬網路保護推斷](how-to-secure-inferencing-vnet.md#enable-azure-container-instances-aci)。

## <a name="deploy-to-aci"></a>部署到 ACI

若要將模型部署至 Azure 容器實例，請建立 __部署__ 設定，以描述所需的計算資源。 例如，核心和記憶體數目。 您也需要 __推斷__設定，其中描述裝載模型和 web 服務所需的環境。 如需有關建立推斷設定的詳細資訊，請參閱 [部署模型的方式和位置](how-to-deploy-and-where.md)。

> [!NOTE]
> * ACI 只適用于大小小於 1 GB 的小型模型。 
> * 我們建議使用單一節點 AKS 來開發測試較大型的模型。
> * 要部署的模型數目限制為每個容器) 每個部署 (1000 個模型。 

### <a name="using-the-sdk"></a>使用 SDK

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

如需此範例中所使用之類別、方法和參數的詳細資訊，請參閱下列參考檔：

* [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [模型. 部署](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truewait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>使用 CLI

若要使用 CLI 進行部署，請使用下列命令。 取代 `mymodel:1` 為已註冊模型的名稱和版本。 `myservice`以用來提供此服務的名稱取代：

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

如需詳細資訊，請參閱 [az ml 模型部署](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-model-deploy) 參考。 

## <a name="using-vs-code"></a>使用 VS Code

請參閱 [使用 VS Code 部署您的模型](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)。

> [!IMPORTANT]
> 您不需要事先建立 ACI 容器來進行測試。 ACI 容器會視需要建立。


## <a name="next-steps"></a>後續步驟

* [如何使用自訂 Docker 映射部署模型](how-to-deploy-custom-docker-image.md)
* [部署疑難排解](how-to-troubleshoot-deployment.md)
* [更新 Web 服務](how-to-deploy-update-web-service.md)
* [使用 TLS 來透過 Azure Machine Learning 保護 Web 服務](how-to-secure-web-service.md)
* [取用部署為 Web 服務的 ML 模型](how-to-consume-web-service.md)
* [使用 Application Insights 監視您的 Azure Machine Learning 模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)
