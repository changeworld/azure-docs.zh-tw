---
title: 使用 Microsoft 圖形 API 管理使用者
titleSuffix: Azure AD B2C
description: 如何透過調用 Microsoft 圖形 API 並使用應用程式識別來自動執行該過程來管理 Azure AD B2C 租戶中的使用者。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 42596ba5470c6062efba4fd1050c1c9745b76e80
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637336"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>使用微軟圖形管理 Azure AD B2C 使用者帳戶

Microsoft 圖形允許您透過在 Microsoft 圖形 API 中提供建立、讀取、更新和刪除方法來管理 Azure AD B2C 目錄中的使用者帳戶。 您可以將現有使用者儲存遷移到 Azure AD B2C 租戶,並透過呼叫 Microsoft 圖形 API 執行其他使用者帳戶管理操作。

在以下各節中,介紹了使用 Microsoft 圖形 API 進行 Azure AD B2C 使用者管理的關鍵方面。 此處介紹的 Microsoft 圖形 API 操作、類型和屬性是 Microsoft 圖形 API 參考文件中顯示的操作、類型和屬性的子集。

## <a name="register-a-management-application"></a>註冊管理應用程式

在編寫的任何使用者管理應用程式或腳本都可以與 Azure AD B2C 租戶中的資源進行交互之前,需要授予執行此操作的許可權的應用程式註冊。

以本操作操作文章中的步驟建立管理應用程式可以使用的應用程式註冊:

