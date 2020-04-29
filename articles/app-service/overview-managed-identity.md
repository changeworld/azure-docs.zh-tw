---
title: 受控身分識別
description: 瞭解受控識別在 Azure App Service 和 Azure Functions 中的使用方式、如何設定受控識別，以及如何產生後端資源的權杖。
author: mattchenderson
ms.topic: article
ms.date: 04/14/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.openlocfilehash: 875d2bbebdfa95c6d180979399d876eb2afc01b4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81392530"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>如何使用 App Service 和 Azure Functions 的受控身分識別

> [!Important] 
> 如果您跨越訂用帳戶/租用戶移轉應用程式，App Service 和 Azure Functions 的受控身分識別將無法正常運作。 應用程式將必須取得新的身分識別，這可透過停用並重新啟用功能來實現。 請參閱下方的[移除身分識別](#remove)。 下游資源也必須更新存取原則，才能使用新的身分識別。

此主題示範如何為 App Service 和 Azure Functions 應用程式建立受控應用程式身分識別，以及如何使用它來存取其他資源。 Azure Active Directory （Azure AD）的受控識別可讓您的應用程式輕鬆地存取其他受 Azure AD 保護的資源，例如 Azure Key Vault。 身分識別由 Azure 平台負責管理，因此您不需要佈建或輪替任何密碼。 如需 Azure AD 中受控識別的詳細資訊，請參閱[適用于 Azure 資源的受控](../active-directory/managed-identities-azure-resources/overview.md)識別。

您的應用程式可以授與兩種類型的身分識別：

- **系統指派的身分識別**會繫結至您的應用程式，如果您的應用程式已刪除，則會被刪除。 應用程式只能有一個系統指派的身分識別。
- **使用者指派**的身分識別是一種獨立的 Azure 資源，可指派給您的應用程式。 應用程式可以有多個使用者指派的身分識別。

## <a name="add-a-system-assigned-identity"></a>新增系統指派的身分識別

若要建立採用系統指派的身分識別的應用程式，您必須在應用程式上設定額外的屬性。

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

若要在入口網站中設定受控身分識別，您需要先像平常一樣建立應用程式，然後再啟用此功能。

1. 像平常一樣在入口網站中建立應用程式。 在入口網站中瀏覽至該應用程式。

2. 如果您使用函式應用程式，請瀏覽至 [平台功能]****。 若使用類型的應用程式，請在左側導覽列中向下捲動到 [設定]****。

3. 選取 [身分**識別**]。

4. 在 [系統指派]**** 索引標籤內，將 [狀態]**** 切換為 [開啟]****。 按一下 **[儲存]** 。

    ![App Service 中的受控身分識別](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)

### <a name="using-the-azure-cli"></a>使用 Azure CLI

若要使用 Azure CLI 設定受控身分識別，您必須對現有的應用程式使用 `az webapp identity assign` 命令。 有三個選項可供您執行本節中的範例︰

- 從 Azure 入口網站使用 [Azure Cloud Shell](../cloud-shell/overview.md)。
- 透過位於下方每個程式碼區塊右上角的 [試試看] 按鈕，使用內嵌的 Azure Cloud Shell。
- 如果您偏好使用本機 CLI 主控台，請[安裝最新版的 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 或更新版本)。 

下列步驟將逐步引導您建立 web 應用程式，並使用 CLI 指派身分識別給它：

1. 如果您要在本機主控台中使用 Azure CLI，請先使用 [az login](/cli/azure/reference-index#az-login) 登入 Azure。 使用與您想要部署應用程式的 Azure 訂用帳戶相關聯的帳戶：

    ```azurecli-interactive
    az login
    ```

2. 使用 CLI 建立 web 應用程式。 如需如何使用 CLI 搭配 App Service 的相關範例，請參閱 [App Service CLI 範例](../app-service/samples-cli.md)：

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. 執行 `identity assign` 命令來建立此應用程式的身分識別：

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

下列步驟將逐步引導您建立 Web 應用程式，並使用 Azure PowerShell 指派身分識別給它：

1. 如有需要，請使用[Azure PowerShell 指南](/powershell/azure/overview)中找到的指示來安裝 Azure PowerShell，然後執行`Login-AzAccount`來建立與 Azure 的連線。

2. 使用 Azure PowerShell 建立 Web 應用程式。 如需更多如何使用 Azure PowerShell 搭配 App Service 的相關範例，請參閱 [App Service PowerShell 範例](../app-service/samples-powershell.md)：

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name myResourceGroup -Location $location

    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free

    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. 執行 `Set-AzWebApp -AssignIdentity` 命令來建立此應用程式的身分識別：

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本

您可以使用 Azure Resource Manager 範本來將 Azure 資源的部署自動化。 若要深入了解如何部署到 App Service 和 Functions，請參閱[在 App Service 中將資源部署自動化](../app-service/deploy-complex-application-predictably.md)和[在 Azure Functions 中將資源部署自動化](../azure-functions/functions-infrastructure-as-code.md)。

對於所有 `Microsoft.Web/sites` 型別的資源來說，您可以在資源定義中加入以下屬性，以建立採用身分識別的資源：

```json
"identity": {
    "type": "SystemAssigned"
}
```

> [!NOTE]
> 應用程式可以同時具有系統指派的身分識別和使用者指派的身分識別。 在此情況下，`type` 屬性將會是 `SystemAssigned,UserAssigned`

新增系統指派的類型能告訴 Azure 該如何建立及管理應用程式的身分識別。

例如，Web 應用程式可能與下圖中的範例相似：

```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

當網站建立時，它擁有以下額外屬性：

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

TenantId 屬性會識別身分識別所屬的 Azure AD 租使用者。 principalId 是應用程式新身分識別的唯一識別碼。 在 Azure AD 內，服務主體的名稱與您提供給 App Service 或 Azure Functions 實例相同。

## <a name="add-a-user-assigned-identity"></a>新增使用者指派的身分識別

利用使用者指派的身分識別建立應用程式會需要您建立身分識別，然後將其資源識別碼新增到您的應用程式設定中。

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

首先，您必須建立使用者指派的身分識別資源。

1. 根據[這些指示](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)建立使用者指派的受控識別資源。

2. 像平常一樣在入口網站中建立應用程式。 在入口網站中瀏覽至該應用程式。

3. 如果您使用函式應用程式，請瀏覽至 [平台功能]****。 若使用類型的應用程式，請在左側導覽列中向下捲動到 [設定]****。

4. 選取 [身分**識別**]。

5. 在 [**使用者指派**] 索引標籤中，按一下 [**新增**]。

6. 搜尋您之前建立的身分識別，並加以選取。 按一下 [加入]  。

    ![App Service 中的受控身分識別](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本

您可以使用 Azure Resource Manager 範本來將 Azure 資源的部署自動化。 若要深入了解如何部署到 App Service 和 Functions，請參閱[在 App Service 中將資源部署自動化](../app-service/deploy-complex-application-predictably.md)和[在 Azure Functions 中將資源部署自動化](../azure-functions/functions-infrastructure-as-code.md)。

對於所有 `Microsoft.Web/sites` 型別的資源來說，您可以在資源定義中加入以下區塊，以所需之身分識別的資源識別碼取代 `<RESOURCEID>`，來建立採用身分識別的資源：

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

> [!NOTE]
> 應用程式可以同時具有系統指派的身分識別和使用者指派的身分識別。 在此情況下，`type` 屬性將會是 `SystemAssigned,UserAssigned`

新增使用者指派的類型，會指示 Azure 使用為您的應用程式指定的使用者指派身分識別。

例如，Web 應用程式可能與下圖中的範例相似：

```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

當網站建立時，它擁有以下額外屬性：

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

PrincipalId 是用於 Azure AD 管理之身分識別的唯一識別碼。 ClientId 是應用程式新身分識別的唯一識別碼，用來指定執行時間呼叫期間要使用的身分識別。

## <a name="obtain-tokens-for-azure-resources"></a>取得 Azure 資源的權杖

應用程式可以使用其受控識別來取得權杖，以存取受 Azure AD 保護的其他資源，例如 Azure Key Vault。 這些權杖代表存取資源的應用程式，而不是任何特定的應用程式使用者。 

您可能需要設定目標資源，讓應用程式得以存取。 例如，如果您要求權杖來存取 Key Vault，您必須確定您已新增包含應用程式身分識別的存取原則。 否則即使呼叫含有權杖，依然會遭到拒絕。 若要深入了解哪些資源支援 Azure Active Directory 權杖，請參閱[支援 Azure AD 驗證的 Azure 服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

> [!IMPORTANT]
> 受控識別的後端服務會保留每個資源 URI 的快取約8小時。 如果您更新特定目標資源的存取原則，並立即抓取該資源的權杖，您可以繼續取得具有過期許可權的快取權杖，直到該權杖過期為止。 目前沒有任何方法可以強制重新整理權杖。

有一個簡單的 REST 通訊協定可用來在 App Service 和 Azure Functions 中取得權杖。 這可用於所有應用程式和語言。 針對 .NET 和 JAVA，Azure SDK 提供此通訊協定的抽象概念，並協助本機開發體驗。

### <a name="using-the-rest-protocol"></a>使用 REST 通訊協定

採用受控身分識別的應用程式有兩個已定義的環境變數：

- IDENTITY_ENDPOINT-本機 token 服務的 URL。
- IDENTITY_HEADER-用來協助減輕伺服器端偽造要求（SSRF）攻擊的標頭。 值會由平台旋轉。

**IDENTITY_ENDPOINT**是您的應用程式可以用來要求權杖的本機 URL。 若要取得資源的權杖，請向該端點提出包含以下參數的 HTTP GET 要求：

> | 參數名稱    | 在     | 描述                                                                                                                                                                                                                                                                                                                                |
> |-------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | 資源          | 查詢  | 應取得權杖之資源的 Azure AD 資源 URI。 這可能是其中一個[支援 Azure AD 驗證的 Azure 服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)，或任何其他資源 URI。    |
> | api-version       | 查詢  | 要使用的權杖 API 版本。 請使用 "2019-08-01" 或更新版本。                                                                                                                                                                                                                                                                 |
> | X-IDENTITY-標頭 | 頁首 | IDENTITY_HEADER 環境變數的值。 此標頭用來協助減輕伺服器端要求偽造 (SSRF) 攻擊。                                                                                                                                                                                                    |
> | client_id         | 查詢  | 選擇性要使用之使用者指派身分識別的用戶端識別碼。 不能用於包含`principal_id`、 `mi_res_id`或`object_id`的要求。 如果省略所有識別碼參數`client_id`（ `principal_id`、 `object_id`、和`mi_res_id`），則會使用系統指派的身分識別。                                             |
> | principal_id      | 查詢  | 選擇性要使用之使用者指派身分識別的主體識別碼。 `object_id`這是可代替使用的別名。 不能用於包含 client_id、mi_res_id 或 object_id 的要求。 如果省略所有識別碼參數`client_id`（ `principal_id`、 `object_id`、和`mi_res_id`），則會使用系統指派的身分識別。 |
> | mi_res_id         | 查詢  | 選擇性要使用之使用者指派身分識別的 Azure 資源識別碼。 不能用於包含`principal_id`、 `client_id`或`object_id`的要求。 如果省略所有識別碼參數`client_id`（ `principal_id`、 `object_id`、和`mi_res_id`），則會使用系統指派的身分識別。                                      |

> [!IMPORTANT]
> 如果您嘗試取得使用者指派身分識別的權杖，您必須包含其中一個選擇性屬性。 否則，權杖服務會嘗試取得系統指派之身分識別的權杖，而這不一定會存在。

成功的 200 OK 回應包括含以下屬性的 JSON 本文：

> | 屬性名稱 | 說明                                                                                                                                                                                                                                        |
> |---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | access_token  | 要求的存取權杖。 呼叫端 Web 服務可以使用此權杖來向接收端 Web 服務進行驗證。                                                                                                                               |
> | client_id     | 所使用之身分識別的用戶端識別碼。                                                                                                                                                                                                       |
> | expires_on    | 存取權杖到期的時間範圍。 日期以 "1970-01-01T0:0:0Z UTC" 起算的秒數表示 (對應至權杖的 `exp` 宣告)。                                                                                |
> | not_before    | 存取權杖生效且可被接受的時間範圍。 日期以 "1970-01-01T0:0:0Z UTC" 起算的秒數表示 (對應至權杖的 `nbf` 宣告)。                                                      |
> | 資源      | 要求存取權杖所針對的資源，符合要求的 `resource` 查詢字串參數。                                                                                                                               |
> | token_type    | 指出權杖類型的值。 Azure AD 唯一支援的類型是 FBearer。 如需持有人權杖的詳細資訊，請參閱[OAuth 2.0 授權架構：持有人權杖用法（RFC 6750）](https://www.rfc-editor.org/rfc/rfc6750.txt)。 |

此回應與[Azure AD 服務對服務存取權杖要求的回應](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response)相同。

> [!NOTE]
> 此通訊協定的舊版（使用 "2017-09-01" API 版本）使用了`secret`標頭，而不`X-IDENTITY-HEADER`是，而且只`clientid`接受使用者指派的屬性。 它也會`expires_on`以時間戳記格式傳回。 MSI_ENDPOINT 可用來做為 IDENTITY_ENDPOINT 的別名，而 MSI_SECRET 則可用來做為 IDENTITY_HEADER 的別名。

### <a name="rest-protocol-examples"></a>REST 通訊協定範例

範例要求如下所示：

```http
GET /MSI/token?resource=https://vault.azure.net&api-version=2019-08-01 HTTP/1.1
Host: localhost:4141
X-IDENTITY-HEADER: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

範例回應如下所示：

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "1586984735",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer",
    "client_id": "5E29463D-71DA-4FE0-8E69-999B57DB23B0"
}
```

### <a name="code-examples"></a>程式碼範例

# <a name="net"></a>[.NET](#tab/dotnet)

> [!TIP]
> 對於 .NET 語言，您也可以使用 [Microsoft.Azure.Services.AppAuthentication](#asal) 而不需要自行製作要求。

```csharp
private readonly HttpClient _client;
// ...
public async Task<HttpResponseMessage> GetToken(string resource)  {
    var request = new HttpRequestMessage(HttpMethod.Get, 
        String.Format("{0}/?resource={1}&api-version=2019-08-01", Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT"), resource));
    request.Headers.Add("X-IDENTITY-HEADER", Environment.GetEnvironmentVariable("IDENTITY_HEADER"));
    return await _client.SendAsync(request);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const rp = require('request-promise');
const getToken = function(resource, cb) {
    let options = {
        uri: `${process.env["IDENTITY_ENDPOINT"]}/?resource=${resource}&api-version=2019-08-01`,
        headers: {
            'X-IDENTITY-HEADER': process.env["IDENTITY_HEADER"]
        }
    };
    rp(options)
        .then(cb);
}
```

# <a name="python"></a>[Python](#tab/python)

```python
import os
import requests

identity_endpoint = os.environ["IDENTITY_ENDPOINT"]
identity_header = os.environ["IDENTITY_HEADER"]

def get_bearer_token(resource_uri):
    token_auth_uri = f"{identity_endpoint}?resource={resource_uri}&api-version=2019-08-01"
    head_msi = {'X-IDENTITY-HEADER':identity_header}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:IDENTITY_ENDPOINT + "?resource=$resourceURI&api-version=2019-08-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"X-IDENTITY-HEADER"="$env:IDENTITY_HEADER"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="using-the-microsoftazureservicesappauthentication-library-for-net"></a><a name="asal"></a>將 Microsoft.Azure.Services.AppAuthentication 程式庫運用在 .NET 上

對於 .NET 應用程式和函式來說，採用受控身分識別最簡單的方法就是透過 Microsoft.Azure.Services.AppAuthentication 套件。 該程式庫還能讓您使用來自 Visual Studio、[Azure CLI](/cli/azure) 或 Active Directory 整合式驗證的使用者帳戶，在部署機器上以本機方式測試程式碼。 如需使用此程式庫之本機開發選項的詳細資訊，請參閱 [Microsoft.Azure.Services.AppAuthentication 參考]。 本節示範如何在您的程式碼中開始使用程式庫。

1. 將對 [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 及任何其他必要 NuGet 套件的參考新增到您的應用程式。 以下範例也使用 [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)。

2. 將下列程式碼新增至您的應用程式，將其目標修改成正確的資源。 此範例示範兩種使用 Azure Key Vault 的方式：

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

若要深入了解 Microsoft.Azure.Services.AppAuthentication 和它公開的作業，請參閱 [Microsoft.Azure.Services.AppAuthentication 參考]與[採用 MSI 的 App Service 和 KeyVault .NET 範例](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)。

### <a name="using-the-azure-sdk-for-java"></a>使用 Azure SDK for JAVA

針對 JAVA 應用程式和函式，使用受控識別最簡單的方式是透過[AZURE SDK For JAVA](https://github.com/Azure/azure-sdk-for-java)。 本節示範如何在您的程式碼中開始使用程式庫。

1. 新增[AZURE SDK 程式庫](https://mvnrepository.com/artifact/com.microsoft.azure/azure)的參考。 對於 Maven 專案，您可以將此程式碼片段新增`dependencies`至專案 POM 檔案的區段：

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. 使用`AppServiceMSICredentials`物件進行驗證。 這個範例會示範如何使用這種機制來處理 Azure Key Vault：

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove-an-identity"></a><a name="remove"></a>移除身分識別

您可用建立身分識別的相同方式，使用入口網站、PowerShell 或 CLI 停用功能，來將系統指派的身分識別移除。 使用者指派的身分識別可以個別移除。 若要移除所有身分識別，請在[ARM 範本](#using-an-azure-resource-manager-template)中將類型設定為 "None"：

```json
"identity": {
    "type": "None"
}
```

以這種方式移除系統指派的身分識別，也會將它從 Azure AD 中刪除。 當您刪除應用程式資源時，系統指派的身分識別也會自動從 Azure AD 中移除。

> [!NOTE]
> 還可以設定另一個應用程式設定：WEBSITE_DISABLE_MSI，這只會停用本機權杖服務。 不過，系統會將身分識別留在原地，且工具仍會將受控身分識別顯示為「開啟」或「已啟用」。 因此，不建議使用這個設定。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用受控身分識別安全地存取 SQL Database](app-service-web-tutorial-connect-msi.md)

[Microsoft.Azure.Services.AppAuthentication 參考]: https://go.microsoft.com/fwlink/p/?linkid=862452
