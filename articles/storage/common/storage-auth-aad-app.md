---
title: 從 Azure AD 取得權杖，以從用戶端應用程式授權要求
titleSuffix: Azure Storage
description: 使用 Azure Active Directory 從用戶端應用程式內進行驗證、取得 OAuth 2.0 權杖，以及授權 Azure Blob 儲存體和佇列儲存體的要求。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d3ee211298598d78f423d88fd4df1c58ed4bfa29
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268479"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>從 Azure AD 取得權杖，以從用戶端應用程式授權要求

使用 Azure Active Directory （Azure AD）搭配 Azure Blob 儲存體或佇列儲存體的主要優點，就是您的認證不再需要儲存在您的程式碼中。 相反地，您可以從 Microsoft 身分識別平臺（先前稱為 Azure AD）要求 OAuth 2.0 存取權杖。 Azure AD 會驗證執行應用程式的安全性主體（使用者、群組或服務主體）。 如果驗證成功，Azure AD 會將存取權杖傳回給應用程式，然後應用程式就可以使用存取權杖來授權對 Azure Blob 儲存體或佇列儲存體的要求。

本文說明如何設定原生應用程式或 web 應用程式，以使用 Microsoft 身分識別平臺2.0 進行驗證。 程式碼範例以 .NET 為主，但其他語言也是使用類似的方法。 如需 Microsoft 身分識別平臺2.0 的詳細資訊，請參閱[microsoft 身分識別平臺（v2.0）總覽](../../active-directory/develop/v2-overview.md)。

如需 OAuth 2.0 程式碼授與流程的概觀，請參閱[使用 OAuth 2.0 授權碼授與流程，授權存取 Azure Active Directory Web 應用程式](../../active-directory/develop/v2-oauth2-auth-code-flow.md)。

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>將角色指派給 Azure AD 的安全性主體

若要從 Azure 儲存體應用程式中驗證安全性主體，必須先為該安全性主體設定角色型存取控制 (RBAC) 設定。 Azure 儲存體會定義內建的 RBAC 角色，其中包含容器和佇列的許可權。 當 RBAC 角色指派給安全性主體時，此安全性主體會獲得存取該資源的權限。 如需詳細資訊，請參閱[使用 RBAC 來管理 Azure Blob 和佇列資料的存取權限](storage-auth-aad-rbac.md)。

## <a name="register-your-application-with-an-azure-ad-tenant"></a>向 Azure AD 租用戶註冊應用程式

