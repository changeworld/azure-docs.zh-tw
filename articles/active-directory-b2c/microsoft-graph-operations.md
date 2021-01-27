---
title: 使用 Microsoft Graph 管理資源
titleSuffix: Azure AD B2C
description: 如何藉由呼叫 Microsoft Graph API，並使用應用程式識別來將程式自動化，以管理 Azure AD B2C 租使用者中的資源。
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 96772020e70aeb32fa1a8ae18bf3818396887877
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98805245"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>使用 Microsoft Graph 管理 Azure AD B2C

Microsoft Graph 可讓您管理 Azure AD B2C 目錄中的資源。 下列 Microsoft Graph API 作業支援管理 Azure AD B2C 資源，包括使用者、身分識別提供者、使用者流程、自訂原則和原則金鑰。 下列各節中的每個連結都會以該作業的 Microsoft Graph API 參考中的對應頁面為目標。 

## <a name="prerequisites"></a>先決條件

若要使用 MS 圖形 API，並與您 Azure AD B2C 租使用者中的資源互動，您需要可授與許可權以執行這項操作的應用程式註冊。 遵循《 [管理 Azure AD B2C 與 Microsoft Graph](microsoft-graph-get-started.md) 文章中的步驟，建立管理應用程式可使用的應用程式註冊。 

## <a name="user-management"></a>使用者管理

- [列出使用者](/graph/api/user-list)
- [建立取用者使用者](/graph/api/user-post-users)
- [取得使用者](/graph/api/user-get)
- [更新使用者](/graph/api/user-update)
- [刪除使用者](/graph/api/user-delete)

## <a name="user-phone-number-management"></a>使用者電話號碼管理

- [加入](/graph/api/authentication-post-phonemethods)
- [獲取](/graph/api/b2cauthenticationmethodspolicy-get)
- [更新](/graph/api/b2cauthenticationmethodspolicy-update)
- [刪除](/graph/api/phoneauthenticationmethod-delete)

如需管理使用者登入電話號碼的詳細資訊，請參閱 [B2C 驗證方法](/graph/api/resources/b2cauthenticationmethodspolicy)。

## <a name="identity-providers-user-flow"></a>身分識別提供者 (使用者流程) 

管理您的 Azure AD B2C 租使用者中，可供您的使用者流程使用的身分識別提供者。

