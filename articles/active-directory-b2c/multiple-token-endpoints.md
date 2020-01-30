---
title: 將以 OWIN 為基礎的 web Api 遷移至 b2clogin.com
titleSuffix: Azure AD B2C
description: 瞭解如何啟用 .NET Web API，以便在您將應用程式遷移至 b2clogin.com 時支援多個權杖簽發者所發出的權杖。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e6a92d12e7f2f24289aafa7e4a9acc9edccbd34c
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840395"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom"></a>將以 OWIN 為基礎的 Web API 遷移至 b2clogin.com

本文說明在執行[Open Web Interface for .net （OWIN）](http://owin.org/)的 web api 中啟用多個權杖簽發者支援的技術。 當您要將 Azure Active Directory B2C （Azure AD B2C） Api 及其應用程式從*login.microsoftonline.com*遷移至*b2clogin.com*時，支援多個權杖端點會很有用。

藉由在您的 API 中新增支援以接受 b2clogin.com 和 login.microsoftonline.com 所簽發的權杖，您可以在移除 API 的 login.microsoftonline.com 發行權杖支援之前，以分段方式遷移 web 應用程式。

下列各節提供一個範例，說明如何在使用[MICROSOFT OWIN][katana]中介軟體元件（Katana）的 Web API 中啟用多個簽發者。 雖然程式碼範例是 Microsoft OWIN 中介軟體特有的，但一般技術應該適用于其他 OWIN 程式庫。

> [!NOTE]
> 本文適用 Azure AD B2C 于目前已部署 Api 的客戶，以及參考 `login.microsoftonline.com` 且想要遷移至建議 `b2clogin.com` 端點的應用程式。 如果您要設定新的應用程式，請依指示使用[b2clogin.com](b2clogin.md) 。

## <a name="prerequisites"></a>必要條件

繼續進行本文中的步驟之前，您必須先準備好下列 Azure AD B2C 資源：

* 在您的租使用者中建立的[使用者流程](tutorial-create-user-flows.md)或[自訂原則](custom-policy-get-started.md)

## <a name="get-token-issuer-endpoints"></a>取得權杖簽發者端點

您必須先針對要在 API 中支援的每個簽發者取得權杖簽發者端點 Uri。 若要取得 Azure AD B2C 租使用者所支援的*b2clogin.com*和*login.microsoftonline.com*端點，請在 Azure 入口網站中使用下列程式。

首先，選取其中一個現有的使用者流程：

1. 流覽至您在[Azure 入口網站](https://portal.azure.com)中的 Azure AD B2C 租使用者
1. 在 [**原則**] 底下，選取 **[使用者流程（原則）** ]
1. 選取現有的原則，例如*B2C_1_signupsignin1*，然後選取 [**執行使用者流程**]
1. 在靠近頁面頂端的 [**執行使用者流程**] 標題底下，選取超連結以流覽至該使用者流程的 OpenID connect 探索端點。

    ![Azure 入口網站的 [立即執行] 頁面中的知名 URI 超連結](media/multi-token-endpoints/portal-01-policy-link.png)

1. 在瀏覽器中開啟的頁面上，記錄 `issuer` 的值，例如：

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. 使用 [**選取網域**] 下拉式選選取其他網域，然後再次執行前兩個步驟，並記錄其 `issuer` 值。

您現在應該已記錄兩個 Uri，如下所示：

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>自訂原則

如果您有自訂原則，而不是使用者流程，您可以使用類似的進程來取得簽發者 Uri。

1. 流覽至您的 Azure AD B2C 租使用者
1. 選取 [識別體驗架構]
1. 選取其中一個信賴憑證者原則，例如， *B2C_1A_signup_signin*
1. 使用 [**選取網域**] 下拉式選來選取網域，例如*yourtenant.b2clogin.com*
1. 選取 [ **OpenID connect 探索端點]** 底下顯示的超連結
1. 記錄 `issuer` 值
1. 針對另一個網域執行步驟4-6，例如*login.microsoftonline.com*

## <a name="get-the-sample-code"></a>取得範例程式碼

現在您已有兩個權杖端點 Uri，您必須更新您的程式碼，以指定兩個端點都是有效的簽發者。 若要逐步解說範例，請下載或複製範例應用程式，然後更新範例以同時支援這兩個端點做為有效的簽發者。

下載封存檔： [active-directory-b2c-dotnet-webapp-and-webapi-master .zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>在 Web API 中啟用多個簽發者

在本節中，您會更新程式碼，以指定兩個權杖簽發者端點都是有效的。

1. 在 Visual Studio 中開啟 [ **B2C-WebAPI-DotNet** ] 方案
1. 在**TaskService**專案中，于您的編輯器中開啟*TaskService\\App_Start\\* * Startup.Auth.cs** * 檔案
1. 將下列 `using` 指示詞新增至檔案頂端：

    `using System.Collections.Generic;`
1. 將[`ValidIssuers`][validissuers]屬性新增至[`TokenValidationParameters`][tokenvalidationparameters]定義，並指定您在上一節中記錄的兩個 uri：

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

`TokenValidationParameters` 是由 MSAL.NET 提供，而且 OWIN 中介軟體會在*Startup.Auth.cs*的程式碼的下一節中使用。 指定了多個有效的簽發者之後，OWIN 應用程式管線會注意到這兩個權杖端點都是有效的簽發者。

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

如先前所述，其他 OWIN 程式庫通常會提供類似的功能來支援多個簽發者。 雖然提供每個程式庫的範例不在本文的討論範圍內，但您可以對大部分的程式庫使用類似的技術。

## <a name="switch-endpoints-in-web-app"></a>在 web 應用程式中切換端點

Web API 現在支援這兩個 Uri，您現在需要更新 web 應用程式，讓它從 b2clogin.com 端點抓取權杖。

例如，您可以藉由修改**TaskWebApp**專案之*TaskWebApp\\* * web.config** * 檔案中的 `ida:AadInstance` 值，將範例 web 應用程式設定為使用新的端點。

變更*TaskWebApp 的 web.config*中的 `ida:AadInstance` 值，使其參考 `{your-b2c-tenant-name}.b2clogin.com` 而不是 `login.microsoftonline.com`。

之前：

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

After （以 B2C 租使用者的名稱取代 `{your-b2c-tenant}`）：

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

當端點字串在 web 應用程式執行期間進行結構化時，會在要求權杖時使用以 b2clogin.com 為基礎的端點。

## <a name="next-steps"></a>後續步驟

本文提供一種方法，說明如何設定執行 Microsoft OWIN 中介軟體（Katana）的 Web API，以接受來自多個簽發者端點的權杖。 您可能會注意到，如果您想要針對自己的租使用者建立及執行這些專案，TaskService 和 TaskWebApp 專案*的 web.config 檔案*中會有其他幾個字串。 如果您想要查看它們的實際運作方式，歡迎您修改專案，不過，完整的逐步解說不在本文的討論範圍內。

如需 Azure AD B2C 所發出之不同安全性權杖類型的詳細資訊，請參閱[Azure Active Directory B2C 中的權杖總覽](tokens-overview.md)。

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/
[validissuers]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
