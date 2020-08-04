---
title: 設定檔模型記憶體和 CPU 使用量
titleSuffix: Azure Machine Learning
description: 瞭解如何分析您的模型記憶體和 CPU 使用量
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
zone_pivot_groups: aml-control-methods
ms.openlocfilehash: 6bbee606c59482e4a06f344d3221e8611f6dcc9d
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87544537"
---
# <a name="profile-your-model-to-determine-resource-utilization"></a>分析模型以判斷資源使用率

本文說明如何將機器學習分析成模型，以判斷當您將模型部署為 web 服務時，需要配置多少 CPU 和記憶體。

## <a name="prerequisites"></a>先決條件

本文假設您已使用 Azure Machine Learning 訓練並註冊模型。 請參閱[這裡的範例教學](how-to-train-scikit-learn.md)課程，以取得使用 Azure Machine Learning 定型和註冊 scikit-learn 學習模型的範例。

## <a name="run-the-profiler"></a>執行分析工具

在您註冊模型並備妥其部署所需的其他元件之後，您可以判斷所部署服務所需的 CPU 和記憶體。 分析會測試執行模型的服務，並傳回如 CPU 使用量、記憶體使用量和回應延遲的資訊。 它也會根據資源使用量提供 CPU 和記憶體的建議。

若要分析您的模型，您將需要：
* 已註冊的模型。
* 根據您的輸入腳本和推斷環境定義的推斷設定。
* 單一資料行表格式資料集，其中每個資料列都包含代表範例要求資料的字串。

> [!IMPORTANT]
> 此時，我們只支援將預期其要求資料為字串的服務進行分析，例如：字串序列化 json、文字、字串序列化影像等。資料集的每個資料列內容（字串）都會放入 HTTP 要求的主體中，並傳送至封裝模型以進行評分的服務。

以下範例說明如何建立輸入資料集，以分析預期其傳入要求資料可包含序列化 json 的服務。 在此情況下，我們建立了一個以資料集為基礎的100實例，其具有相同的要求資料內容。 在真實世界的案例中，我們建議您使用包含各種輸入的較大型資料集，特別是當您的模型資源使用方式/行為與輸入相依時更是如此。

::: zone pivot="py-sdk"

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

一旦您的資料集包含備妥範例要求資料，請建立推斷設定。 推斷設定是以 score.py 和環境定義為基礎。 下列範例示範如何建立推斷設定並執行分析：

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

::: zone-end

::: zone pivot="cli"


下列命令示範如何使用 CLI 來分析模型：

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> 若要保存分析所傳回的資訊，請使用模型的標記或屬性。 使用標記或屬性，會將資料與模型儲存在模型登錄中。 下列範例示範如何新增包含和資訊的新 `requestedCpu` 標記 `requestedMemoryInGb` ：
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

::: zone-end

## <a name="next-steps"></a>後續步驟

* [針對失敗的部署進行疑難排解](how-to-troubleshoot-deployment.md)
* [部署到 Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [建立用戶端應用程式以使用 web 服務](how-to-consume-web-service.md)
* [更新 web 服務](how-to-deploy-update-web-service.md)
* [如何使用自訂 Docker 映射部署模型](how-to-deploy-custom-docker-image.md)
* [使用 TLS 來透過 Azure Machine Learning 保護 Web 服務](how-to-secure-web-service.md)
* [使用 Application Insights 監視您的 Azure Machine Learning 模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)
* [建立模型部署的事件警示和觸發程式](how-to-use-event-grid.md)

