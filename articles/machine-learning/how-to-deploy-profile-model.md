---
title: 設定檔模型記憶體和 CPU 使用量
titleSuffix: Azure Machine Learning
description: 瞭解如何在部署之前分析模型。 分析會決定模型的記憶體和 CPU 使用量。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
zone_pivot_groups: aml-control-methods
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: b9ae40b3d2673961f9b84ed702f18b25b79b6d0c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93320409"
---
# <a name="profile-your-model-to-determine-resource-utilization"></a>分析您的模型以判斷資源使用率

本文說明如何分析機器學習模型，以判斷當您將模型部署為 web 服務時，需要配置多少 CPU 和記憶體。

## <a name="prerequisites"></a>必要條件

本文假設您已使用 Azure Machine Learning 來定型和註冊模型。 請參閱 [這裡的範例教學](how-to-train-scikit-learn.md) 課程，以取得使用 Azure Machine Learning 定型和註冊 scikit-learn 學習模型的範例。

## <a name="limitations"></a>限制

* 當工作區的 Azure Container Registry (ACR) 位於虛擬網路後方時，分析將無法運作。

## <a name="run-the-profiler"></a>執行分析工具

註冊您的模型並備妥其部署所需的其他元件之後，您就可以判斷已部署的服務將需要的 CPU 和記憶體。 程式碼剖析會測試執行模型的服務，並傳回如 CPU 使用量、記憶體使用量和回應延遲等資訊。 它也會根據資源使用量提供 CPU 和記憶體的建議。

為了分析您的模型，您將需要：
* 已註冊的模型。
* 根據您的輸入腳本和推斷環境定義的推斷設定。
* 單一資料行表格式資料集，其中每個資料列都包含代表範例要求資料的字串。

> [!IMPORTANT]
> 目前，我們只支援分析預期其要求資料為字串的服務，例如：字串序列化的 json、文字、字串序列化映射等。資料集 (字串) 的每個資料列內容都會放入 HTTP 要求的主體中，並傳送至服務，以封裝模型以進行評分。

> [!IMPORTANT]
> 我們只支援在 ChinaEast2 和 USGovArizona 區域中分析最多2個 Cpu。

以下範例說明如何建立輸入資料集來分析服務，以預期其傳入的要求資料包含序列化的 json。 在此情況下，我們建立了一個以資料集為基礎的相同要求資料內容的100實例。 在真實世界的案例中，我們建議您使用包含各種輸入的較大型資料集，特別是當您的模型資源使用方式/行為是輸入相依的情況時。

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

一旦您的資料集包含就緒的範例要求資料之後，請建立推斷設定。 推斷設定是以 score.py 和環境定義為基礎。 下列範例示範如何建立推斷設定並執行程式碼剖析：

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
> 若要保存分析所傳回的資訊，請使用模型的標記或屬性。 使用標記或屬性，會在模型登錄中將資料儲存在模型中。 下列範例示範如何加入包含和資訊的新 `requestedCpu` 標記 `requestedMemoryInGb` ：
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
* [更新 Web 服務](how-to-deploy-update-web-service.md)
* [如何使用自訂 Docker 映射部署模型](how-to-deploy-custom-docker-image.md)
* [使用 TLS 來透過 Azure Machine Learning 保護 Web 服務](how-to-secure-web-service.md)
* [使用 Application Insights 監視您的 Azure Machine Learning 模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)
* [建立模型部署的事件警示和觸發程式](how-to-use-event-grid.md)

