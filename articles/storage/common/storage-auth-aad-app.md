---
title: 從 Azure AD 獲取權杖，以授權來自用戶端應用程式的請求
titleSuffix: Azure Storage
description: 使用 Azure 活動目錄從用戶端應用程式中進行身份驗證、獲取 OAuth 2.0 權杖以及授權對 Azure Blob 存儲和佇列存儲的請求。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d3ee211298598d78f423d88fd4df1c58ed4bfa29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268479"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>從 Azure AD 獲取權杖，以授權來自用戶端應用程式的請求

將 Azure 活動目錄 （Azure AD） 與 Azure Blob 存儲或佇列存儲一起使用的一個關鍵優點是，您的憑據不再需要存儲在代碼中。 相反，可以從 Microsoft 標識平臺（以前的 Azure AD）請求 OAuth 2.0 訪問權杖。 Azure AD 對運行應用程式的安全主體（使用者、組或服務主體）進行身份驗證。 如果身份驗證成功，Azure AD 會將訪問權杖返回到應用程式，然後應用程式可以使用訪問權杖授權對 Azure Blob 存儲或佇列存儲的請求。

本文演示如何配置本機應用程式或 Web 應用程式以使用 Microsoft 標識平臺 2.0 進行身份驗證。 程式碼範例以 .NET 為主，但其他語言也是使用類似的方法。 有關 Microsoft 標識平臺 2.0 的詳細資訊，請參閱[Microsoft 標識平臺 （v2.0） 概述](../../active-directory/develop/v2-overview.md)。

如需 OAuth 2.0 程式碼授與流程的概觀，請參閱[使用 OAuth 2.0 授權碼授與流程，授權存取 Azure Active Directory Web 應用程式](../../active-directory/develop/v2-oauth2-auth-code-flow.md)。

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>將角色指派給 Azure AD 安全主體

若要從 Azure 儲存體應用程式中驗證安全性主體，必須先為該安全性主體設定角色型存取控制 (RBAC) 設定。 Azure 存儲定義包含容器和佇列許可權的內置 RBAC 角色。 當 RBAC 角色指派給安全性主體時，此安全性主體會獲得存取該資源的權限。 有關詳細資訊，請參閱使用[RBAC 管理對 Azure Blob 和佇列資料的存取權限](storage-auth-aad-rbac.md)。

## <a name="register-your-application-with-an-azure-ad-tenant"></a>向 Azure AD 租用戶註冊應用程式

