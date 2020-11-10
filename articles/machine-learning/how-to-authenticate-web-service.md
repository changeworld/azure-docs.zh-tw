---
title: 針對部署為 web 服務的模型設定驗證
titleSuffix: Azure Machine Learning
description: 瞭解如何設定 Azure Machine Learning 中部署至 web 服務之機器學習模型的驗證。
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 11/06/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5bd938fce347d439c2acb4e3fcace04d5b27d770
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94447585"
---
# <a name="configure-authentication-for-models-deployed-as-web-services"></a>針對部署為 web 服務的模型設定驗證

Azure Machine Learning 可讓您將已定型的機器學習模型部署為 web 服務。 在本文中，您將瞭解如何設定這些部署的驗證。

Azure Machine Learning 所建立的模型部署可以設定為使用兩種驗證方法的其中一種：

* 以索引 **鍵為基礎** ：靜態金鑰是用來對 web 服務進行驗證。
* 以 **權杖為基礎** ：您必須從 Azure Machine Learning 工作區取得暫存權杖， (使用 Azure Active Directory) ，並用來驗證 web 服務。 此權杖會在一段時間後到期，而且必須重新整理，才能繼續使用 web 服務。

    > [!NOTE]
    > 只有在部署至 Azure Kubernetes Service 時，才可使用以權杖為基礎的驗證。

## <a name="key-based-authentication"></a>金鑰式驗證

Azure Kubernetes Service (AKS) 上部署的 Web 服務，預設會 *啟用* 以金鑰為基礎的驗證。

Azure 容器實例 (ACI) 部署的服務預設會 *停用* 金鑰型驗證，但是您可以在 `auth_enabled=True` 建立 ACI web 服務時設定來啟用它。 下列程式碼範例示範如何在啟用金鑰型驗證的情況下建立 ACI 部署設定。

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

其後，您可使用 `Model` 類別，在部署中使用自訂 ACI 設定。

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

若要擷取驗證金鑰，請使用 `aci_service.get_keys()`。 若要重新產生金鑰，請使用 `regen_key()` 函式，並傳遞 **Primary** 或 **Secondary** 。

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

## <a name="token-based-authentication"></a>權杖式驗證

當啟用 Web 服務的權杖驗證時，使用者必須向 Web 服務出示 Machine Learning JSON Web 權杖才能加以存取。 權杖會在指定的時間範圍之後過期，且需要重新整理才能繼續進行呼叫。

* 在部署至 Azure Kubernetes Service 時， **依預設會停用** 權杖驗證。
* 部署至 Azure 容器執行個體時， **不支援** 權杖驗證。
* 權杖驗證 **無法與金鑰型驗證同時使用** 。

若要控制權杖驗證，請 `token_auth_enabled` 在建立或更新部署時使用參數：

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

如果已啟用權杖驗證，即可使用 `get_token` 方法來擷取 JSON Web 權杖 (JWT)，以及該權杖的到期時間：

> [!TIP]
> 如果您使用服務主體來取得權杖，並希望它具有取得權杖的最低必要存取權，請將它指派給工作區的 **讀取** 者角色。

```python
token, refresh_by = aks_service.get_token()
print(token)
```

> [!IMPORTANT]
> 您將必須在權杖的 `refresh_by` 時間之後要求新的權杖。 如果需要在 Python SDK 以外重新整理權杖，則其中一個選項是使用 REST API 搭配服務主體驗證來定期進行 `service.get_token()` 呼叫，如先前所述。
>
> 強烈建議在與 Azure Kubernetes Service 叢集相同的區域中建立 Azure Machine Learning 工作區。
>
> 若要使用權杖進行驗證，Web 服務會呼叫 Azure Machine Learning 工作區的建立區域。 如果您的工作區區域無法使用，即使您的叢集與工作區位於不同的區域，您也無法提取 web 服務的權杖。 結果是，在您的工作區區域再次可供使用之前，Azure AD 驗證無法使用。
>
> 此外，您的叢集區域和工作區區域之間的距離愈大，提取權杖所需的時間就愈長。

## <a name="next-steps"></a>後續步驟

如需有關驗證已部署模型的詳細資訊，請參閱為 [部署為 web 服務的模型建立用戶端](how-to-consume-web-service.md)。