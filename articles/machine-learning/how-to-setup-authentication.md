---
title: 設定驗證
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure Machine Learning 中設定各種資源和工作流程的驗證。 在服務內設定和使用驗證的方法有很多種，範圍從用於開發或測試用途的簡單 UI 型驗證到完整 Azure Active Directory 服務主體驗證。
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: has-adal-ref
ms.openlocfilehash: 6b2cfa85ea412a5ef8bda47a7ff6e99970ba6b0e
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611835"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>設定 Azure Machine Learning 資源和工作流程的驗證
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您會了解如何在 Azure Machine Learning 中設定各種資源和工作流程的驗證。 向服務進行驗證的方法有很多種，範圍從用於開發或測試用途的簡單 UI 型驗證到完整 Azure Active Directory 服務主體驗證。 本文也會說明 Web 服務驗證運作方式的差異，以及如何向 Azure Machine Learning REST API 進行驗證。

本操作指南會示範如何執行下列工作：

* 使用互動式 UI 驗證進行測試/開發
* 設定服務主體驗證
* 向工作區進行驗證
* 取得適用於 Azure Machine Learning REST API 的 OAuth 2.0 承載者類型權杖
* 了解 Web 服務驗證

如需 Azure Machine Learning 內安全性和驗證的一般概觀，請參閱[概念文章](concept-enterprise-security.md)。

## <a name="prerequisites"></a>必要條件

