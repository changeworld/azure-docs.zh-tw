---
title: 受控身分識別
description: 了解受控識別如何在 Azure App Service 和 Azure Functions 中運作、如何設定受控識別，以及如何產生後端資源的權杖。
author: mattchenderson
ms.topic: article
ms.date: 05/27/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 82cb8da1a83e5b1e5430ebecf40f5152c824f6aa
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92742502"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>如何使用 App Service 和 Azure Functions 的受控身分識別

此主題示範如何為 App Service 和 Azure Functions 應用程式建立受控應用程式身分識別，以及如何使用它來存取其他資源。 

> [!Important] 
> 如果您跨越訂用帳戶/租用戶移轉應用程式，App Service 和 Azure Functions 的受控身分識別無法正常運作。 應用程式必須取得新的身分識別，這可透過停用並重新啟用功能來實現。 請參閱下方的[移除身分識別](#remove)。 下游資源也必須更新存取原則，才能使用新的身分識別。

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

## <a name="add-a-system-assigned-identity"></a>新增系統指派的身分識別

若要建立採用系統指派的身分識別的應用程式，您必須在應用程式上設定額外的屬性。

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

若要在入口網站中設定受控身分識別，您需要先像平常一樣建立應用程式，然後再啟用此功能。

1. 像平常一樣在入口網站中建立應用程式。 在入口網站中瀏覽至該應用程式。

2. 如果您使用函式應用程式，請瀏覽至 [平台功能]。 若使用類型的應用程式，請在左側導覽列中向下捲動到 [設定]。

3. 選取 [身分識別]。

4. 在 [系統指派] 索引標籤內，將 [狀態] 切換為 [開啟]。 按一下 [檔案] 。

    ![App Service 中的受控身分識別](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)


> [!NOTE] 
> 若要在 Azure 入口網站中尋找 web 應用程式或位置應用程式的受控識別，請在 [ **企業應用程式** ] 下的 [ **使用者設定** ] 區段中尋找。 通常，位置名稱類似于 `<app name>/slots/<slot name>` 。


### <a name="using-the-azure-cli"></a>使用 Azure CLI

若要使用 Azure CLI 設定受控身分識別，您必須對現有的應用程式使用 `az webapp identity assign` 命令。 有三個選項可供您執行本節中的範例︰

- 從 Azure 入口網站使用 [Azure Cloud Shell](../cloud-shell/overview.md)。
- 請透過下方每個程式碼區塊右上角的 [立即試用] 按鈕，使用內嵌的 Azure Cloud Shell。
- 如果您偏好使用本機 CLI 主控台，請[安裝最新版的 Azure CLI](/cli/azure/install-azure-cli) (2.0.31 或更新版本)。 

下列步驟將逐步引導您建立 web 應用程式，並使用 CLI 指派身分識別給它：

1. 如果您要在本機主控台中使用 Azure CLI，請先使用 [az login](/cli/azure/reference-index#az-login) 登入 Azure。 使用您要部署應用程式且已與 Azure 訂用帳戶相關聯的帳戶：

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

下列步驟將逐步引導您建立應用程式，並使用 Azure PowerShell 為其指派身分識別。 建立 web 應用程式和函數應用程式的指示不同。

#### <a name="using-azure-powershell-for-a-web-app"></a>針對 web 應用程式使用 Azure PowerShell

1. 您可以視需要使用 [Azure PowerShell 指南](/powershell/azure/)中的指示來安裝 Azure PowerShell，然後執行 `Login-AzAccount` 來建立與 Azure 的連線。

2. 使用 Azure PowerShell 建立 Web 應用程式。 如需更多如何使用 Azure PowerShell 搭配 App Service 的相關範例，請參閱 [App Service PowerShell 範例](../app-service/samples-powershell.md)：

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $resourceGroupName -Tier Free

    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $resourceGroupName
    ```

3. 執行 `Set-AzWebApp -AssignIdentity` 命令來建立此應用程式的身分識別：

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName $resourceGroupName 
    ```

#### <a name="using-azure-powershell-for-a-function-app"></a>針對函數應用程式使用 Azure PowerShell

1. 您可以視需要使用 [Azure PowerShell 指南](/powershell/azure/)中的指示來安裝 Azure PowerShell，然後執行 `Login-AzAccount` 來建立與 Azure 的連線。

2. 使用 Azure PowerShell 建立函數應用程式。 如需有關如何搭配使用 Azure PowerShell 與 Azure Functions 的更多範例，請參閱 [Az. 函數參考](/powershell/module/az.functions/?view=azps-4.1.0#functions)：

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a function app with a system-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType SystemAssigned
    ```

您也可以改為使用更新現有的函數應用程式 `Update-AzFunctionApp` 。

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

tenantId 屬性能辨識身分識別所隸屬的 Azure AD 租用戶。 principalId 是應用程式新身分識別的唯一識別碼。 在 Azure AD 內，服務主體的名稱與您提供給 App Service 或 Azure Functions 執行個體的名稱相同。

如果您需要在範本的後續階段中參考這些屬性，您可以透過具有旗標的樣板[ `reference()` ](../azure-resource-manager/templates/template-functions-resource.md#reference)函式來執行 `'Full'` 此動作，如下列範例所示：

```json
{
    "tenantId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.tenantId]",
    "objectId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.principalId]",
}
```

## <a name="add-a-user-assigned-identity"></a>新增使用者指派的身分識別

利用使用者指派的身分識別建立應用程式會需要您建立身分識別，然後將其資源識別碼新增到您的應用程式設定中。

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

首先，您必須建立使用者指派的身分識別資源。

1. 根據[這些指示](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)建立使用者指派的受控識別資源。

2. 像平常一樣在入口網站中建立應用程式。 在入口網站中瀏覽至該應用程式。

3. 如果您使用函式應用程式，請瀏覽至 [平台功能]。 若使用類型的應用程式，請在左側導覽列中向下捲動到 [設定]。

4. 選取 [身分識別]。

5. 在 [使用者指派] 索引標籤內，按一下 [新增]。

6. 搜尋您之前建立的身分識別，並加以選取。 按一下 [新增] 。

    ![App Service 中的受控身分識別](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

下列步驟將逐步引導您建立應用程式，並使用 Azure PowerShell 為其指派身分識別。

> [!NOTE]
> Azure App Service 的 Azure PowerShell commandlet 的目前版本不支援使用者指派的身分識別。 下列指示適用于 Azure Functions。

1. 您可以視需要使用 [Azure PowerShell 指南](/powershell/azure/)中的指示來安裝 Azure PowerShell，然後執行 `Login-AzAccount` 來建立與 Azure 的連線。

2. 使用 Azure PowerShell 建立函數應用程式。 如需有關如何搭配使用 Azure PowerShell 與 Azure Functions 的更多範例，請參閱 [Az. 函數參考](/powershell/module/az.functions/?view=azps-4.1.0#functions)。 下列腳本也會使用，您 `New-AzUserAssignedIdentity` 必須根據 [建立、列出或刪除使用者指派的受控識別，並使用 Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)來個別安裝。

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create a function app with a user-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType UserAssigned -IdentityId $userAssignedIdentity.Id
    ```

您也可以改為使用更新現有的函數應用程式 `Update-AzFunctionApp` 。

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

新增使用者指派的類型，會告訴 Azure 使用針對您應用程式指定的使用者指派身分識別。

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

principalId 是身分識別的唯一識別碼，而此身分識別用於 Azure AD 管理。 clientId 是應用程式新身分識別的唯一識別碼，用來指定要在執行階段呼叫期間使用的身分識別。

## <a name="obtain-tokens-for-azure-resources"></a>取得 Azure 資源的權杖

應用程式可以使用自己的受控識別來取得權杖，存取其他受 Azure AD 保護的資源 (如 Azure Key Vault)。 這些權杖代表存取資源的應用程式，而不是任何特定的應用程式使用者。 

您可能需要設定目標資源，讓應用程式得以存取。 例如，如果您要求權杖來存取 Key Vault，便需要確認是否已新增含有應用程式身分識別的存取原則。 否則即使呼叫含有權杖，依然會遭到拒絕。 若要深入了解哪些資源支援 Azure Active Directory 權杖，請參閱[支援 Azure AD 驗證的 Azure 服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

> [!IMPORTANT]
> 受控識別的後端服務會將每個資源的 URI 快取保留大約 8 小時。 如果您更新特定目標資源的存取原則，並立即擷取該資源的權杖，則可以繼續取得具有過期權限的快取權杖，直到該權杖到期為止。 目前沒有任何方法可以強制重新整理權杖。

有一個簡單的 REST 通訊協定可用來在 App Service 和 Azure Functions 中取得權杖。 這可用於所有應用程式和語言。 若為 .NET 和 JAVA，Azure SDK 提供此通訊協定的抽象概念，並協助本機開發體驗。

### <a name="using-the-rest-protocol"></a>使用 REST 通訊協定

> [!NOTE]
> 此舊版通訊協定 (使用 "2017-09-01" API 版本) 使用的是 `secret` 標頭，而不是 `X-IDENTITY-HEADER`，而且只接受使用者指派的 `clientid` 屬性。 其也會以時間戳記格式傳回 `expires_on`。 MSI_ENDPOINT 可用來做為 IDENTITY_ENDPOINT 的別名，而 MSI_SECRET 則可用來做為 IDENTITY_HEADER 的別名。 Linux 使用量主控方案目前需要此版本的通訊協定。

採用受控身分識別的應用程式有兩個已定義的環境變數：

- IDENTITY_ENDPOINT - 本機權杖服務的 URL。
- IDENTITY_HEADER - 用於協助減輕伺服器端要求偽造 (SSRF) 攻擊的標頭。 值會由平台旋轉。

**IDENTITY_ENDPOINT** 是應用程式要求權杖的來源本機 URL。 若要取得資源的權杖，請向該端點提出包含以下參數的 HTTP GET 要求：

> | 參數名稱    | 在     | 描述                                                                                                                                                                                                                                                                                                                                |
> |-------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | resource          | 查詢  | 資源的 Azure AD 資源 URI，也就是要取得權杖的目標資源。 這可能是其中一個[支援 Azure AD 驗證的 Azure 服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)，或任何其他資源 URI。    |
> | api-version       | 查詢  | 要使用的權杖 API 版本。 除非使用目前僅提供 "2017-09-01" 的 Linux 使用量，否則請使用 "2019-08-01" 或更新版本 (-請參閱上述) 的附注。                                                                                                                                                                                                                                                                 |
> | X-IDENTITY-HEADER | 頁首 | IDENTITY_HEADER 環境變數的值。 此標頭用來協助減輕伺服器端要求偽造 (SSRF) 攻擊。                                                                                                                                                                                                    |
> | client_id         | 查詢  | (選擇性) 要使用的使用者指派身分識別，其用戶端識別碼。 不能用於包含 `principal_id`、`mi_res_id` 或 `object_id` 的要求。 如果省略所有識別碼參數 (`client_id`、`principal_id`、`object_id` 和 `mi_res_id`)，則會使用系統指派的身分識別。                                             |
> | principal_id      | 查詢  | (選擇性) 要使用的使用者指派身分識別，其主體識別碼。 `object_id` 是可代替使用的別名。 不能用於包含 client_id、mi_res_id 或 object_id 的要求。 如果省略所有識別碼參數 (`client_id`、`principal_id`、`object_id` 和 `mi_res_id`)，則會使用系統指派的身分識別。 |
> | mi_res_id         | 查詢  | (選擇性) 要使用的使用者指派身分識別，其 Azure 資源識別碼。 不能用於包含 `principal_id`、`client_id` 或 `object_id` 的要求。 如果省略所有識別碼參數 (`client_id`、`principal_id`、`object_id` 和 `mi_res_id`)，則會使用系統指派的身分識別。                                      |

> [!IMPORTANT]
> 如果您嘗試取得使用者指派身分識別的權杖，則必須包含其中一個選擇性屬性。 否則，權杖服務會嘗試取得系統指派身分識別的權杖，這不一定會存在。

成功的 200 OK 回應包括含以下屬性的 JSON 本文：

> | 屬性名稱 | 描述                                                                                                                                                                                                                                        |
> |---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | access_token  | 所要求的存取權杖。 呼叫端 Web 服務可以使用此權杖來向接收端 Web 服務進行驗證。                                                                                                                               |
> | client_id     | 所使用身分識別的用戶端識別碼。                                                                                                                                                                                                       |
> | expires_on    | 存取權杖到期的時間範圍。 日期以 "1970-01-01T0:0:0Z UTC" 起算的秒數表示 (對應至權杖的 `exp` 宣告)。                                                                                |
> | not_before    | 存取權杖生效且可被接受的時間範圍。 日期以 "1970-01-01T0:0:0Z UTC" 起算的秒數表示 (對應至權杖的 `nbf` 宣告)。                                                      |
> | resource      | 要求存取權杖所針對的資源，符合要求的 `resource` 查詢字串參數。                                                                                                                               |
> | token_type    | 表示權杖類型值。 Azure AD 唯一支援的類型是 FBearer。 如需有關持有人權杖的詳細資訊，請參閱 [OAuth 2.0 授權架構︰持有人權杖使用方式 (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) \(英文\)。 |

該回應與 [ AD 服務對服務存取權杖要求的回應](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response)相同。

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
const rp = require('request-promise');
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

對於 .NET 應用程式和函式來說，採用受控身分識別最簡單的方法就是透過 Microsoft.Azure.Services.AppAuthentication 套件。 該程式庫還能讓您使用來自 Visual Studio、[Azure CLI](/cli/azure) 或 Active Directory 整合式驗證的使用者帳戶，在部署機器上以本機方式測試程式碼。 裝載在雲端時，預設會使用系統指派的身分識別，但您可以使用連接字串環境變數（參考使用者指派之身分識別的用戶端識別碼）來自訂此行為。 如需此程式庫之開發選項的詳細資訊，請參閱 [AppAuthentication 參考]。 本節示範如何在您的程式碼中開始使用程式庫。

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

如果您想要使用使用者指派的受控識別，您可以將 `AzureServicesAuthConnectionString` 應用程式設定設為 `RunAs=App;AppId=<clientId-guid>` 。 取代 `<clientId-guid>` 為您要使用之身分識別的用戶端識別碼。 您可以使用自訂應用程式設定，並將其值傳遞至 Azureservicetokenprovider 會的函式，以定義多個這類連接字串。

```csharp
    var identityConnectionString1 = Environment.GetEnvironmentVariable("UA1_ConnectionString");
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider(identityConnectionString1);
    
    var identityConnectionString2 = Environment.GetEnvironmentVariable("UA2_ConnectionString");
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider(identityConnectionString2);
```

若要深入瞭解如何設定 Azureservicetokenprovider 會和它公開的作業，請參閱 [AppAuthentication 參考] ，以及 [使用 MSI .net 範例的 App Service 和 KeyVault](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)。

### <a name="using-the-azure-sdk-for-java"></a>使用 Azure SDK for Java

對於 Java 應用程式和函式，使用受控識別的最簡單方式是透過 [Azure SDK for Java](https://github.com/Azure/azure-sdk-for-java)。 本節示範如何在您的程式碼中開始使用程式庫。

1. 加入 [Azure SDK 程式庫](https://mvnrepository.com/artifact/com.microsoft.azure/azure)的參考。 對於 Maven 專案，您可以將此程式碼片段新增至專案 POM 檔案的 `dependencies` 區段：

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. 使用 `AppServiceMSICredentials` 物件進行驗證。 這個範例會示範如何使用這種機制來處理 Azure Key Vault：

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

您可用建立身分識別的相同方式，使用入口網站、PowerShell 或 CLI 停用功能，來將系統指派的身分識別移除。 使用者指派的身分識別可以個別移除。 若要移除所有身分識別，請將識別類型設定為 "None"。

以這種方式移除系統指派的身分識別，也會從 AAD 將其刪除。 當您刪除應用程式資源時，系統指派的身分識別會自動從 Azure AD 移除。

移除 [ARM 範本](#using-an-azure-resource-manager-template)中的所有身分識別：

```json
"identity": {
    "type": "None"
}
```

若要移除 Azure PowerShell 中的所有身分識別 (只 Azure Functions) ：

```azurepowershell-interactive
# Update an existing function app to have IdentityType "None".
Update-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -IdentityType None
```

> [!NOTE]
> 還可以設定另一個應用程式設定：WEBSITE_DISABLE_MSI，這只會停用本機權杖服務。 不過，系統會將身分識別留在原地，且工具仍會將受控身分識別顯示為「開啟」或「已啟用」。 因此，不建議使用這個設定。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用受控身分識別安全地存取 SQL Database](app-service-web-tutorial-connect-msi.md)

[Microsoft.Azure.Services.AppAuthentication 參考]: ../key-vault/general/service-to-service-authentication.md