- [列出在 Azure AD B2C 租使用者中註冊的身分識別提供者](/graph/api/identityprovider-list)
- [建立身分識別提供者](/graph/api/identityprovider-post-identityproviders)
- [取得身分識別提供者](/graph/api/identityprovider-get)
- [更新識別提供者](/graph/api/identityprovider-update)
- [刪除身分識別提供者](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>使用者流程

設定註冊、登入、合併註冊與登入、密碼重設和設定檔更新的預先建立原則。

- [列出使用者流程](/graph/api/identitycontainer-list-b2cuserflows)
- [建立使用者流程](/graph/api/identitycontainer-post-b2cuserflows)
- [取得使用者流程](/graph/api/b2cidentityuserflow-get)
- [刪除使用者流程](/graph/api/b2cidentityuserflow-delete)

## <a name="custom-policies"></a>自訂原則

下列作業可讓您管理 Azure AD B2C 信任架構原則，稱為 [自訂原則](custom-policy-overview.md)。

- [列出租使用者中設定的所有信任架構原則](/graph/api/trustframework-list-trustframeworkpolicies)
- [建立信任架構原則](/graph/api/trustframework-post-trustframeworkpolicy)
- [讀取現有信任架構原則的屬性](/graph/api/trustframeworkpolicy-get)
- [更新或建立信任架構原則。](/graph/api/trustframework-put-trustframeworkpolicy)
- [刪除現有的信任架構原則](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>原則金鑰

Identity Experience Framework 會儲存自訂原則中所參考的秘密，以在元件之間建立信任。 這些秘密可以是對稱或非對稱金鑰/值。 在 Azure 入口網站中，這些實體會顯示為 **原則金鑰**。

Microsoft Graph API 中原則金鑰的最上層資源是 [受信任的架構索引鍵集](/graph/api/resources/trustframeworkkeyset)。 每個索引 **鍵集** 至少包含一個 **金鑰**。 若要建立金鑰，請先建立空的索引鍵集，然後在索引鍵集中產生金鑰。 您可以建立手動密碼、上傳憑證或 PKCS12 金鑰。 金鑰可以是產生的密碼、字串 (例如 Facebook 應用程式密碼) 或您上傳的憑證。 如果索引鍵集有多個索引鍵，則只會使用其中一個索引鍵。

### <a name="trust-framework-policy-keyset"></a>信任架構原則索引鍵集

- [列出信任架構索引鍵集](/graph/api/trustframework-list-keysets)
- [建立信任架構索引鍵集](/graph/api/trustframework-post-keysets)
- [取得索引鍵集](/graph/api/trustframeworkkeyset-get)
- [更新信任架構索引鍵集](/graph/api/trustframeworkkeyset-update)
- [刪除信任架構索引鍵集](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>信任架構原則金鑰

- [取得索引鍵集中目前的使用中金鑰](/graph/api/trustframeworkkeyset-getactivekey)
- [在索引鍵集中產生金鑰](/graph/api/trustframeworkkeyset-generatekey)
- [上傳以字串為基礎的秘密](/graph/api/trustframeworkkeyset-uploadsecret)
- [上傳 x.509 憑證](/graph/api/trustframeworkkeyset-uploadcertificate)
- [上傳 PKCS12 格式憑證](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>應用程式

- [列出應用程式](/graph/api/application-list)
- [建立應用程式](/graph/api/resources/application)
- [更新應用程式](/graph/api/application-update) \(英文\)
- [建立 servicePrincipal](/graph/api/resources/serviceprincipal)
- [建立 oauth2Permission 授與](/graph/api/resources/oauth2permissiongrant)
- [刪除應用程式](/graph/api/application-delete)

## <a name="application-extension-properties"></a>應用程式延伸模組屬性

- [清單延伸模組屬性](/graph/api/application-list-extensionproperty)

Azure AD B2C 提供一個目錄，可為每個使用者保存 100 個自訂屬性。 針對使用者流程，這些擴充屬性是 [使用 Azure 入口網站來管理](user-flow-custom-attributes.md)。 在自訂原則中，Azure AD B2C 會在您第一次將值寫入擴充屬性時為您建立屬性。

## <a name="audit-logs"></a>稽核記錄

- [列出審核記錄](/graph/api/directoryaudit-list)

如需存取 Azure AD B2C audit 記錄檔的詳細資訊，請參閱 [存取 Azure AD B2C audit 記錄](view-audit-logs.md)檔。

## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>程式碼範例：如何以程式設計方式管理使用者帳戶

這個程式碼範例是一個 .NET Core 主控台應用程式，它會使用 [MICROSOFT GRAPH SDK](/graph/sdks/sdks-overview) 來與 Microsoft Graph API 互動。 其程式碼示範如何呼叫 API，以程式設計方式管理 Azure AD B2C 租使用者中的使用者。
您可以 [下載範例](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) 封存 ( * .zip) 、流覽 GitHub 上 [的存放庫](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) ，或複製存放庫：

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

取得程式碼範例之後，請針對您的環境進行設定，然後建立專案：

1. 在 [Visual Studio](https://visualstudio.microsoft.com) 中開啟專案或 [Visual Studio Code](https://code.visualstudio.com)。
1. 開啟 `src/appsettings.json`。
1. 在 `appSettings` 區段中，以 `your-b2c-tenant` 您的租使用者名稱取代，並 `Application (client) ID` 以 `Client secret` 管理應用程式註冊的值取代。 如需詳細資訊，請參閱 [註冊 Microsoft Graph 應用程式](microsoft-graph-get-started.md)。
1. 在存放庫的本機複本中開啟主控台視窗，切換至 `src` 目錄，然後建立專案：

    ```console
    cd src
    dotnet build
    ```
    
1. 使用 `dotnet` 命令執行應用程式：

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

應用程式會顯示您可以執行的命令清單。 例如，取得所有使用者、取得單一使用者、刪除使用者、更新使用者的密碼，以及大量匯入。

### <a name="code-discussion"></a>程式碼討論

範例程式碼會使用 [MICROSOFT GRAPH SDK](/graph/sdks/sdks-overview)，其設計目的是要簡化可存取 Microsoft Graph 的高品質、有效率且可復原的應用程式。

Microsoft Graph API 的任何要求都需要存取權杖以進行驗證。 解決方案會使用 [Microsoft](https://www.nuget.org/packages/Microsoft.Graph.Auth/) 驗證程式庫 (MSAL) 提供的驗證案例型包裝函式，以搭配 Microsoft Graph SDK 使用。

`RunAsync` _Program.cs_ 檔中的方法：

1. 從檔案 _appsettings.js_ 讀取應用程式設定
1. 使用 [OAuth 2.0 用戶端認證授](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) 與流程來初始化 auth provider。 在用戶端認證授與流程中，應用程式可以取得存取權杖來呼叫 Microsoft Graph API。
1. 使用驗證提供者設定 Microsoft Graph 服務用戶端：

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

然後，在 _UserService.cs_ 中使用初始化的 *GraphServiceClient* 來執行使用者管理作業。 例如，取得租使用者中的使用者帳戶清單：

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

[使用 Microsoft Graph Sdk 進行 API 呼叫的](/graph/sdks/create-requests) 資訊包括如何從 Microsoft Graph 讀取和寫入資訊、使用 `$select` 控制傳回的屬性、提供自訂查詢參數，以及使用 `$filter` 和 `$orderBy` 查詢參數。

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
