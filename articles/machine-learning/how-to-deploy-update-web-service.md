---
title: 更新 web 服務
titleSuffix: Azure Machine Learning
description: 瞭解如何重新整理已在 Azure Machine Learning 中部署的 web 服務。 您可以更新 [模型]、[環境] 和 [輸入腳本] 等設定。
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.custom: deploy, devx-track-azurecli
ms.openlocfilehash: ddd075a5880899a065ee6eaaa4ff0636b6590370
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840775"
---
# <a name="update-a-deployed-web-service"></a>更新已部署的 web 服務

在本文中，您將瞭解如何更新隨 Azure Machine Learning 部署的 web 服務。

## <a name="prerequisites"></a>先決條件

本教學課程假設您已部署具有 Azure Machine Learning 的 web 服務。 如果您需要瞭解如何部署 web 服務，請遵循下列 [步驟](how-to-deploy-and-where.md)。

## <a name="update-web-service"></a>更新 Web 服務

若要更新 web 服務，請使用 `update` 方法。 您可以將 web 服務更新為使用新的模型、新的輸入腳本，或可在推斷設定中指定的新相依性。 如需詳細資訊，請參閱 Webservice 的檔 [。更新](/python/api/azureml-core/azureml.core.webservice.webservice.webservice?preserve-view=true&view=azure-ml-py#&preserve-view=trueupdate--args-)。

請參閱 [AKS 服務更新方法。](/python/api/azureml-core/azureml.core.webservice.akswebservice?preserve-view=true&view=azure-ml-py#&preserve-view=trueupdate-image-none--autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--tags-none--properties-none--description-none--models-none--inference-config-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-)

請參閱 [ACI 服務更新方法。](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=trueupdate-image-none--tags-none--properties-none--description-none--auth-enabled-none--ssl-enabled-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--enable-app-insights-none--models-none--inference-config-none-)

> [!IMPORTANT]
> 當您建立新版本的模型時，必須手動更新您想要使用的每個服務。
>
> 您不能使用 SDK 來更新從 Azure Machine Learning 設計工具發行的 web 服務。

**使用 SDK**

下列程式碼示範如何使用 SDK 來更新 web 服務的模型、環境和專案腳本：

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# Register new model.
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment.
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service.
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s).
service.update(models=[new_model], inference_config=inference_config)
service.wait_for_deployment(show_output=True)
print(service.state)
print(service.get_logs())
```

**使用 CLI**

您也可以使用 ML CLI 來更新 web 服務。 下列範例示範如何註冊新的模型，然後將 web 服務更新為使用新的模型：

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> 在此範例中，會使用 JSON 檔，將來自註冊命令的模型資訊傳遞給 update 命令。
>
> 若要將服務更新為使用新的輸入腳本或環境，請建立 [推斷設定檔](./reference-azure-machine-learning-cli.md#inference-configuration-schema) ，並使用參數來指定 `ic` 。

如需詳細資訊，請參閱 [az ml 服務更新](/cli/azure/ext/azure-cli-ml/ml/service?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update) 檔。

## <a name="next-steps"></a>後續步驟

* [針對失敗的部署進行疑難排解](how-to-troubleshoot-deployment.md)
* [部署到 Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [建立用戶端應用程式以使用 web 服務](how-to-consume-web-service.md)
* [如何使用自訂 Docker 映射部署模型](how-to-deploy-custom-docker-image.md)
* [使用 TLS 來透過 Azure Machine Learning 保護 Web 服務](how-to-secure-web-service.md)
* [使用 Application Insights 監視您的 Azure Machine Learning 模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)
* [建立模型部署的事件警示和觸發程式](how-to-use-event-grid.md)
