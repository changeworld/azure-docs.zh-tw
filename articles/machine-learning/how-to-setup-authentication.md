---
title: 設定驗證
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure Machine Learning 中設定各種資源和工作流程的驗證。
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 11/05/2020
ms.topic: conceptual
ms.custom: how-to, has-adal-ref, devx-track-js, devx-track-azurecli, contperfq2
ms.openlocfilehash: ca8a36584c09d850ed1daab8cba301b244f76526
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96447018"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>設定 Azure Machine Learning 資源和工作流程的驗證


瞭解如何設定 Azure Machine Learning 工作區的驗證。 Azure Machine Learning 工作區的驗證是以 __Azure Active Directory__ (Azure AD) 為基礎。 一般情況下，您可以在連接到工作區時使用三個驗證工作流程：

* __互動式__：您在 Azure Active Directory 中使用您的帳戶來直接驗證，或取得用於驗證的權杖。 互動式驗證會在 _實驗和反復開發_ 期間使用。 互動式驗證可讓您控制對資源的存取 (例如，以每個使用者為基礎的 web 服務) 。

* __服務主體__：您會在 Azure Active Directory 中建立服務主體帳戶，並使用它來驗證或取得權杖。 當您需要 _自動化程式來_ 向服務驗證，而不需要使用者互動時，就會使用服務主體。 例如，每次定型程式碼變更時，會訓練並測試模型的持續整合和部署腳本。

* __受控識別__：當您 _在 azure 虛擬機器上_ 使用 Azure Machine Learning SDK 時，您可以使用 azure 的受控識別。 此工作流程可讓 VM 使用受控識別來連線到工作區，而不需將認證儲存在 Python 程式碼中，或提示使用者進行驗證。 您也可以將 Azure Machine Learning 計算叢集設定為使用受控識別，以在 _定型模型_ 時使用受控識別來存取工作區。

> [!IMPORTANT]
> 無論使用哪一種驗證工作流程，Azure 角色型存取控制 (Azure RBAC) 用來限定資源 (授權) 的存取層級。 例如，系統管理員或自動化程式可能具有建立計算實例的存取權，但不能使用它，而資料科學家可以使用它，但無法刪除或建立。 如需詳細資訊，請參閱 [管理 Azure Machine Learning 工作區的存取權](how-to-assign-roles.md)。

## <a name="prerequisites"></a>必要條件