* 建立 [Azure Machine Learning 工作區](how-to-manage-workspace.md)。
* [設定開發環境](how-to-configure-environment.md)以安裝 Azure Machine Learning SDK，或使用已安裝 SDK 的 [Azure Machine Learning Notebook VM](concept-azure-machine-learning-architecture.md#compute-instance)。

## <a name="interactive-authentication"></a>互動式驗證

此服務的文件中，大部分範例都是使用 Jupyter 筆記本中的互動式驗證來簡易測試和示範。 您可透過此簡易方法來測試正在建置的內容。 有兩個函式呼叫會自動提示使用 UI 型驗證流程。

呼叫 `from_config()` 函式會發出提示。

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

`from_config()` 函式會尋找 JSON 檔案，其中包含工作區連線資訊。 您也可使用 `Workspace` 建構函式明確指定連線詳細資料，這也會提示進行互動式驗證。 這兩個呼叫都是相同的。

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

如果您有多個租用戶的存取權，則可能需要匯入類別，並明確定義目標租用戶。 呼叫 `InteractiveLoginAuthentication` 的建構函式也會提示您登入，與上述呼叫類似。

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

儘管對於測試和學習很有用，但互動式驗證將無法協助建置自動或無周邊的工作流程。 設定服務主體驗證對於使用 SDK 的自動化流程是最佳方法。

## <a name="set-up-service-principal-authentication"></a>設定服務主體驗證

若要啟用與特定使用者登入分離的驗證，則此為必要流程，其可供向自動工作流程中的 Azure Machine Learning Python SDK 進行驗證。 服務主體驗證也可供[驗證 REST API](#azure-machine-learning-rest-api-auth)。

若要設定服務主體驗證，請先在 Azure Active Directory 中建立應用程式註冊，然後將應用程式角色型存取權授與 ML 工作區。 完成此安裝程式最簡單方式是透過 Azure 入口網站中的 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)。 登入入口網站之後，請按一下名稱附近頁面右上方的 `>_` 圖示，以開啟命令介面。

如果 Azure 帳戶中尚未使用 Cloud Shell，則必須建立儲存體帳戶資源，以儲存所有寫入的檔案。 一般而言，此儲存體帳戶僅會產生些微的每月費用。 此外，如果先前未使用機器學習延伸模組，則請使用下列命令來加以安裝。

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> 您必須是訂用帳戶的管理員才能執行下列步驟。

接下來，執行下列命令來建立服務主體。 為其命名，此案例中名稱為 **ml-auth**。

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth --name ml-auth
```

此輸出將會是與下列類似的 JSON。 請記下 `clientId`、`clientSecret` 和 `tenantId` 欄位，因為本文中的其他步驟將會用到。

```json
{
    "clientId": "your-client-id",
    "clientSecret": "your-client-secret",
    "subscriptionId": "your-sub-id",
    "tenantId": "your-tenant-id",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com",
    "activeDirectoryGraphResourceId": "https://graph.windows.net",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:5555",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net"
}
```

接下來，執行下列命令以取得剛才建立服務主體的詳細資料，並使用上述 `clientId` 值作為 `--id` 參數的輸入。

```azurecli-interactive
az ad sp show --id your-client-id
```

下列是來自命令的 JSON 輸出簡易範例。 記下 `objectId` 欄位，因為您需要此欄位的值才能進行下一個步驟。

```json
{
    "accountEnabled": "True",
    "addIns": [],
    "appDisplayName": "ml-auth",
    ...
    ...
    ...
    "objectId": "your-sp-object-id",
    "objectType": "ServicePrincipal"
}
```

接下來，使用下列命令，將服務主體存取權指派給機器學習工作區。 您需要工作區名稱，以及其個別 `-w` 和 `-g` 參數的資源群組名稱。 針對 `--user` 參數，請使用上一個步驟中的 `objectId` 值。 `--role` 參數可供設定服務主體的存取角色，一般而言，您會使用**擁有者**或**參與者**角色。 兩者都具有現有資源 (如計算叢集和資料存放區) 的寫入權限，但只有**擁有者**可佈建這些資源。

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

此呼叫不會產生任何輸出，但您現在已為工作區設定服務主體驗證。

## <a name="authenticate-to-your-workspace"></a>向工作區進行驗證

現在您已啟用服務主體驗證，即可在 SDK 中驗證工作區，而不需要實際以使用者身分登入。 使用 `ServicePrincipalAuthentication` 類別建構函式，並使用從上一個步驟中所獲得的值作為參數。 `tenant_id` 參數會對應到上述的 `tenantId`、`service_principal_id` 對應至 `clientId`、`service_principal_password` 對應至 `clientSecret`。

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

`sp` 變數現在會保留在 SDK 中直接使用的驗證物件。 一般而言，將上述使用的識別碼/祕密儲存在環境變數中是個好主意，如下列程式碼所示。

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

針對在 Python 中執行並主要使用 SDK 的自動工作流程，您可在大部分的情況下使用此物件來進行驗證。 下列程式碼會使用剛才建立的驗證物件，以向工作區進行驗證。

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Azure Machine Learning REST API 驗證

在上述步驟內建立的服務主體也可用來向 Azure Machine Learning [REST API](https://docs.microsoft.com/rest/api/azureml/) 進行驗證。 您可使用 Azure Active Directory [用戶端認證授與流程](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)，其允許在自動工作流程中進行無周邊驗證的服務對服務呼叫。 這些範例是使用 Python 和 Node.js 中的 [ADAL 程式庫](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)所實作，但您也可以使用任何支援 OpenID Connect 1.0 的開放原始碼程式庫。

> [!NOTE]
> MSAL.js 是比 ADAL 更新的程式庫，但無法使用 MSAL.js 的用戶端認證來進行服務對服務驗證，因為該認證主要適用於與特定使用者繫結的互動式/UI 驗證用戶端程式庫。 我們建議使用如下所示的 ADAL，以透過 REST API 建置自動工作流程。

### <a name="nodejs"></a>Node.js

使用下列步驟以利用 Node.js 來產生驗證權杖。 在環境中，執行 `npm install adal-node`。 然後，使用在上述步驟中所建立服務主體的 `tenantId`、`clientId` 和 `clientSecret`，作為下列指令碼中相符變數的值。

```javascript
const adal = require('adal-node').AuthenticationContext;

const authorityHostUrl = 'https://login.microsoftonline.com/';
const tenantId = 'your-tenant-id';
const authorityUrl = authorityHostUrl + tenantId;
const clientId = 'your-client-id';
const clientSecret = 'your-client-secret';
const resource = 'https://management.azure.com/';

const context = new adal(authorityUrl);

context.acquireTokenWithClientCredentials(
  resource,
  clientId,
  clientSecret,
  (err, tokenResponse) => {
    if (err) {
      console.log(`Token generation failed due to ${err}`);
    } else {
      console.dir(tokenResponse, { depth: null, colors: true });
    }
  }
);
```

變數 `tokenResponse` 為物件，其中包含權杖和相關聯的中繼資料 (例如到期時間)。 權杖的有效時間為 1 小時，且可以重新整理，只要再次執行相同呼叫來擷取新的權杖即可。 下列為回應的範例。

```javascript
{
    tokenType: 'Bearer',
    expiresIn: 3599,
    expiresOn: 2019-12-17T19:15:56.326Z,
    resource: 'https://management.azure.com/',
    accessToken: "random-oauth-token",
    isMRRT: true,
    _clientId: 'your-client-id',
    _authority: 'https://login.microsoftonline.com/your-tenant-id'
}
```

使用 `accessToken` 屬性來擷取驗證權杖。 如需如何使用權杖進行 API 呼叫的範例，請參閱 [REST API 文件](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API)。

### <a name="python"></a>Python

使用下列步驟以透過 Python 來產生驗證權杖。 在環境中，執行 `pip install adal`。 然後，使用在上述步驟中所建立服務主體的 `tenantId`、`clientId` 和 `clientSecret`，作為下列指令碼中適當變數的值。

```python
from adal import AuthenticationContext

client_id = "your-client-id"
client_secret = "your-client-secret"
resource_url = "https://login.microsoftonline.com"
tenant_id = "your-tenant-id"
authority = "{}/{}".format(resource_url, tenant_id)

auth_context = AuthenticationContext(authority)
token_response = auth_context.acquire_token_with_client_credentials("https://management.azure.com/", client_id, client_secret)
print(token_response)
```

變數 `token_response` 為字典，其中包含權杖和相關聯的中繼資料 (例如到期時間)。 權杖的有效時間為 1 小時，且可以重新整理，只要再次執行相同呼叫來擷取新的權杖即可。 下列為回應的範例。

```python
{
    'tokenType': 'Bearer',
    'expiresIn': 3599,
    'expiresOn': '2019-12-17 19:47:15.150205',
    'resource': 'https://management.azure.com/',
    'accessToken': 'random-oauth-token',
    'isMRRT': True,
    '_clientId': 'your-client-id',
    '_authority': 'https://login.microsoftonline.com/your-tenant-id'
}
```

使用 `token_response["accessToken"]` 來擷取驗證權杖。 如需如何使用權杖進行 API 呼叫的範例，請參閱 [REST API 文件](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API)。

## <a name="web-service-authentication"></a>Web 服務驗證

Azure Machine Learning Web 服務使用與上述不同的驗證模式。 向已部署 Web 服務進行驗證的最簡單方式，是使用**金鑰型驗證**，此驗證會產生不需要重新整理的靜態承載者類型驗證金鑰。 如果只需要對已部署的 Web 服務進行驗證，則不需要設定如上所示的服務主體驗證。

在 Azure Kubernetes Service 上部署的 Web 服務預設會「啟用」金鑰型驗證。 Azure 容器執行個體部署的服務預設會「停用」金鑰型驗證，但您可在建立 ACI Web 服務時設定 `auth_enabled=True` 來加以啟用。 下列範例會建立啟用金鑰式驗證的 ACI 部署設定。

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

若要擷取驗證金鑰，請使用 `aci_service.get_keys()`。 若要重新產生金鑰，請使用 `regen_key()` 函式，並傳遞 **Primary** 或 **Secondary**。

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Web 服務也支援權杖型驗證，但僅適用於 Azure Kubernetes Service 部署。 如需有關驗證的詳細資訊，請參閱使用 Web 服務的[操作說明](how-to-consume-web-service.md)。

### <a name="token-based-web-service-authentication"></a>權杖型 Web 服務驗證

當啟用 Web 服務的權杖驗證時，使用者必須向 Web 服務出示 Machine Learning JSON Web 權杖才能加以存取。 權杖會在指定的時間範圍之後過期，且需要重新整理才能繼續進行呼叫。

* 在部署至 Azure Kubernetes Service 時，**依預設會停用**權杖驗證。
* 部署至 Azure 容器執行個體時，**不支援**權杖驗證。

若要控制權杖驗證，請在建立或更新部署時使用 `token_auth_enabled` 參數。

如果已啟用權杖驗證，即可使用 `get_token` 方法來擷取 JSON Web 權杖 (JWT)，以及該權杖的到期時間：

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> 您將必須在權杖的 `refresh_by` 時間之後要求新的權杖。 如果需要在 Python SDK 以外重新整理權杖，則其中一個選項是使用 REST API 搭配服務主體驗證來定期進行 `service.get_token()` 呼叫，如先前所述。
>
> 強烈建議在與 Azure Kubernetes Service 叢集相同的區域中建立 Azure Machine Learning 工作區。
>
> 若要使用權杖進行驗證，Web 服務會呼叫 Azure Machine Learning 工作區的建立區域。 如果工作區區域無法使用，則無法擷取 Web 服務的權杖，即使叢集與工作區位於不同的區域也一樣。 因此，除非工作區區域再次可供使用，否則 Azure AD 驗證無法使用。
>
> 此外，叢集區域和工作區區域之間的距離愈大，擷取權杖所需的時間就越長。

## <a name="next-steps"></a>後續步驟

* [定型及部署映像分類模型](tutorial-train-models-with-aml.md)。
* [使用部署為 Web 服務的 Azure Machine Learning 模型](how-to-consume-web-service.md)。
