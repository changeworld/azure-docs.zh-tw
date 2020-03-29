---
title: 將基於 OWIN 的 Web API 遷移到b2clogin.com
titleSuffix: Azure AD B2C
description: 瞭解如何啟用 .NET Web API 以支援多個權杖頒發者在將應用程式遷移到b2clogin.com時頒發的權杖。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5daf88e746ea803f345c79bd31d656f2615b6754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184089"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom"></a>將基於 OWIN 的 Web API 遷移到b2clogin.com

本文介紹了一種技術，用於在實現[.NET （OWIN） 的開放 Web 介面](http://owin.org/)的 Web API 中為多個權杖頒發者提供支援。 將 Azure 活動目錄 B2C （Azure AD B2C） API 及其應用程式從*login.microsoftonline.com*遷移到*b2clogin.com*時，支援多個權杖終結點非常有用。

通過在 API 中添加支援以接受b2clogin.com和login.microsoftonline.com頒發的權杖，可以在從 API 中刪除對login.microsoftonline.com頒發的權杖的支援之前分階段遷移 Web 應用程式。

以下各節介紹了如何在使用[Microsoft OWIN][katana]中介軟體元件 （Katana） 的 Web API 中啟用多個頒發者的示例。 儘管代碼示例特定于 Microsoft OWIN 中介軟體，但常規技術應適用于其他 OWIN 庫。

> [!NOTE]
> 本文面向 Azure AD B2C 客戶，這些客戶當前已部署 API`login.microsoftonline.com`和應用程式引用並希望遷移到建議的`b2clogin.com`終結點。 如果要設置新應用程式，請按照指示使用[b2clogin.com。](b2clogin.md)

## <a name="prerequisites"></a>Prerequisites

在繼續執行本文中的步驟之前，您需要將以下 Azure AD B2C 資源到位：

* 在租戶中創建[的使用者流](tutorial-create-user-flows.md)或[自訂策略](custom-policy-get-started.md)

## <a name="get-token-issuer-endpoints"></a>獲取權杖頒發者終結點

您首先需要獲取要在 API 中支援的每個頒發者權杖頒發者終結點 URI。 要獲取 Azure AD B2C 租戶支援的*b2clogin.com**和login.microsoftonline.com*終結點，請使用 Azure 門戶中的以下過程。

首先選擇一個現有使用者流：

1. 導航到[Azure 門戶](https://portal.azure.com)中的 Azure AD B2C 租戶
1. 在**策略**下，選擇**使用者流（策略）**
1. 選擇現有策略，例如*B2C_1_signupsignin1*，然後選擇 **"運行使用者流"**
1. 在頁面頂部附近的 **"運行使用者流**"標題下，選擇要導航到該使用者流的 OpenID Connect 發現終結點的超連結。

    ![Azure 門戶的"立即運行"頁中的已知 URI 超連結](media/multi-token-endpoints/portal-01-policy-link.png)

1. 在瀏覽器中打開的頁面中，記錄`issuer`該值，例如：

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. 使用 **"選擇域**下拉清單"選擇其他域，然後再次執行前兩個步驟並記錄其`issuer`值。

現在，您應該記錄兩個類似于：

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>自訂原則

如果您有自訂策略而不是使用者流，則可以使用類似的過程來獲取頒發者 URI。

1. 導航到 Azure AD B2C 租戶
1. 選擇**身份體驗框架**
1. 選擇依賴方策略之一，例如 *，B2C_1A_signup_signin*
1. 使用 **"選擇域**"下拉清單選擇域，例如*yourtenant.b2clogin.com*
1. 選擇**OpenID 連接發現終結點**下顯示的超連結
1. 記錄`issuer`值
1. 為其他域執行步驟 4-6，例如*login.microsoftonline.com*

## <a name="get-the-sample-code"></a>取得範例程式碼

現在，您已經擁有了兩個權杖終結點 URI，您需要更新代碼以指定兩個終結點都是有效的頒發者。 要演練示例，請下載或克隆應用程式範例，然後更新示例以支援兩個終結點作為有效的頒發者。

下載存檔：[活動目錄-b2c-點網-webapp-webapi-master.zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>在 Web API 中啟用多個頒發者

在本節中，您將更新代碼以指定兩個權杖頒發者終結點都有效。

1. 在視覺化工作室中打開**B2C-WebAPI-DotNet.sln**解決方案
1. 在**任務服務**專案中，在編輯器中打開*任務\\服務\\App_Start [Startup.Auth.cs*] 檔
1. 將以下`using`指令添加到檔頂部：

    `using System.Collections.Generic;`
1. 將[`ValidIssuers`][validissuers]屬性添加到定義並[`TokenValidationParameters`][tokenvalidationparameters]指定在上一節中記錄的兩個 URI：

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters`由MSAL.NET提供，並由*oWIN*中介軟體在 Startup.Auth.cs 的下一節中使用。 指定多個有效頒發器後，OWIN 應用程式管道將知道兩個權杖終結點都是有效的頒發者。

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

如前所述，其他 OWIN 庫通常提供類似的功能來支援多個頒發者。 儘管為每個庫提供示例超出了本文的範圍，但您可以對大多數庫使用類似的技術。

## <a name="switch-endpoints-in-web-app"></a>切換 Web 應用中的終結點

Web API 現在支援這兩個 URI，現在需要更新 Web 應用程式，以便從b2clogin.com終結點檢索權杖。

例如，您可以通過修改**TaskWebApp**專案的*TaskWebApp _WebApp _WebApp_Web.config\\*_ 檔中`ida:AadInstance`的值來配置示例 Web 應用程式以使用新終結點。

更改`ida:AadInstance`TaskWebApp 的*Web.config 中*的值，以便`{your-b2c-tenant-name}.b2clogin.com`它引用`login.microsoftonline.com`而不是 。

之前：

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

之後（替換為`{your-b2c-tenant}`B2C 租戶的名稱）：

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

當在 Web 應用執行期間構造終結點字串時，在請求權杖時將使用基於b2clogin.com終結點。

## <a name="next-steps"></a>後續步驟

本文介紹了一種配置實現 Microsoft OWIN 中介軟體 （Katana） 的 Web API 的方法，以接受來自多個頒發者終結點的權杖。 您可能注意到 *，Web.Config*檔中的其他幾個字串是 TaskService 和 TaskWebApp 專案的檔，如果要針對自己的租戶生成和運行這些專案，則需要更改這些檔。 但是，如果您希望看到專案在操作中，歡迎您適當地修改這些專案，但是，這樣做的完整演練不在本文的範圍之內。

有關 Azure AD B2C 發出的不同類型的安全權杖的詳細資訊，請參閱 Azure[活動目錄 B2C 中的權杖概述](tokens-overview.md)。

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/
[validissuers]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
