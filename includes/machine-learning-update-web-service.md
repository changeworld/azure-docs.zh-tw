---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: e6a7ee8ed497c87c08f13e09a0022b0835c952cc
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82975043"
---
若要更新 web 服務，請使用`update`方法。 您可以更新 web 服務，以使用新的模型、新的輸入腳本，或可在推斷設定中指定的新相依性。 如需詳細資訊，請參閱[Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-)的檔。

> [!IMPORTANT]
> 當您建立模型的新版本時，您必須手動更新您想要使用的每個服務。
>
> 您不能使用 SDK 來更新從 Azure Machine Learning 設計工具發佈的 web 服務。

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
> 在此範例中，會使用 JSON 檔，將來自註冊命令的模型資訊傳遞至 update 命令。
>
> 若要將服務更新為使用新的專案腳本或環境，請建立[推斷設定檔](/azure/machine-learning/reference-azure-machine-learning-cli#inference-configuration-schema)，並使用`ic`參數來指定它。

如需詳細資訊，請參閱[az ml 服務更新](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update)檔。