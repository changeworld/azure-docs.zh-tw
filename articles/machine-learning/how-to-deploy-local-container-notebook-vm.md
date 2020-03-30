---
title: 如何部署模型以計算實例
titleSuffix: Azure Machine Learning
description: 瞭解如何使用計算實例將 Azure 機器學習模型部署為 Web 服務。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: afbd9950c31bc1c40b01ec0aaf3d2bfffb8a6b94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78398160"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>將模型部署到 Azure 機器學習計算實例

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

瞭解如何使用 Azure 機器學習在 Azure 機器學習計算實例上將模型部署為 Web 服務。 如果以下條件之一為 true，請使用計算實例：

- 您需要快速部署及驗證模型。
- 您正在測試處於開發狀態的模型。

> [!TIP]
> 將模型從計算實例上的 Jupyter 筆記本部署到同一 VM 上的 Web 服務是_本地部署_。 在這種情況下，"本地"電腦是計算實例。 有關部署的詳細資訊，請參閱[使用 Azure 機器學習部署模型](how-to-deploy-and-where.md)。

## <a name="prerequisites"></a>Prerequisites

- 運行計算實例的 Azure 機器學習工作區。 有關詳細資訊，請參閱[設置環境和工作區](tutorial-1st-experiment-sdk-setup.md)。

## <a name="deploy-to-the-compute-instances"></a>部署到計算實例

計算實例中包含演示本地部署的示例筆記本。 使用以下步驟載入筆記本並將模型部署為 VM 上的 Web 服務：

1. 從[Azure 機器學習工作室](https://ml.azure.com)中，選擇 Azure 機器學習計算實例。

1. 打開子`samples-*`目錄，然後打開`how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`。 打開後，運行筆記本。

    ![筆記本上運行的本機服務的螢幕截圖](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)

1. 筆記本顯示服務正在運行的 URL 和埠。 例如： `https://localhost:6789` 。 您還可以運行包含`print('Local service port: {}'.format(local_service.port))`的儲存格以顯示埠。

    ![正在運行的本機服務埠的螢幕截圖](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. 要從計算實例測試服務，請使用`https://localhost:<local_service.port>`URL。 要從遠端用戶端進行測試，獲取在計算實例上運行的服務的公共 URL。 可以使用以下公式確定公共 URL; 
    * 筆記本 VM： `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score`. 
    * 計算實例： `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score`. 

    例如， 
    * 筆記本 VM：`https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * 計算實例：`https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>測試服務

要將示例資料提交到正在運行的服務，請使用以下代碼。 將 的值`service_url`替換為 上一步驟中的 URL：

> [!NOTE]
> 驗證到計算實例上的部署時，將使用 Azure 活動目錄進行身份驗證。 示例代碼`interactive_auth.get_authentication_header()`中的調用將使用 AAD 對您進行身份驗證，並返回一個標頭，該標頭隨後可用於對計算實例上的服務進行身份驗證。 有關詳細資訊，請參閱為[Azure 機器學習資源和工作流設置身份驗證](how-to-setup-authentication.md#interactive-authentication)。
>
> 驗證到 Azure 庫伯奈斯服務或 Azure 容器實例上的部署時，使用不同的身份驗證方法。 有關詳細資訊，請參閱[為 Azure 機器學習資源和工作流設置身份驗證](how-to-setup-authentication.md#web-service-authentication)。

```python
import requests
import json
from azureml.core.authentication import InteractiveLoginAuthentication

# Get a token to authenticate to the compute instance from remote
interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()

# Create and submit a request using the auth header
headers = auth_header
# Add content type header
headers.update({'Content-Type':'application/json'})

# Sample data to send to the service
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

# Replace with the URL for your compute instance, as determined from the previous section
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>後續步驟

* [如何使用自訂 Docker 映射部署模型](how-to-deploy-custom-docker-image.md)
* [部署故障排除](how-to-troubleshoot-deployment.md)
* [使用 SSL 保護 Azure Machine Learning Web 服務](how-to-secure-web-service.md)
* [取用部署為 Web 服務的 ML 模型](how-to-consume-web-service.md)
* [使用應用程式見解監視 Azure 機器學習模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)