* 建立 [Azure Machine Learning 工作區](how-to-manage-workspace.md)。
* [設定您的開發環境](how-to-configure-environment.md) 以安裝 Azure Machine Learning sdk，或使用已安裝 sdk 的 [Azure Machine Learning 計算實例](concept-azure-machine-learning-architecture.md#compute-instance) 。

## <a name="azure-active-directory"></a>Azure Active Directory

您工作區的所有驗證工作流程都依賴 Azure Active Directory。 如果您想要讓使用者使用個別帳戶進行驗證，他們必須有 Azure AD 中的帳戶。 如果您想要使用服務主體，則必須存在於您的 Azure AD 中。 受控識別也是 Azure AD 的功能。 

如需 Azure AD 的詳細資訊，請參閱 [什麼是 Azure Active Directory 驗證](..//active-directory/authentication/overview-authentication.md)。

當您建立 Azure AD 帳戶之後，請參閱 [管理 Azure Machine Learning 工作區的存取](how-to-assign-roles.md) 權，以取得將工作區的存取權授與 Azure Machine Learning 中其他作業的相關資訊。

## <a name="configure-a-service-principal"></a>定服務主體

若要使用服務主體 (SP) ，您必須先建立 SP，並授與它對您工作區的存取權。 如先前所述，Azure RBAC)  (的 Azure 角色型存取控制可用來控制存取，因此您也必須決定授與 SP 的存取權。

> [!IMPORTANT]
> 使用服務主體時，請將它所用的 __工作所需的最小存取權__ 授與給它。 例如，您不會將服務主體擁有者或參與者存取權授與服務主體擁有者或參與者存取權，因為它是用來讀取 web 部署的存取權杖。
>
> 授與最小存取權的原因是，服務主體會使用密碼進行驗證，而且密碼可能會儲存為自動化腳本的一部分。 如果密碼洩漏，則擁有特定工作所需的最低存取權可將 SP 的惡意使用降至最低。

建立 SP 並授與存取權給工作區的最簡單方式是使用 [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)。 若要建立服務主體，並授與它對您工作區的存取權，請使用下列步驟：

> [!NOTE]
> 您必須是訂用帳戶的系統管理員，才能執行上述所有步驟。

1. 向您的 Azure 訂用帳戶進行驗證：

    ```azurecli-interactive
    az login
    ```

    如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入登入頁面。 否則，您需要開啟瀏覽器，並遵循命令列上的指示。 這些指示需要瀏覽至 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)，並輸入授權碼。

    如果您有多個 Azure 訂用帳戶，您可以使用 `az account set -s <subscription name or ID>` 命令來設定訂用帳戶。 如需詳細資訊，請參閱[使用多個 Azure 訂用帳戶](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)。

    如需其他驗證方法，請參閱[使用 Azure CLI 登入](/cli/azure/authenticate-azure-cli?preserve-view=true&view=azure-cli-latest)。

1. 安裝 Azure Machine Learning 擴充功能：

    ```azurecli-interactive
    az extension add -n azure-cli-ml
    ```

1. 建立服務主體。 下列範例會建立名為 **ml-auth** 的 SP：

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

1. 使用上一個步驟中所傳回的值，取出服務主體的詳細資料 `clientId` ：

    ```azurecli-interactive
    az ad sp show --id your-client-id
    ```

    下列 JSON 是命令輸出的簡化範例。 記下 `objectId` 欄位，因為您需要此欄位的值才能進行下一個步驟。

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

1. 允許 SP 存取您的 Azure Machine Learning 工作區。 您需要工作區名稱，以及其個別 `-w` 和 `-g` 參數的資源群組名稱。 針對 `--user` 參數，請使用上一個步驟中的 `objectId` 值。 `--role`參數可讓您設定服務主體的存取角色。 在下列範例中，會將 SP 指派給 **擁有** 者角色。 

    > [!IMPORTANT]
    > 擁有者存取權可讓服務主體在您的工作區中進行幾乎任何作業。 本檔中使用它來示範如何授與存取權;在生產環境中，Microsoft 建議授與服務主體執行您想要的角色所需的最少存取權。 如需有關使用您的案例所需的存取權來建立自訂角色的詳細資訊，請參閱 [管理 Azure Machine Learning 工作區的存取權](how-to-assign-roles.md)。

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    此呼叫不會在成功時產生任何輸出。

## <a name="configure-a-managed-identity"></a>設定受控識別

> [!IMPORTANT]
> 只有使用 Azure 虛擬機器或 Azure Machine Learning 計算叢集的 Azure Machine Learning SDK 時，才支援受控識別。 搭配使用受控識別與計算叢集的功能目前為預覽狀態。

### <a name="managed-identity-with-a-vm"></a>具有 VM 的受控識別

1. [針對 VM 上的 Azure 資源啟用系統指派的受控識別](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)。

1. 從 [Azure 入口網站](https://portal.azure.com)選取您的工作區，然後選取 [__存取控制] (IAM)__]、[__新增角色指派__]，然後從 [__將存取權指派給__] 下拉式清單選取 [__虛擬機器__]。 最後，選取您 VM 的身分識別。

1. 選取要指派給此身分識別的角色。 例如，參與者或自訂角色。 如需詳細資訊，請參閱 [控制資源的存取權](how-to-assign-roles.md)。

### <a name="managed-identity-with-compute-cluster"></a>具有計算叢集的受控識別

如需詳細資訊，請參閱 [設定計算叢集的受控識別](how-to-create-attach-compute-cluster.md#managed-identity)。

<a id="interactive-authentication"></a>

## <a name="use-interactive-authentication"></a>使用互動式驗證

> [!IMPORTANT]
> 互動式驗證會使用您的瀏覽器，而且需要 cookie (包括) 協力廠商 cookie。 如果您已停用 cookie，可能會收到「我們無法將您登入」之類的錯誤。 如果您已啟用 [Azure AD Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)，也可能會發生此錯誤。

檔和範例中的大部分範例都會使用互動式驗證。 例如，使用 SDK 時，有兩個函式呼叫會自動提示您使用以 UI 為基礎的驗證流程：

* 呼叫 `from_config()` 函式會發出提示。

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

    `from_config()` 函式會尋找 JSON 檔案，其中包含工作區連線資訊。

* 使用此函式 `Workspace` 來提供訂用帳戶、資源群組和工作區資訊，也會提示進行互動式驗證。

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

使用 Azure CLI 時， `az login` 命令是用來驗證 CLI 會話。 如需詳細資訊，請參閱 [開始使用 Azure CLI](/cli/azure/get-started-with-azure-cli)。

> [!TIP]
> 如果您是從先前使用 Azure CLI 以互動方式驗證的環境中使用 SDK，則可以使用此類別，透過 `AzureCliAuthentication` CLI 所快取的認證來驗證工作區：
>
> ```python
> from azureml.core.authentication import AzureCliAuthentication
> cli_auth = AzureCliAuthentication()
> ws = Workspace(subscription_id="your-sub-id",
>                resource_group="your-resource-group-id",
>                workspace_name="your-workspace-name",
>                auth=cli_auth
>                )
> ```

<a id="service-principal-authentication"></a>

## <a name="use-service-principal-authentication"></a>使用服務主體驗證

若要從 SDK 使用服務主體驗證您的工作區，請使用類別的函式 `ServicePrincipalAuthentication` 。 當您建立服務提供者作為參數時，請使用您取得的值。 `tenant_id` 參數會對應到上述的 `tenantId`、`service_principal_id` 對應至 `clientId`、`service_principal_password` 對應至 `clientSecret`。

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

`sp` 變數現在會保留在 SDK 中直接使用的驗證物件。 一般而言，將上述使用的識別碼/祕密儲存在環境變數中是個好主意，如下列程式碼所示。 儲存在環境變數中，可防止資訊不慎簽入 GitHub 存放庫。

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

### <a name="use-a-service-principal-from-the-azure-cli"></a>從 Azure CLI 使用服務主體

您可以使用服務主體來 Azure CLI 命令。 如需詳細資訊，請參閱 [使用服務主體登入](/cli/azure/create-an-azure-service-principal-azure-cli?preserve-view=true&view=azure-cli-latest#sign-in-using-a-service-principal)。

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>使用具有 REST API (預覽的服務主體) 

服務主體也可以用來向 Azure Machine Learning [REST API](/rest/api/azureml/) (preview) 進行驗證。 您可使用 Azure Active Directory [用戶端認證授與流程](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)，其允許在自動工作流程中進行無周邊驗證的服務對服務呼叫。 這些範例是使用 Python 和 Node.js 中的 [ADAL 程式庫](../active-directory/azuread-dev/active-directory-authentication-libraries.md)所實作，但您也可以使用任何支援 OpenID Connect 1.0 的開放原始碼程式庫。

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

#### <a name="java"></a>Java

在 JAVA 中，使用標準 REST 呼叫來取得持有人權杖：

```java
String tenantId = "your-tenant-id";
String clientId = "your-client-id";
String clientSecret = "your-client-secret";
String resourceManagerUrl = "https://management.azure.com";

HttpRequest tokenAuthenticationRequest = tokenAuthenticationRequest(tenantId, clientId, clientSecret, resourceManagerUrl);

HttpClient client = HttpClient.newBuilder().build();
Gson gson = new Gson();
HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
if (response.statusCode == 200)
{
     body = gson.fromJson(body, AuthenticationBody.class);

    // ... etc ... 
}
// ... etc ...

static HttpRequest tokenAuthenticationRequest(String tenantId, String clientId, String clientSecret, String resourceManagerUrl){
    String authUrl = String.format("https://login.microsoftonline.com/%s/oauth2/token", tenantId);
    String clientIdParam = String.format("client_id=%s", clientId);
    String resourceParam = String.format("resource=%s", resourceManagerUrl);
    String clientSecretParam = String.format("client_secret=%s", clientSecret);

    String bodyString = String.format("grant_type=client_credentials&%s&%s&%s", clientIdParam, resourceParam, clientSecretParam);

    HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create(authUrl))
            .POST(HttpRequest.BodyPublishers.ofString(bodyString))
            .build();
    return request;
}

class AuthenticationBody {
    String access_token;
    String token_type;
    int expires_in;
    String scope;
    String refresh_token;
    String id_token;
    
    AuthenticationBody() {}
}
```

上述程式碼必須處理以外的例外狀況和狀態碼 `200 OK` ，但會顯示模式： 

- 使用用戶端識別碼和密碼來驗證您的程式是否應該具有存取權
- 使用您的租使用者識別碼來指定 `login.microsoftonline.com` 應查看的位置
- 使用 Azure Resource Manager 作為授權權杖的來源

## <a name="use-managed-identity-authentication"></a>使用受控識別驗證

若要從使用受控識別設定的 VM 或計算叢集驗證工作區，請使用 `MsiAuthentication` 類別。 下列範例示範如何使用這個類別來驗證工作區：

```python
from azureml.core.authentication import MsiAuthentication

msi_auth = MsiAuthentication()

ws = Workspace(subscription_id="your-sub-id",
                resource_group="your-resource-group-id",
                workspace_name="your-workspace-name",
                auth=msi_auth
                )
```

## <a name="next-steps"></a>後續步驟

* [如何在訓練中使用秘密](how-to-use-secrets-in-runs.md)。
* [如何針對部署為 web 服務的模型設定驗證](how-to-authenticate-web-service.md)。
* [使用部署為 Web 服務的 Azure Machine Learning 模型](how-to-consume-web-service.md)。