使用 Azure AD 授權訪問存儲資源的第一步是從[Azure 門戶](https://portal.azure.com)向 Azure AD 租戶註冊用戶端應用程式。 註冊用戶端應用程式時，會向 Azure AD 提供有關應用程式的資訊。 Azure AD 接著會提供您在執行階段用來將應用程式與 Azure AD 產生關聯的用戶端識別碼 (也稱為「應用程式識別碼」**)。 若要深入了解用戶端識別碼，請參閱 [Azure Active Directory 中的應用程式和服務主體物件](../../active-directory/develop/app-objects-and-service-principals.md)。

要註冊 Azure 存儲應用程式，請按照["快速入門：向 Microsoft 標識平臺註冊應用程式](../../active-directory/develop/quickstart-configure-app-access-web-apis.md)"中的步驟操作。 下圖顯示了註冊 Web 應用程式的常見設置：

![演示如何向 Azure AD 註冊存儲應用程式的螢幕截圖](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> 如果將應用程式註冊為本機應用程式，則可以為重定向 URI 指定任何有效的**URI。** 對於本機應用程式，此值不必是真正的 URL。 對於 Web 應用程式，重定向 URI 必須是有效的 URI，因為它指定提供權杖的 URL。

註冊您的應用程式之後，您會在 [設定]**** 下看到應用程式識別碼 (或用戶端識別碼)︰

![顯示用戶端 ID 的螢幕截圖](./media/storage-auth-aad-app/app-registration-client-id.png)

如需有關向 Azure AD 註冊應用程式的詳細資訊，請參閱[整合應用程式與 Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md)。

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>將 Azure 儲存體的權限授與已註冊的應用程式

接下來，授予應用程式調用 Azure 存儲 API 的許可權。 此步驟使應用程式能夠授權使用 Azure AD 對 Azure 存儲的請求。

1. 在已註冊應用程式的 **"概述"** 頁上，選擇 **"查看 API 許可權**"。
1. 在**API 許可權**部分中，選擇 **"添加許可權"** 並選擇**Microsoft API**。
1. 從結果清單中選擇**Azure 存儲**以顯示 **"請求 API 許可權**"窗格。
1. 在**應用程式需要哪種類型的許可權下？，** 請注意，可用許可權類型是**委派許可權**。 預設情況下，將為您選擇此選項。
1. 在 **"請求 API 許可權**"窗格的 **"選擇許可權**"部分中，選擇**user_impersonation**旁邊的核取方塊，然後按一下"**添加許可權**"。

    ![顯示存儲許可權的螢幕截圖](media/storage-auth-aad-app/registered-app-permissions-1.png)

**API 許可權**窗格現在顯示已註冊的 Azure AD 應用程式有權訪問 Microsoft 圖形和 Azure 存儲。 首次向 Azure AD 註冊應用時，將自動授予 Microsoft 圖形許可權。

![顯示註冊應用許可權的螢幕截圖](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>建立用戶端密碼

應用程式在請求權杖時需要用戶端金鑰來證明其身份。 要添加用戶端金鑰，請按照以下步驟操作：

1. 導航到 Azure 門戶中的應用註冊。
1. 選擇 **"證書&機密**設置。
1. 在**用戶端機密**下，按一下 **"新用戶端"機密**以創建新機密。
1. 提供機密的說明，並選擇所需的過期間隔。
1. 立即將新機密的值複製到安全位置。 完整值僅顯示一次。

    ![顯示用戶端機密的螢幕截圖](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>用於權杖獲取的用戶端庫

註冊應用程式並授予應用程式訪問 Azure Blob 存儲或佇列存儲中的資料的許可權後，可以將代碼添加到應用程式以驗證安全主體並獲取 OAuth 2.0 權杖。 要驗證和獲取權杖，可以使用其中一個[Microsoft 標識平臺身份驗證庫](../../active-directory/develop/reference-v2-libraries.md)或支援 OpenID Connect 1.0 的另一個開源庫。 然後，應用程式可以使用訪問權杖授權針對 Azure Blob 存儲或佇列存儲的請求。

有關支援獲取權杖的方案清單，請參閱[Microsoft 身份驗證庫內容](/azure/active-directory/develop/msal-overview)的[身份驗證流](/en-us/azure/active-directory/develop/msal-authentication-flows)部分。

## <a name="well-known-values-for-authentication-with-azure-ad"></a>使用 Azure AD 進行驗證所需的已知值

若要使用 Azure AD 驗證安全性主體，您需要在程式碼中包含一些已知值。

### <a name="azure-ad-authority"></a>Azure AD 授權單位

針對 Microsoft 公用雲端，以下是基本 Azure AD 授權單位，其中 *tenant-id* 是 Active Directory 租用戶識別碼 (或目錄識別碼)：

`https://login.microsoftonline.com/<tenant-id>/`

租用戶識別碼會識別要用來驗證的 Azure AD 租用戶。 它也稱為目錄 ID。 若要檢索租戶 ID，請導航到 Azure 門戶中應用註冊的 **"概述"** 頁，然後從那裡複製該值。

### <a name="azure-storage-resource-id"></a>Azure 存儲資源識別碼

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET 程式碼範例：建立區塊 Blob

程式碼範例會示範如何從 Azure AD 取得存取權杖。 存取權杖會用來驗證指定的使用者，然後對建立區塊 Blob 的要求進行授權。 若要讓此範例運作，需先遵循前面幾節中所述的步驟。

要請求權杖，您需要從應用的註冊中提供以下值：

- Azure AD 域的名稱。 從 Azure 活動目錄的 **"概述"** 頁檢索此值。
- 租戶（或目錄）ID。 從應用註冊的 **"概述"** 頁面檢索此值。
- 用戶端（或應用程式）ID。 從應用註冊的 **"概述"** 頁面檢索此值。
- 用戶端重定向 URI。 從應用註冊的**身份驗證**設置中檢索此值。
- 用戶端機密的值。 從以前複製此值的位置檢索此值。

### <a name="create-a-storage-account-and-container"></a>建立儲存體帳戶和容器

要運行代碼示例，請在同一訂閱中創建與 Azure 活動目錄相同的存儲帳戶。 然後在該存儲帳戶中創建一個容器。 示例代碼將在此容器中創建塊 Blob。

接下來，顯式將存儲**Blob 資料參與者**角色指派給將在該使用者帳戶上運行示例代碼的使用者帳戶。 有關如何在 Azure 門戶中分配此角色的說明，請參閱在[Azure 門戶中使用 RBAC 授予對 Azure Blob 和佇列資料的存取權限](storage-auth-aad-rbac-portal.md)。

> [!NOTE]
> 創建 Azure 存儲帳戶時，不會自動分配通過 Azure AD 訪問資料的許可權。 您必須明確地將 Azure 儲存體的 RBAC 角色指派給自己。 您可以在訂用帳戶、資源群組、儲存體帳戶或容器/佇列層級上指派此角色。

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>創建授權使用 Azure AD 訪問 Blob 存儲的 Web 應用程式

當應用程式訪問 Azure 存儲時，它代表使用者這樣做，這意味著使用登錄使用者的許可權訪問 Blob 或佇列資源。 要嘗試此代碼示例，需要一個 Web 應用程式，提示使用者使用 Azure AD 標識登錄。 您可以創建自己的應用程式，或使用 Microsoft 提供的應用程式範例。

[GitHub](https://aka.ms/aadstorage)上提供了獲取權杖並使用它在 Azure 存儲中創建 blob 的已完成示例 Web 應用程式。 查看並運行已完成的示例可能有助於瞭解代碼示例。 有關如何運行已完成示例的說明，請參閱標題為["查看"並運行已完成的示例](#view-and-run-the-completed-sample)的部分。

#### <a name="add-references-and-using-statements"></a>新增參考並使用陳述式  

從視覺化工作室安裝 Azure 存儲用戶端庫。 從 **"工具"** 功能表中，選擇**NuGet 包管理器**，然後**選擇包管理器主控台**。 在主控台視窗中鍵入以下命令，以便從 Azure 存儲用戶端庫中為 .NET 安裝必要的包：

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

接下來，將以下使用語句添加到HomeController.cs檔中：

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>建立區塊 Blob

添加以下程式碼片段以創建塊 Blob：

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with your storage account URL
    CloudBlockBlob blob =
        new CloudBlockBlob(
            new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
            storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

> [!NOTE]
> 要使用 OAuth 2.0 權杖授權 Blob 和佇列操作，必須使用 HTTPS。

在上面的範例中，.NET 用戶端程式庫會處理要求的授權以建立區塊 Blob。 其他語言的 Azure 存儲用戶端庫也處理請求的授權。 不過，若您透過 OAuth 權杖使用 REST API 呼叫 Azure 儲存體作業，則您將必須使用 OAuth 權杖授權該要求。

若要使用 OAuth 存取權杖呼叫 Blob 與佇列服務作業，請使用 **Bearer** 結構描述在 **Authorization** 標頭中傳遞存取權杖，並指定 2017-11-09 或更高的服務版本，如下列範例所示：

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>從 Azure AD 取得 OAuth 權杖

接下來，添加一個代表使用者從 Azure AD 請求權杖的方法。 此方法定義要授予許可權的範圍。 有關許可權和作用域的詳細資訊，請參閱 Microsoft[標識平臺終結點中的許可權和同意](../../active-directory/develop/v2-permissions-and-consent.md)。

使用資源識別碼 構造要為其獲取權杖的範圍。 該示例通過使用資源識別碼 和內置`user_impersonation`作用域來構造作用域，該作用域指示正在代表使用者請求權杖。

請記住，您可能需要向使用者提供一個介面，使使用者能夠同意代表他們請求權杖。 當需要同意時，該示例捕獲**MsalUi需求異常**並調用另一種方法以方便請求同意：

```csharp
public async Task<IActionResult> Blob()
{
    var scopes = new string[] { "https://storage.azure.com/user_impersonation" };
    try
    {
        var accessToken =
            await _tokenAcquisition.GetAccessTokenOnBehalfOfUser(HttpContext, scopes);
        ViewData["Message"] = await CreateBlob(accessToken);
        return View();
    }
    catch (MsalUiRequiredException ex)
    {
        AuthenticationProperties properties =
            BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

同意是使用者授權應用程式代表使用者存取受保護的資源所用的程序。 Microsoft 標識平臺 2.0 支援增量同意，這意味著安全主體最初可以請求一組最小許可權，並根據需要根據需要添加許可權。 當代碼請求訪問權杖時，請在`scope`參數中指定應用在任意給定時間所需的許可權範圍。 有關增量同意的詳細資訊，請參閱"[為什麼更新到 Microsoft 標識平臺 （v2.0）"](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)中的"**增量和動態同意**"部分。

以下方法構造請求增量同意的身份驗證屬性：

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes,
                                                                                    MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET or MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope,
                                                 scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented
    // with an account selection dialog.
    string loginHint = HttpContext.User.GetLoginHint();
    if (!string.IsNullOrWhiteSpace(loginHint))
    {
        properties.SetParameter<string>(OpenIdConnectParameterNames.LoginHint, loginHint);

        string domainHint = HttpContext.User.GetDomainHint();
        properties.SetParameter<string>(OpenIdConnectParameterNames.DomainHint, domainHint);
    }

    // Specify any additional claims that are required (for instance, MFA).
    if (!string.IsNullOrEmpty(ex.Claims))
    {
        properties.Items.Add("claims", ex.Claims);
    }

    return properties;
}
```

## <a name="view-and-run-the-completed-sample"></a>查看並運行已完成的示例

要運行應用程式範例，請先克隆或從[GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal)下載它。 然後按照以下各節所述更新應用程式。

### <a name="provide-values-in-the-settings-file"></a>在設置檔中提供值

接下來，使用您自己的值更新*appsettings.json*檔，如下所示：

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    // To call an API
    "ClientSecret": "<client-secret>"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>更新存儲帳戶和容器名稱

在*HomeController.cs*檔中，更新引用塊 Blob 的 URI 以使用存儲帳戶和容器的名稱：

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>啟用隱式授予流

要運行該示例，您可能需要為應用註冊配置隱式授予流。 請遵循下列步驟：

1. 導航到 Azure 門戶中的應用註冊。
1. 在"管理"部分中，選擇 **"身份驗證**"設置。
1. 在 **"高級設置**"中，在 **"隱式授予**"部分中，選擇核取方塊以啟用訪問權杖和 ID 權杖，如下圖所示：

    ![顯示如何啟用隱式授予流的設置的螢幕截圖](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>更新本地主機使用的埠

運行示例時，您可能會發現需要更新應用註冊中指定的重定向 URI，才能使用運行時分配的*本地主機*埠。 要更新重定向 URI 以使用分配的埠，請按照以下步驟操作：

1. 導航到 Azure 門戶中的應用註冊。
1. 在"管理"部分中，選擇 **"身份驗證**"設置。
1. 在 **"重定向 URI"** 下，編輯埠以匹配應用程式範例使用埠，如下圖所示：

    ![顯示應用註冊重定向 URI 的螢幕截圖](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>後續步驟

- 要瞭解有關微軟標識平臺的更多資訊，請參閱[Microsoft 標識平臺](https://docs.microsoft.com/azure/active-directory/develop/)。
- 要瞭解有關 Azure 存儲的 RBAC 角色，請參閱[使用 RBAC 管理存儲資料的存取權限](storage-auth-aad-rbac.md)。
- 要瞭解如何使用 Azure 存儲的 Azure 資源進行託管標識，請參閱[使用 Azure 活動目錄和 Azure 資源的託管標識對 Blob 和佇列進行身份驗證訪問](storage-auth-aad-msi.md)。
