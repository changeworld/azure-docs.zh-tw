---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 07/23/2019
ms.openlocfilehash: 8754504655cdd08c9bf9f89311cb6c5d1057f0e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262265"
---
## <a name="authenticate-with-azure-active-directory"></a>向 Azure Active Directory 進行驗證

> [!IMPORTANT]
> 1. 目前，**只有**電腦視覺 API、人臉 API、文本分析 API、沉浸式讀取器、表單識別器、異常檢測器以及除必應自訂搜索支援使用 Azure 活動目錄 （AAD） 身份驗證之外的所有必應服務。
> 2. AAD 身份驗證必須始終與 Azure 資源的自訂子功能變數名稱一起使用。 [區域終結點](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#is-there-a-list-of-regional-endpoints)不支援 AAD 身份驗證。

在前面的章節中，我們演示如何使用單一服務或多服務訂閱金鑰組 Azure 認知服務進行身份驗證。 雖然這些金鑰提供了快速簡便的開始開發路徑，但它們在需要基於角色的存取控制的更複雜的方案中不足。 讓我們來看看使用 Azure 活動目錄 （AAD） 進行身份驗證所需的內容。

在以下部分中，您將使用 Azure 雲外殼環境或 Azure CLI 創建子域、分配角色並獲取無記名權杖來調用 Azure 認知服務。 如果遇到問題，每個部分都會提供連結，並帶有 Azure 雲外殼/Azure CLI 中每個命令的所有可用選項。

### <a name="create-a-resource-with-a-custom-subdomain"></a>使用自訂子域創建資源

第一步是創建自訂子域。 如果要使用沒有自訂子功能變數名稱的現有認知服務資源，請按照[認知服務自訂子域](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#how-does-this-impact-existing-resources)中的說明為資源啟用自訂子域。

1. 首先打開 Azure 雲外殼。 然後[選擇訂閱](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext?view=azps-3.3.0)：

   ```powershell-interactive
   Set-AzContext -SubscriptionName <SubscriptionName>
   ```

2. 接下來，使用自訂子域[創建認知服務資源](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0)。 子功能變數名稱需要全域唯一，不能包括特殊字元，例如："."，"！"

   ```powershell-interactive
   New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. 如果成功，**終結點**應顯示資源獨有的子功能變數名稱。


### <a name="assign-a-role-to-a-service-principal"></a>將角色指派給服務主體

現在，您擁有了與資源關聯的自訂子域，您需要將角色指派給服務主體。

> [!NOTE]
> 請記住，AAD 角色指派可能需要長達五分鐘才能傳播。

1. 首先，讓我們註冊一個[AAD應用程式](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0)。

   ```powershell-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   在下一步中，您將需要**應用程式 Id。**

2. 接下來，您需要為 AAD 應用程式[創建服務主體](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0)。

   ```powershell-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > 如果在 Azure 門戶中註冊應用程式，則此步驟將完成。

3. 最後一步是將["認知服務使用者"角色指派給](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0)服務主體（範圍限定為資源）。 通過分配角色，您將授予對此資源的服務主體存取權限。 您可以授予對訂閱中的多個資源的相同服務主體存取權限。
   >[!NOTE]
   > 使用服務主體的 ObjectID，而不是應用程式的 ObjectId。
   > ACCOUNT_ID將是您創建的認知服務帳戶的 Azure 資源識別碼。 可以從 Azure 門戶中資源的"屬性"中找到 Azure 資源識別碼。

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>範例要求

在此示例中，密碼用於驗證服務主體。 然後，提供的權杖用於調用電腦視覺 API。

1. 獲取**租戶 Id**：
   ```powershell-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. 獲取權杖：
   ```powershell-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
3. 調用電腦視覺 API：
   ```powershell-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

或者，可以使用證書對服務主體進行身份驗證。 除了服務主體之外，使用者主體還通過通過另一個 AAD 應用程式委派許可權來提供支援。 在這種情況下，在獲取權杖時，將提示使用者進行雙重身份驗證，而不是密碼或證書。