使用 Azure AD 來授與儲存體資源存取權的第一個步驟，是向[Azure 入口網站](https://portal.azure.com)向 Azure AD 租使用者註冊您的用戶端應用程式。 當您註冊用戶端應用程式時，您會提供應用程式的相關資訊以 Azure AD。 Azure AD 接著會提供您在執行階段用來將應用程式與 Azure AD 產生關聯的用戶端識別碼 (也稱為「應用程式識別碼」)。 若要深入了解用戶端識別碼，請參閱 [Azure Active Directory 中的應用程式和服務主體物件](../../active-directory/develop/app-objects-and-service-principals.md)。

若要註冊您的 Azure 儲存體應用程式，請遵循 [快速入門中所示的步驟：使用 Microsoft 身分識別平臺來註冊應用程式](../../active-directory/develop/quickstart-configure-app-access-web-apis.md)。 下圖顯示註冊 web 應用程式的一般設定：

![顯示如何向 Azure AD 註冊儲存體應用程式的螢幕擷取畫面](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> 如果您將應用程式註冊為原生應用程式，就能為**重新導向 URI** 指定任何有效的 URI。 對於原生應用程式，這個值不一定是真正的 URL。 對於 web 應用程式，重新導向 URI 必須是有效的 URI，因為它會指定提供權杖的 URL。

註冊您的應用程式之後，您會在 [設定] 下看到應用程式識別碼 (或用戶端識別碼)︰

![顯示用戶端識別碼的螢幕擷取畫面](./media/storage-auth-aad-app/app-registration-client-id.png)

如需有關向 Azure AD 註冊應用程式的詳細資訊，請參閱[整合應用程式與 Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md)。

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>將 Azure 儲存體的權限授與已註冊的應用程式

接下來，授與您的應用程式許可權來呼叫 Azure 儲存體 Api。 此步驟可讓您的應用程式授權 Azure AD Azure 儲存體的要求。

1. 在您已註冊應用程式的 [**總覽**] 頁面上，選取 [ **View API 許可權**]。
1. 在 [ **API 許可權**] 區段中，選取 [**新增許可權**]，然後選擇 [ **Microsoft api**]。
1. 從結果清單中選取 [ **Azure 儲存體**]，以顯示 [**要求 API 許可權**] 窗格。
1. 在 [**您的應用程式需要何種類型的許可權？** ] 底下，觀察可用的許可權類型是否為**委派的許可權**。 預設會為您選取此選項。
1. 在 [**要求 API 許可權**] 窗格的 [**選取許可權**] 區段中，選取 [ **user_impersonation**] 旁的核取方塊，然後按一下 [**新增許可權**]。

    ![顯示儲存體許可權的螢幕擷取畫面](media/storage-auth-aad-app/registered-app-permissions-1.png)

[ **API 許可權**] 窗格現在會顯示您已註冊的 Azure AD 應用程式可以存取 Microsoft Graph 和 Azure 儲存體。 當您第一次向 Azure AD 註冊應用程式時，系統會將許可權授與 Microsoft Graph。

![顯示註冊應用程式許可權的螢幕擷取畫面](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>建立用戶端密碼

應用程式需要用戶端密碼，才能在要求權杖時證明其身分識別。 若要新增用戶端密碼，請遵循下列步驟：

1. 在 Azure 入口網站中，流覽至您的應用程式註冊。
1. 選取 [**憑證 & 密碼**] 設定。
1. 在 [**用戶端密碼**] 底下，按一下 [**新增用戶端密碼**] 以建立新的密碼。
1. 提供密碼的描述，然後選擇想要的到期間隔。
1. 立即將新密碼的值複製到安全的位置。 [完整] 值只會顯示一次。

    ![顯示用戶端密碼的螢幕擷取畫面](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>取得權杖的用戶端程式庫

在您註冊應用程式並授與它存取 Azure Blob 儲存體或佇列儲存體中資料的許可權之後，您就可以將程式碼新增至應用程式，以驗證安全性主體並取得 OAuth 2.0 權杖。 若要驗證並取得權杖，您可以使用其中一個 Microsoft 身分[識別平臺驗證程式庫](../../active-directory/develop/reference-v2-libraries.md)，或另一個支援 OpenID connect 1.0 的開放原始碼程式庫。 然後，您的應用程式就可以使用存取權杖來授權對 Azure Blob 儲存體或佇列儲存體的要求。

如需支援取得權杖的案例清單，請參閱[Microsoft 驗證程式庫內容](/azure/active-directory/develop/msal-overview)的[驗證流程](/en-us/azure/active-directory/develop/msal-authentication-flows)一節。

## <a name="well-known-values-for-authentication-with-azure-ad"></a>使用 Azure AD 進行驗證所需的已知值

若要使用 Azure AD 驗證安全性主體，您需要在程式碼中包含一些已知值。

### <a name="azure-ad-authority"></a>Azure AD 授權單位

針對 Microsoft 公用雲端，以下是基本 Azure AD 授權單位，其中 *tenant-id* 是 Active Directory 租用戶識別碼 (或目錄識別碼)：

`https://login.microsoftonline.com/<tenant-id>/`

租用戶識別碼會識別要用來驗證的 Azure AD 租用戶。 它也稱為目錄識別碼。 若要取得租使用者識別碼，請在 Azure 入口網站中流覽至應用程式註冊的 [**總覽**] 頁面，並從該處複製值。

### <a name="azure-storage-resource-id"></a>Azure 儲存體資源識別碼

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET 程式碼範例：建立區塊 Blob

程式碼範例會示範如何從 Azure AD 取得存取權杖。 存取權杖會用來驗證指定的使用者，然後對建立區塊 Blob 的要求進行授權。 若要讓此範例運作，需先遵循前面幾節中所述的步驟。

若要要求權杖，您將需要應用程式註冊中的下列值：

- Azure AD 網域的名稱。 從您 Azure Active Directory 的 [**總覽**] 頁面取出此值。
- 租使用者（或目錄）識別碼。 從應用程式註冊的 [**總覽**] 頁面取出此值。
- 用戶端（或應用程式）識別碼。 從應用程式註冊的 [**總覽**] 頁面取出此值。
- 用戶端重新導向 URI。 從您的應用程式註冊的**驗證**設定中抓取此值。
- 用戶端密碼的值。 從您先前複製的位置抓取此值。

### <a name="create-a-storage-account-and-container"></a>建立儲存體帳戶和容器

若要執行程式碼範例，請在與您 Azure Active Directory 相同的訂用帳戶中建立儲存體帳戶。 然後在該儲存體帳戶內建立容器。 範例程式碼會在此容器中建立區塊 blob。

接下來，將**儲存體 Blob 資料參與者**角色明確指派給您將用來執行範例程式碼的使用者帳戶。 如需如何在 Azure 入口網站中指派此角色的指示，請參閱[在 Azure 入口網站中使用 RBAC 授與 Azure blob 和佇列資料的存取權](storage-auth-aad-rbac-portal.md)。

> [!NOTE]
> 當您建立 Azure 儲存體帳戶時，不會自動指派許可權以透過 Azure AD 存取資料。 您必須明確地將 Azure 儲存體的 RBAC 角色指派給自己。 您可以在訂用帳戶、資源群組、儲存體帳戶或容器/佇列層級上指派此角色。

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>建立 web 應用程式，以使用 Azure AD 授權存取 Blob 儲存體

當您的應用程式存取 Azure 儲存體時，會代表使用者執行此動作，這表示會使用登入之使用者的許可權來存取 blob 或佇列資源。 若要嘗試此程式碼範例，您需要 web 應用程式，以提示使用者使用 Azure AD 身分識別登入。 您可以建立自己的，或使用 Microsoft 提供的範例應用程式。

取得權杖並使用它在 Azure 儲存體中建立 blob 的完整範例 web 應用程式，可在[GitHub](https://aka.ms/aadstorage)上取得。 查看和執行已完成的範例可能有助於瞭解程式碼範例。 如需有關如何執行完成範例的指示，請參閱標題為[View 並執行已完成的範例](#view-and-run-the-completed-sample)一節。

#### <a name="add-references-and-using-statements"></a>新增參考並使用陳述式  

從 Visual Studio 安裝 Azure 儲存體用戶端程式庫。 在 [工具] 功能表中，依序選取 [NuGet 套件管理員] 及 [套件管理員主控台]。 在主控台視窗中輸入下列命令，從適用于 .NET 的 Azure 儲存體用戶端程式庫安裝必要的套件：

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

接下來，將下列 using 語句新增至 HomeController.cs 檔案：

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>建立區塊 Blob

新增下列程式碼片段以建立區塊 blob：

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
> 若要使用 OAuth 2.0 權杖來授權 blob 和佇列作業，您必須使用 HTTPS。

在上面的範例中，.NET 用戶端程式庫會處理要求的授權以建立區塊 Blob。 適用于其他語言 Azure 儲存體用戶端程式庫也會為您處理要求的授權。 不過，若您透過 OAuth 權杖使用 REST API 呼叫 Azure 儲存體作業，則您將必須使用 OAuth 權杖授權該要求。

若要使用 OAuth 存取權杖呼叫 Blob 與佇列服務作業，請使用 **Bearer** 結構描述在 **Authorization** 標頭中傳遞存取權杖，並指定 2017-11-09 或更高的服務版本，如下列範例所示：

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>從 Azure AD 取得 OAuth 權杖

接下來，新增方法，以向 Azure AD 代表使用者要求權杖。 這個方法會定義要授與許可權的範圍。 如需有關許可權和範圍的詳細資訊，請參閱[Microsoft 身分識別平臺端點中的許可權和同意](../../active-directory/develop/v2-permissions-and-consent.md)。

使用資源識別碼來建立要取得權杖的範圍。 此範例會使用資源識別碼搭配內建的 `user_impersonation` 範圍來建立範圍，這表示會代表使用者要求權杖。

請記住，您可能需要向使用者呈現介面，讓使用者同意代表其要求權杖。 當需要同意時，此範例會攔截**MsalUiRequiredException**並呼叫另一個方法，以協助要求同意：

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

同意是使用者授權應用程式代表使用者存取受保護的資源所用的程序。 Microsoft 身分識別平臺2.0 支援累加式同意，這表示安全性主體一開始可以要求一組最小許可權，並視需要新增許可權一段時間。 當您的程式碼要求存取權杖時，請在 `scope` 參數中指定您的應用程式在任何指定時間所需的許可權範圍。 如需有關增量同意的詳細資訊，請參閱 [為何要更新 Microsoft 身分識別平臺（v2.0）](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)中的「**增量和動態同意**」一節。

下列方法會針對要求增量同意來建立驗證屬性：

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

## <a name="view-and-run-the-completed-sample"></a>查看並執行已完成的範例

若要執行範例應用程式，請先從[GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal)進行複製或下載。 然後更新應用程式，如下列各節所述。

### <a name="provide-values-in-the-settings-file"></a>在設定檔案中提供值

接下來，使用您自己的值來更新*appsettings* ，如下所示：

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

### <a name="update-the-storage-account-and-container-name"></a>更新儲存體帳戶和容器名稱

在*HomeController.cs*檔案中，更新參考區塊 BLOB 的 URI，以使用您的儲存體帳戶和容器的名稱：

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>啟用隱含授與流程

若要執行範例，您可能需要設定應用程式註冊的隱含授與流程。 請遵循下列步驟：

1. 在 Azure 入口網站中，流覽至您的應用程式註冊。
1. 在 [管理] 區段中，選取 [**驗證**] 設定。
1. 在 [ **Advanced settings**] 底下的 [**隱含授**與] 區段中，選取核取方塊以啟用存取權杖和識別碼權杖，如下圖所示：

    ![顯示如何啟用隱含授與流程設定的螢幕擷取畫面](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>更新 localhost 所使用的埠

當您執行範例時，您可能會發現需要更新應用程式註冊中指定的重新導向 URI，才能使用在執行時間指派的*localhost*埠。 若要將重新導向 URI 更新為使用指派的埠，請遵循下列步驟：

1. 在 Azure 入口網站中，流覽至您的應用程式註冊。
1. 在 [管理] 區段中，選取 [**驗證**] 設定。
1. 在 [重新**導向 uri**] 底下，編輯埠以符合範例應用程式所使用的埠，如下圖所示：

    ![螢幕擷取畫面，顯示應用程式註冊的重新導向 Uri](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>後續步驟

- 若要深入瞭解 Microsoft 身分識別平臺，請參閱[microsoft 身分識別平臺](https://docs.microsoft.com/azure/active-directory/develop/)。
- 若要深入瞭解 Azure 儲存體的 RBAC 角色，請參閱[使用 Rbac 管理儲存體資料的存取權限](storage-auth-aad-rbac.md)。
- 若要瞭解如何使用適用于 Azure 資源的受控識別搭配 Azure 儲存體，請參閱[使用 Azure 資源的 Azure Active Directory 和受控識別來驗證 blob 和佇列的存取權](storage-auth-aad-msi.md)。
