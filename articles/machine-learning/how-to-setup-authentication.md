---
title: 設定驗證
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure Machine Learning 中設定各種資源和工作流程的驗證。 在服務內設定和使用驗證的方法有很多種，範圍從用於開發或測試用途的簡單 UI 型驗證到完整 Azure Active Directory 服務主體驗證。
services: machine-learning
author: larryfr
ms.author: larryfr
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, has-adal-ref, devx-track-javascript
ms.openlocfilehash: 4061d7a3d21b8c2db2bf161c422994cb2742b0b4
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87489872"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>設定 Azure Machine Learning 資源和工作流程的驗證
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

瞭解如何驗證您的 Azure Machine Learning 工作區，以及部署為 web 服務的模型。

一般來說，您可以搭配 Azure Machine Learning 使用兩種類型的驗證：

* __互動式__：您可以在 Azure Active Directory 中使用您的帳戶直接進行驗證，或取得用於驗證的權杖。 在實驗和反復開發期間，會使用互動式驗證。 或者，您想要以每個使用者為基礎來控制資源（例如 web 服務）的存取權。
* __服務主體__：您會在 Azure Active Directory 中建立服務主體帳戶，並使用它來驗證或取得權杖。 當您需要自動化程式來向服務進行驗證，而不需要使用者互動時，會使用服務主體。 例如，持續整合和部署腳本，會在每次定型程式碼變更時，訓練並測試模型。 如果您不想要要求服務的終端使用者進行驗證，您也可以使用服務主體來抓取權杖，以驗證 web 服務。 或者，不會使用 Azure Active Directory 直接執行使用者驗證。

不論使用何種驗證類型，都會使用角色型存取控制（RBAC）來界定資源允許的存取層級範圍。 例如，用來取得已部署模型之存取權杖的帳戶，只需要工作區的讀取權限。 如需 RBAC 的詳細資訊，請參閱[管理 Azure Machine Learning 工作區的存取權](how-to-assign-roles.md)。

## <a name="prerequisites"></a>必要條件

