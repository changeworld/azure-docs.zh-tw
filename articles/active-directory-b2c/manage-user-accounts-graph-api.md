---
title: 使用 Microsoft Graph API 管理使用者
titleSuffix: Azure AD B2C
description: 如何藉由呼叫 Microsoft Graph API，並使用應用程式識別來將程式自動化，以管理 Azure AD B2C 租使用者中的使用者。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/13/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff3cd858de86d21637f4a7a9ab9d9a83c7022f5a
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178869"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>使用 Microsoft Graph 管理 Azure AD B2C 的使用者帳戶

Microsoft Graph 可讓您在 Microsoft Graph API 中提供建立、讀取、更新和刪除方法，以管理 Azure AD B2C 目錄中的使用者帳戶。 您可以藉由呼叫 Microsoft Graph API，將現有的使用者存放區遷移至 Azure AD B2C 的租使用者，並執行其他使用者帳戶管理作業。

在接下來的各節中，會顯示 Azure AD B2C 的 Microsoft Graph API 進行使用者管理的重要層面。 此處所示的 Microsoft Graph API 作業、類型和屬性是出現在 Microsoft Graph API 參考檔中的子集。

## <a name="register-a-management-application"></a>註冊管理應用程式

在您撰寫的任何使用者管理應用程式或腳本都可以與 Azure AD B2C 租使用者中的資源互動之前，您需要可授與許可權以進行授權的應用程式註冊。

遵循本操作說明文章中的步驟，建立您的管理應用程式可使用的應用程式註冊：

[使用 Microsoft Graph 管理 Azure AD B2C](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>使用者管理 Microsoft Graph 作業

[MICROSOFT GRAPH API](/graph/api/resources/user)中提供下列使用者管理作業：

- [取得使用者清單](/graph/api/user-list)
- [建立使用者](/graph/api/user-post-users)
- [取得使用者](/graph/api/user-get)
- [更新使用者](/graph/api/user-update)
- [刪除使用者](/graph/api/user-delete)


## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>程式碼範例：如何以程式設計方式管理使用者帳戶

這個程式碼範例是一個 .NET Core 主控台應用程式，它會使用 [MICROSOFT GRAPH SDK](/graph/sdks/sdks-overview) 來與 Microsoft Graph API 互動。 其程式碼示範如何呼叫 API，以程式設計方式管理 Azure AD B2C 租使用者中的使用者。
您可以 [下載範例](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) 封存 ( * .zip) 、流覽 GitHub 上 [的存放庫](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) ，或複製存放庫：

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

取得程式碼範例之後，請針對您的環境進行設定，然後建立專案：

1. 在 [Visual Studio](https://visualstudio.microsoft.com) 中開啟專案或 [Visual Studio Code](https://code.visualstudio.com)。
1. 開啟 `src/appsettings.json`。
1. 在 `appSettings` 區段中，將取代 `your-b2c-tenant` 為您的租使用者名稱， `Application (client) ID` 並 `Client secret` 以管理應用程式註冊的值取代 (參閱本文的「 [註冊管理應用程式](#register-a-management-application) 」一節) 。
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

## <a name="next-steps"></a>下一步

如需 Azure AD B2C 資源支援之 Microsoft Graph API 作業的完整索引，請參閱 [適用于 Azure AD B2C 的 Microsoft Graph 作業](microsoft-graph-operations.md)。

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
