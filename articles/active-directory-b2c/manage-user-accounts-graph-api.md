---
title: 使用 Microsoft Graph API 來管理使用者
titleSuffix: Azure AD B2C
description: 如何藉由呼叫 Microsoft Graph API 並使用應用程式身分識別，來管理 Azure AD B2C 租使用者中的使用者，以自動化進程。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 54b4292c74f7737f1c392d601627eb3e0ff48812
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87116201"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>使用 Microsoft Graph 管理 Azure AD B2C 的使用者帳戶

Microsoft Graph 可讓您在 Microsoft Graph API 中提供 create、read、update 和 delete 方法，以管理 Azure AD B2C 目錄中的使用者帳戶。 您可以藉由呼叫 Microsoft Graph API，將現有的使用者存放區遷移至 Azure AD B2C 租使用者，並執行其他使用者帳戶管理作業。

在接下來的章節中，會顯示使用 Microsoft Graph API Azure AD B2C 使用者管理的主要層面。 此處顯示的 Microsoft Graph API 作業、類型和屬性，都是出現在 Microsoft Graph API 參考檔中的子集。

## <a name="register-a-management-application"></a>註冊管理應用程式

在您撰寫的任何使用者管理應用程式或腳本可以與您 Azure AD B2C 租使用者中的資源互動之前，您需要一個應用程式註冊，以授與這些許可權。

請遵循此操作說明文章中的步驟，建立您的管理應用程式可使用的應用程式註冊：