* 建立 [Azure Machine Learning 工作區](how-to-manage-workspace.md)。
* [設定開發環境](how-to-configure-environment.md)以安裝 Azure Machine Learning SDK，或使用已安裝 SDK 的 [Azure Machine Learning Notebook VM](concept-azure-machine-learning-architecture.md#compute-instance)。

## <a name="interactive-authentication"></a>互動式驗證

檔和範例中的大部分範例都會使用互動式驗證。 例如，使用 SDK 時，會有兩個函式呼叫，會自動提示您使用以 UI 為基礎的驗證流程：

* 呼叫 `from_config()` 函式會發出提示。

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

    `from_config()` 函式會尋找 JSON 檔案，其中包含工作區連線資訊。

* 使用此函式 `Workspace` 來提供訂用帳戶、資源群組和工作區資訊，也會提示您進行互動式驗證。

    ```python
    ws = Workspace(subscription_id="your-sub-id",
                  resource_group="your-resource-group-id",
                  workspace_name="your-workspace-name"
                  )
    ```

> [!TIP]
> 如果您有多個租用戶的存取權，則可能需要匯入類別，並明確定義目標租用戶。 呼叫 `InteractiveLoginAuthentication` 的建構函式也會提示您登入，與上述呼叫類似。
>
> ```python
> from azureml.core.authentication import InteractiveLoginAuthentication
> interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
> ```

## <a name="service-principal-authentication"></a>服務主體驗證

若要使用服務主體（SP）驗證，您必須先建立 SP 並授與它對您工作區的存取權。 如先前所述，Azure 角色型存取控制（Azure RBAC）是用來控制存取權，因此您也必須決定要授與 SP 的存取權。

> [!IMPORTANT]
> 使用服務主體時，請授與它用於__的工作所需的最小存取權__。 例如，如果用來讀取 web 部署的存取權杖，您就不會授與服務主體擁有者或參與者存取權。
>
> 授與最小存取權的原因是，服務主體會使用密碼來進行驗證，而且密碼可能會儲存為自動化腳本的一部分。 如果密碼洩漏，則擁有特定工作所需的最小存取權，可將 SP 的惡意使用降至最低。

建立 SP 並將存取權授與您的工作區，最簡單的方式是使用[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 若要建立服務主體並將存取權授與您的工作區，請使用下列步驟：

> [!NOTE]
> 您必須是訂用帳戶的系統管理員，才能執行所有這些步驟。

1. 向您的 Azure 訂用帳戶進行驗證：

    ```azurecli-interactive
    az login
    ```

    如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入登入頁面。 否則，您需要開啟瀏覽器，並遵循命令列上的指示。 這些指示需要瀏覽至 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)，並輸入授權碼。

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

    如需其他驗證方法，請參閱[使用 Azure CLI 登入](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。

1. 安裝 Azure Machine Learning 延伸模組：

    ```azurecli-interactive
    az extension add -n azure-cli-ml
    ```

1. 建立服務主體。 在下列範例中，會建立名為**ml-auth**的 SP：

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

1. 使用上一個步驟中所傳回的值，抓取服務主體的詳細資料 `clientId` ：

    ```azurecli-interactive
    az ad sp show --id your-client-id
    ```

    下列 JSON 是命令輸出的簡單範例。 記下 `objectId` 欄位，因為您需要此欄位的值才能進行下一個步驟。

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

1. 允許 SP 存取您的 Azure Machine Learning 工作區。 您需要工作區名稱，以及其個別 `-w` 和 `-g` 參數的資源群組名稱。 針對 `--user` 參數，請使用上一個步驟中的 `objectId` 值。 `--role`參數可讓您設定服務主體的存取角色。 在下列範例中，SP 會指派給**擁有**者角色。 

    > [!IMPORTANT]
    > 擁有者存取權允許服務主體在您的工作區中執行幾乎任何作業。 本檔中使用它來示範如何授與存取權;在生產環境中，Microsoft 建議授與服務主體執行您想要的角色所需的最小存取權。 如需詳細資訊，請參閱[管理 Azure Machine Learning 工作區的存取權](how-to-assign-roles.md)。

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    此呼叫不會在成功時產生任何輸出。

### <a name="use-a-service-principal-from-the-sdk"></a>使用 SDK 中的服務主體

若要使用服務主體從 SDK 向您的工作區進行驗證，請使用類別的函式 `ServicePrincipalAuthentication` 。 使用您在建立服務提供者作為參數時所獲得的值。 `tenant_id` 參數會對應到上述的 `tenantId`、`service_principal_id` 對應至 `clientId`、`service_principal_password` 對應至 `clientSecret`。

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

`sp` 變數現在會保留在 SDK 中直接使用的驗證物件。 一般而言，將上述使用的識別碼/祕密儲存在環境變數中是個好主意，如下列程式碼所示。 將儲存在環境變數中，可防止意外將資訊簽入 GitHub 存放庫中。

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

針對在 Python 中執行並主要使用 SDK 的自動工作流程，您可在大部分的情況下使用此物件來進行驗證。 下列程式碼會使用您建立的驗證物件來驗證您的工作區。

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

### <a name="use-a-service-principal-from-the-azure-cli"></a>使用 Azure CLI 中的服務主體

您可以使用服務主體來執行 Azure CLI 命令。 如需詳細資訊，請參閱[使用服務主體登入](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#sign-in-using-a-service-principal)。

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>搭配 REST API 使用服務主體（預覽）

服務主體也可以用來驗證 Azure Machine Learning [REST API](https://docs.microsoft.com/rest/api/azureml/) （預覽）。 您可使用 Azure Active Directory [用戶端認證授與流程](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)，其允許在自動工作流程中進行無周邊驗證的服務對服務呼叫。 這些範例是使用 Python 和 Node.js 中的 [ADAL 程式庫](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)所實作，但您也可以使用任何支援 OpenID Connect 1.0 的開放原始碼程式庫。

> [!NOTE]
> MSAL.js 是比 ADAL 更新的程式庫，但無法使用 MSAL.js 的用戶端認證來進行服務對服務驗證，因為該認證主要適用於與特定使用者繫結的互動式/UI 驗證用戶端程式庫。 我們建議使用如下所示的 ADAL，以透過 REST API 建置自動工作流程。

#### <a name="nodejs"></a>Node.js

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

變數 `tokenResponse` 為物件，其中包含權杖和相關聯的中繼資料 (例如到期時間)。 權杖的有效時間為 1 小時，且可以重新整理，只要再次執行相同呼叫來擷取新的權杖即可。 下列程式碼片段是範例回應。

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

#### <a name="python"></a>Python

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

變數 `token_response` 為字典，其中包含權杖和相關聯的中繼資料 (例如到期時間)。 權杖的有效時間為 1 小時，且可以重新整理，只要再次執行相同呼叫來擷取新的權杖即可。 下列程式碼片段是範例回應。

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

Azure Machine Learning 所建立的模型部署提供兩種驗證方法：

* 以索引**鍵為基礎**：使用靜態金鑰來驗證 web 服務。
* **權杖型**：必須從工作區取得暫時權杖，並用來對 web 服務進行驗證。 此權杖會在一段時間後過期，而且必須重新整理，才能繼續使用 web 服務。

    > [!NOTE]
    > 只有在部署至 Azure Kubernetes Service 時，才可以使用權杖型驗證。

### <a name="key-based-web-service-authentication"></a>以金鑰為基礎的 web 服務驗證

Azure Kubernetes Service （AKS）上部署的 Web 服務預設會*啟用*以金鑰為基礎的驗證。 Azure 容器實例（ACI）部署的服務預設會*停用*金鑰型驗證，但是您可以藉由 `auth_enabled=True` 在建立 ACI web 服務時設定來啟用它。 下列程式碼範例會建立以金鑰為基礎的驗證所啟用的 ACI 部署設定。

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

如需驗證已部署模型的詳細資訊，請參閱針對[部署為 web 服務的模型建立用戶端](how-to-consume-web-service.md)。

### <a name="token-based-web-service-authentication"></a>權杖型 Web 服務驗證

當啟用 Web 服務的權杖驗證時，使用者必須向 Web 服務出示 Machine Learning JSON Web 權杖才能加以存取。 權杖會在指定的時間範圍之後過期，且需要重新整理才能繼續進行呼叫。

* 在部署至 Azure Kubernetes Service 時，**依預設會停用**權杖驗證。
* 部署至 Azure 容器執行個體時，**不支援**權杖驗證。
* 權杖驗證**無法和金鑰型驗證同時使用**。

若要控制權杖驗證，請在 `token_auth_enabled` 建立或更新部署時使用參數：

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
> 如果您使用服務主體來取得權杖，而且想要讓它擁有取得權杖所需的最低存取權，請將它指派給工作區的**讀者**角色。

```python
token, refresh_by = aks_service.get_token()
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

* [如何在定型中使用秘密](how-to-use-secrets-in-runs.md)。
* [定型及部署映像分類模型](tutorial-train-models-with-aml.md)。
* [使用部署為 Web 服務的 Azure Machine Learning 模型](how-to-consume-web-service.md)。
