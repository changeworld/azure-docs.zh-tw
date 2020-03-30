---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: d36bf2db05113656a77e76ff900d95910f313c73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477250"
---
要更新 Web 服務，請使用`update`方法。 您可以更新 Web 服務以使用可在推理配置中指定的新模型、新條目腳本或新依賴項。 有關詳細資訊，請參閱[Webservice.update 的文檔](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-)。

> [!IMPORTANT]
> 創建模型的新版本時，必須手動更新要使用的每個服務。
>
> 不能使用 SDK 更新從 Azure 機器學習設計器發佈的 Web 服務。

**使用 SDK**

以下代碼演示如何使用 SDK 更新 Web 服務的模型、環境和條目腳本：

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
print(service.state)
print(service.get_logs())
```

**使用 CLI**

您還可以使用 ML CLI 更新 Web 服務。 下面的示例演示了註冊新模型，然後更新 Web 服務以使用新模型：

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> 在此示例中，JSON 文檔用於將模型資訊從註冊命令傳遞到更新命令中。
>
> 要更新服務以使用新的條目腳本或環境，請創建一個[推理設定檔](/azure/machine-learning/service/reference-azure-machine-learning-cli#inference-configuration-schema)，並使用 參數`ic`指定它。

有關詳細資訊，請參閱 az [ml 服務更新](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update)文檔。