[使用微軟圖形管理 Azure AD B2C](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>使用者管理 Microsoft St

以下使用者管理操作在[Microsoft 圖形 API](https://docs.microsoft.com/graph/api/resources/user)中可用:

- [取得使用者清單](https://docs.microsoft.com/graph/api/user-list)
- [建立使用者](https://docs.microsoft.com/graph/api/user-post-users)
- [取得使用者](https://docs.microsoft.com/graph/api/user-get)
- [更新使用者](https://docs.microsoft.com/graph/api/user-update)
- [刪除使用者](https://docs.microsoft.com/graph/api/user-delete)

## <a name="user-properties"></a>使用者屬性

### <a name="display-name-property"></a>顯示名稱屬性

是`displayName`要在 Azure 門戶使用者管理中顯示的名稱,在訪問權杖 Azure AD B2C 中傳回到應用程式。 這是必要屬性。

### <a name="identities-property"></a>識別屬性

客戶帳戶(可以是消費者、合作夥伴或公民)可以與以下標識類型相關聯:

- **本地**識別 - 使用者名稱和密碼儲存在 Azure AD B2C 目錄中的本地端。 我們經常將這些身份稱為"本地帳戶」。
- **聯合**標識 - 也稱為*社交*或*企業*帳戶,使用者的身份由聯合標識供應商(如 Facebook、Microsoft、ADFS 或 Salesforce)管理。

具有客戶帳戶的使用者可以使用多個標識登錄。 例如,使用者名、電子郵件、員工 ID、政府 ID 等。 單個帳戶可以具有多個標識(本地身份和社交身份)具有相同的密碼。

在 Microsoft 圖形 API 中,本地識別和聯合識別`identities`都儲存在使用者 屬性中,該屬性的類型為[物件識別][graph-objectIdentity]。 該`identities`集合表示用於登錄到使用者帳戶的一組標識。 此集合使用戶能夠使用任何關聯標識登錄到使用者帳戶。

| 屬性   | 類型 |描述|
|:---------------|:--------|:----------|
|符號類型|字串| 指定目錄中的使用者登錄類型。 對於本地帳戶:、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、 `emailAddress` `emailAddress1` `emailAddress2` `emailAddress3` `userName` 社會帳號必須設定為`federated`。|
|簽發者|字串|指定標識的頒發者。 對本地帳號(**其中 signInType**`federated`不是),此屬性是本地 B2C 租戶預設`contoso.onmicrosoft.com`網域名稱,例如 。 對於社交識別(**其中符號InType**為`federated`) 值是頒發者的名稱,例如`facebook.com`|
|發行人配置ID|字串|指定頒發者分配給使用者的唯一標識符。 **頒發者和頒發**者**AssignedId**的組合在租戶中必須是唯一的。 對於本地帳戶,當**signInType**設置`emailAddress``userName`為 或 時,它表示使用者的登錄名稱。<br>選擇**符號類型**設定為: <ul><li>`emailAddress`(或從`emailAddress``emailAddress1`喜歡 開始)**設定者配置 Id**必須是有效的電子郵件地址</li><li>`userName`( 或任何其他值),**發行者 AssignedId**必須是電子郵件地址的有效[本地部分](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`,**發行者分配 Id**表示聯合帳戶的唯一識別碼</li></ul>|

以下**標識**屬性,具有具有登錄名的本地帳戶標識、作為登錄名的電子郵寄地址以及具有社交標識。 

 ```JSON
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

對於聯合識別,根據識別提供者,**頒發者 AssignedId**是每個應用程式或開發帳戶給定使用者的唯一值。 配置 Azure AD B2C 策略時,具有以前由社交提供者或同一開發帳戶中其他應用程式分配的應用程式 ID。

### <a name="password-profile-property"></a>密碼設定檔屬性

對於本地標識,密碼**配置檔**屬性是必需的,並包含使用者的密碼。 屬性`forceChangePasswordNextSignIn`必須設定`false`為 。

對於聯合(社交)標識,密碼**配置檔**屬性不是必需的。

```JSON
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>密碼原則屬性

Azure AD B2C 密碼策略(針對本地帳戶)基於 Azure 活動目錄[強密碼強度](../active-directory/authentication/concept-sspr-policy.md)策略。 Azure AD B2C 註冊或登錄和密碼重置策略需要此強密碼強度,並且不會使密碼過期。

在使用者遷移方案中,如果要遷移的帳戶的密碼強度低於 Azure AD B2C 強制[的強密碼強度](../active-directory/authentication/concept-sspr-policy.md),則可以禁用強密碼要求。 若要變更預設密碼原則，請將 `passwordPolicies` 屬性設定為 `DisableStrongPassword`。 例如，您可以修改「建立使用者要求」，如下所示：

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>延伸屬性

每個面向客戶的應用程式對要收集的資訊都有獨特的要求。 Azure AD B2C 租戶附帶一組內置資訊,這些資訊存儲在屬性中,如給定名稱、姓氏、城市和郵政編碼。 使用 Azure AD B2C,可以擴展存儲在每個客戶帳戶中的屬性集。 有關定義自定義屬性的詳細資訊,請參閱[自定義屬性(使用者流)](user-flow-custom-attributes.md)和[自定義屬性(自定義策略)。](custom-policy-custom-attributes.md)

Microsoft 圖形 API 支援使用擴充屬性創建和更新使用者。 圖形 API 中的擴充屬性會使用 `extension_ApplicationObjectID_attributename` 慣例來命名。 例如：

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

## <a name="code-sample"></a>程式碼範例

此代碼示例是 .NET 核心主控台應用程式,它使用[Microsoft圖形SDK](https://docs.microsoft.com/graph/sdks/sdks-overview)與Microsoft圖形API進行互動。 其代碼演示如何將 API 調用以程式設計方式管理 Azure AD B2C 租戶中的使用者。
您可以[下載範例存檔](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip)(*.zip)、瀏覽 GitHub 上的[儲存函式](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management)庫或複製儲存函式庫:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

取得代碼範例後,為其環境配置它,然後產生專案:

1. 在[視覺工作室](https://visualstudio.microsoft.com)或[視覺工作室代碼](https://code.visualstudio.com)中打開專案。
1. 開啟 `src/appsettings.json`。
1. 在本`appSettings`節中,使用租`your-b2c-tenant`戶的名稱`Application (client) ID``Client secret`以及管理應用程式註冊的值替換(請參閱本文的["註冊管理應用程式](#register-a-management-application)"部分)。
1. 開啟本地克隆中的主控台視窗,切換`src`到 目錄,然後產生專案:
    ```console
    cd src
    dotnet build
    ```
1. 使用 `dotnet` 命令執行應用程式：

    ```console
    dotnet bin/Debug/netcoreapp3.0/b2c-ms-graph.dll
    ```

應用程式顯示可以執行的命令清單。 例如,獲取所有使用者、獲取單個使用者、刪除使用者、更新用戶的密碼以及批量導入。

### <a name="code-discussion"></a>代碼討論

範例代碼使用[Microsoft圖形SDK,](https://docs.microsoft.com/graph/sdks/sdks-overview)旨在簡化建構存取Microsoft Graph的高品質、高效和彈性應用程式。

對 Microsoft 圖形 API 的任何請求都需要存取權杖進行身份驗證。 該解決方案使用[Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet 套件,該包提供 Microsoft 身份驗證庫 (MSAL) 的基於身份驗證方案的包裝器,以便與 Microsoft 圖形 SDK 一起使用。

Program.cs`RunAsync`檔案中的方法_Program.cs_:

1. 從_應用程式設定.json_檔案讀取應用程式設定
1. 使用[OAuth 2.0 用戶端認證授權授權提供授權](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)授權提供者。 使用客戶端認證授予串流,應用能夠取得存取權碼來調用Microsoft圖形API。
1. 使用認證提供者設定 Microsoft 圖形服務用戶端:

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

然後,在_UserService.cs_中使用初始化*的圖形ServiceClient*來執行使用者管理操作。 例如,獲取租戶中的使用者帳戶清單:

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

[使用 Microsoft 圖形 SDK 進行 API 呼叫](https://docs.microsoft.com/graph/sdks/create-requests)包括有關如何從 Microsoft`$select`圖形讀取和寫入資訊 、用於控制返回`$filter`的`$orderBy`屬性、提供自訂查詢參數以及使用 和查詢參數的資訊。

## <a name="next-steps"></a>後續步驟

有關 Azure AD B2C 資源支援的 Microsoft 圖形 API 操作的完整索引,請參閱[適用於 Azure AD B2C 的 Microsoft 圖形操作](microsoft-graph-operations.md)。

<!-- LINK -->

[graph-objectIdentity]: https://docs.microsoft.com/graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