[使用 Microsoft Graph 管理 Azure AD B2C](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>使用者管理 Microsoft Graph 作業

[MICROSOFT GRAPH API](https://docs.microsoft.com/graph/api/resources/user)中提供下列使用者管理作業：

- [取得使用者清單](https://docs.microsoft.com/graph/api/user-list)
- [建立使用者](https://docs.microsoft.com/graph/api/user-post-users)
- [取得使用者](https://docs.microsoft.com/graph/api/user-get)
- [更新使用者](https://docs.microsoft.com/graph/api/user-update)
- [刪除使用者](https://docs.microsoft.com/graph/api/user-delete)

## <a name="user-properties"></a>使用者屬性

### <a name="display-name-property"></a>顯示名稱屬性

`displayName`是要在使用者的 Azure 入口網站使用者管理中顯示的名稱，而在存取權杖中 Azure AD B2C 會回到應用程式。 這是必要屬性。

### <a name="identities-property"></a>識別屬性

客戶帳戶（可能是取用者、合作夥伴或公民）可以與下列身分識別類型相關聯：

- **本機**身分識別-使用者名稱和密碼會儲存在本機的 Azure AD B2C 目錄中。 我們通常會將這些身分識別稱為「本機帳戶」。
- 同盟身分識別-也稱為*社交*或*企業*帳戶，使用者的**身分識別是**由 Facebook、Microsoft、ADFS 或 Salesforce 等同盟身分識別提供者所管理。

具有客戶帳戶的使用者可以使用多個身分識別登入。 例如，使用者名稱、電子郵件、員工識別碼、政府識別碼等等。 單一帳戶可以有多個具有相同密碼的本機和社交身分識別。

在 Microsoft Graph API 中，本機和同盟身分識別都會儲存在 user `identities` 屬性中，這是[objectIdentity][graph-objectIdentity]類型。 `identities`集合代表一組用來登入使用者帳戶的身分識別。 此集合可讓使用者使用其任何相關聯的身分識別登入使用者帳戶。

| 屬性   | 類型 |說明|
|:---------------|:--------|:----------|
|signInType|字串| 在您的目錄中指定使用者登入類型。 針對本機帳戶： `emailAddress` 、 `emailAddress1` 、 `emailAddress2` 、 `emailAddress3` 、 `userName` 或您喜歡的任何其他類型。 社交帳戶必須設定為 `federated` 。|
|簽發者|字串|指定身分識別的簽發者。 若為本機帳戶（其中**signInType**不是 `federated` ），這個屬性就是本機 B2C 租使用者的預設功能變數名稱，例如 `contoso.onmicrosoft.com` 。 針對社交身分識別（其中**signInType**為 `federated` ），值為簽發者的名稱，例如`facebook.com`|
|issuerAssignedId|字串|指定簽發者指派給使用者的唯一識別碼。 **簽發者**和**issuerAssignedId**的組合在您的租使用者中必須是唯一的。 若為本機帳戶，當**signInType**設定為 `emailAddress` 或時 `userName` ，它代表使用者的登入名稱。<br>當**signInType**設定為時： <ul><li>`emailAddress`（或開頭為 `emailAddress` like `emailAddress1` ） **issuerAssignedId**必須是有效的電子郵件地址</li><li>`userName`（或任何其他值）， **issuerAssignedId**必須是[電子郵件地址的有效本機部分](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`， **issuerAssignedId**代表同盟帳戶的唯一識別碼</li></ul>|

**下列 identity**屬性，具有具有登入名稱的本機帳戶身分識別、電子郵件地址，以及使用社交身分識別。 

 ```json
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

針對同盟身分識別，根據身分識別提供者， **issuerAssignedId**是每個應用程式或開發帳戶的指定使用者唯一的值。 使用先前由社交提供者或相同開發帳戶中的其他應用程式所指派的相同應用程式識別碼，設定 Azure AD B2C 原則。

### <a name="password-profile-property"></a>密碼配置檔案屬性

若為本機身分識別，則需要**passwordProfile**屬性，並包含使用者的密碼。 `forceChangePasswordNextSignIn`屬性必須設定為 `false` 。

若為同盟（社交）身分識別，則不需要**passwordProfile**屬性。

```json
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>密碼原則屬性

Azure AD B2C 密碼原則（針對本機帳戶）是以 Azure Active Directory[強式密碼強度](../active-directory/authentication/concept-sspr-policy.md)原則為基礎。 Azure AD B2C 註冊或登入和密碼重設原則需要此強式密碼強度，而且不會讓密碼過期。

在使用者遷移案例中，如果您想要遷移的帳戶密碼強度比 Azure AD B2C 強制執行的[強式密碼強度](../active-directory/authentication/concept-sspr-policy.md)還弱，您可以停用強式密碼需求。 若要變更預設密碼原則，請將 `passwordPolicies` 屬性設定為 `DisableStrongPassword`。 例如，您可以修改「建立使用者要求」，如下所示：

```json
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>延伸模組屬性

每個面向客戶的應用程式對於要收集的資訊都有獨特的需求。 您的 Azure AD B2C 租使用者隨附一組儲存在屬性中的內建資訊，例如指定的名稱、姓氏、城市和郵遞區號。 使用 Azure AD B2C，您可以擴充儲存在每個客戶帳戶中的屬性集合。 如需定義自訂屬性的詳細資訊，請參閱[自訂屬性（使用者流程）](user-flow-custom-attributes.md)和[自訂屬性（自訂原則）](custom-policy-custom-attributes.md)。

Microsoft Graph API 支援以擴充屬性建立和更新使用者。 圖形 API 中的擴充屬性會使用 `extension_ApplicationObjectID_attributename` 慣例來命名。 例如：

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

## <a name="code-sample"></a>程式碼範例

此程式碼範例是一個 .NET Core 主控台應用程式，它會使用[MICROSOFT GRAPH SDK](https://docs.microsoft.com/graph/sdks/sdks-overview)來與 Microsoft Graph API 互動。 其程式碼示範如何呼叫 API，以程式設計方式管理 Azure AD B2C 租使用者中的使用者。
您可以[下載範例](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip)封存（* .zip）、流覽 GitHub 上[的存放庫](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management)，或複製存放庫：

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

取得程式碼範例之後，請針對您的環境進行設定，然後建立專案：

1. 在[Visual Studio](https://visualstudio.microsoft.com)或[Visual Studio Code](https://code.visualstudio.com)中開啟專案。
1. 開啟 `src/appsettings.json`。
1. 在 `appSettings` 區段中，將取代 `your-b2c-tenant` 為您的租使用者名稱，並以 `Application (client) ID` 您的 `Client secret` 管理應用程式註冊值取代（請參閱本文的[註冊管理應用程式](#register-a-management-application)一節）。
1. 在存放庫的本機複本中開啟主控台視窗，切換至 `src` 目錄，然後建立專案：
    ```console
    cd src
    dotnet build
    ```
1. 使用 `dotnet` 命令執行應用程式：

    ```console
    dotnet bin/Debug/netcoreapp3.0/b2c-ms-graph.dll
    ```

應用程式會顯示您可以執行的命令清單。 例如，取得所有使用者、取得單一使用者、刪除使用者、更新使用者的密碼，以及大量匯入。

### <a name="code-discussion"></a>程式碼討論

範例程式碼會使用[MICROSOFT GRAPH SDK](https://docs.microsoft.com/graph/sdks/sdks-overview)，其設計目的是為了簡化可存取 Microsoft Graph 的高品質、有效率且可復原的應用程式。

Microsoft Graph API 的任何要求都需要存取權杖以進行驗證。 此解決方案會使用[microsoft. Graph. Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet 封裝，它會提供 microsoft 驗證程式庫（MSAL）的驗證案例型包裝函式，以與 Microsoft Graph SDK 搭配使用。

`RunAsync` _Program.cs_檔案中的方法：

1. 從檔案中的_appsettings.js_讀取應用程式設定
1. 使用[OAuth 2.0 用戶端認證授](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)與流程，初始化驗證提供者。 在用戶端認證授與流程中，應用程式可以取得存取權杖來呼叫 Microsoft Graph API。
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

然後會在_UserService.cs_中使用初始化的*GraphServiceClient*來執行使用者管理作業。 例如，取得租使用者中的使用者帳戶清單：

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

[使用 Microsoft Graph Sdk 進行 API 呼叫](https://docs.microsoft.com/graph/sdks/create-requests)包括如何從 Microsoft Graph 讀取和寫入資訊、用 `$select` 來控制傳回的屬性、提供自訂查詢參數，以及使用 `$filter` 和查詢參數的資訊 `$orderBy` 。

## <a name="next-steps"></a>後續步驟

如需 Azure AD B2C 資源支援之 Microsoft Graph API 作業的完整索引，請參閱[適用于 Azure AD B2C 的 Microsoft Graph 作業](microsoft-graph-operations.md)。

<!-- LINK -->

[graph-objectIdentity]: https://docs.microsoft.com/graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
