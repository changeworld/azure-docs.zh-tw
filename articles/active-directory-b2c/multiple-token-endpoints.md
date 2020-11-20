---
title: 將以 OWIN 為基礎的 web Api 遷移至 b2clogin.com
titleSuffix: Azure AD B2C
description: 瞭解如何在您將應用程式遷移至 b2clogin.com 時，讓 .NET web API 支援多個權杖簽發者所簽發的權杖。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c362ce256259606c85af0a7e13ccde1715bb012b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953928"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom"></a>將以 OWIN 為基礎的 web API 遷移至 b2clogin.com

本文說明一種技術，可讓您在 web Api 中啟用多個權杖簽發者的支援，以執行 [.net (OWIN) 的開放式 Web 介面 ](http://owin.org/)。 當您要將 Azure Active Directory B2C (Azure AD B2C) Api 和其應用程式從 *login.microsoftonline.com* 遷移至 *b2clogin.com* 時，支援多個權杖端點會很有用。

藉由在 API 中新增支援 b2clogin.com 和 login.microsoftonline.com 所發出的權杖，您可以在從 API 中移除 login.microsoftonline.com 發行權杖的支援之前，以分段的方式遷移您的 web 應用程式。

下列各節示範如何在使用 [MICROSOFT OWIN][katana] 中介軟體元件的 web API 中啟用多個簽發者 (Katana) 。 雖然程式碼範例是 Microsoft OWIN 中介軟體專屬的，但一般技術應該適用于其他 OWIN 程式庫。

> [!NOTE]
> 本文適用于目前已部署的 Api 和應用程式， `login.microsoftonline.com` 且想要遷移至建議端點的 Azure AD B2C 客戶 `b2clogin.com` 。 如果您要設定新的應用程式，請依照指示使用 [b2clogin.com](b2clogin.md) 。

## <a name="prerequisites"></a>Prerequisites

您需要下列 Azure AD B2C 資源，才能繼續進行本文中的步驟：

* 在您的租使用者中建立的[使用者流程](tutorial-create-user-flows.md)或[自訂原則](custom-policy-get-started.md)

## <a name="get-token-issuer-endpoints"></a>取得權杖簽發者端點

您必須先針對您想要在 API 中支援的每個簽發者取得權杖簽發者端點 Uri。 若要取得 Azure AD B2C 租使用者所支援的 *b2clogin.com* 和 *login.microsoftonline.com* 端點，請在 Azure 入口網站中使用下列程式。

首先，選取其中一個現有的使用者流程：

1. 流覽至您在[Azure 入口網站](https://portal.azure.com)中的 Azure AD B2C 租使用者
1. 在 [ **原則**] 底下，選取 [ **使用者流程] (原則)**
1. 選取現有的原則，例如 *B2C_1_signupsignin1*，然後選取 [**執行使用者流程**]
1. 在接近頁面頂端的 [ **執行使用者流程** ] 標題下，選取超連結以流覽至該使用者流程的 OpenID Connect 探索端點。

    ![Azure 入口網站上 [立即執行] 頁面中已知的 URI 超連結](media/multi-token-endpoints/portal-01-policy-link.png)

1. 在瀏覽器中開啟的頁面上，記錄 `issuer` 值，例如：

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. 使用 [ **選取網域** ] 下拉式清單選取其他網域，然後再次執行前兩個步驟，並記錄其 `issuer` 值。

您現在應該有兩個記錄的 Uri，如下所示：

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>自訂原則

如果您有自訂原則，而不是使用者流程，您可以使用類似的進程來取得簽發者 Uri。

1. 流覽至您的 Azure AD B2C 租使用者
1. 選取 **Identity Experience Framework**
1. 選取您的信賴憑證者原則之一，例如 *B2C_1A_signup_signin*
1. 使用 [ **選取網域** ] 下拉式清單來選取網域，例如 *yourtenant.b2clogin.com*
1. 選取 [ **OpenID Connect 探索端點**] 下顯示的超連結
1. 記錄 `issuer` 值
1. 執行其他網域的步驟4-6，例如 *login.microsoftonline.com*

## <a name="get-the-sample-code"></a>取得範例程式碼

現在您已經有兩個權杖端點 Uri，您必須更新您的程式碼，以指定兩個端點都是有效的簽發者。 若要逐步解說範例，請下載或複製範例應用程式，然後更新範例以同時支援這兩個端點作為有效的簽發者。

下載封存： [active-directory-b2c-dotnet-webapp-and-webapi-master.zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>在 web API 中啟用多個簽發者

在本節中，您會更新程式碼，以指定兩個權杖簽發者端點都有效。

1. 在 Visual Studio 中開啟 **B2C-WebAPI-DotNet .sln** 方案
1. 在 **TaskService** 專案中，于編輯器中開啟 * TaskService \\ App_Start \\ **Startup.Auth.cs** _ 檔案
1. 將下列指示詞新增 `using` 至檔案頂端：

    `using System.Collections.Generic;`
1. 將 [`ValidIssuers`][validissuers] 屬性新增至 [`TokenValidationParameters`][tokenvalidationparameters] 定義，並指定您在上一節中記錄的兩個 uri：

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

`TokenValidationParameters` 是由 MSAL.NET 所提供，並且是由 _Startup .cs * 中下一節程式碼的 OWIN 中介軟體所使用。 指定了多個有效的簽發者之後，OWIN 應用程式管線就會知道這兩個權杖端點都是有效的簽發者。

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

如先前所述，其他 OWIN 程式庫通常會提供類似的功能來支援多個簽發者。 雖然提供每個程式庫的範例超出本文的範圍，但您可以對大部分的程式庫使用類似的技術。

## <a name="switch-endpoints-in-web-app"></a>在 web 應用程式中切換端點

您的 web API 現在支援兩個 Uri，您現在需要更新 web 應用程式，以便從 b2clogin.com 端點抓取權杖。

例如，您可以將範例 web 應用程式設定為使用新的端點，方法是修改 `ida:AadInstance` _ TaskWebApp * * 專案的 *TaskWebApp \\ **Web.config** _* 檔案中的值。

變更 `ida:AadInstance` TaskWebApp *Web.config* 中的值，使其參考， `{your-b2c-tenant-name}.b2clogin.com` 而不是 `login.microsoftonline.com` 。

之前：

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

在 (取代 `{your-b2c-tenant}` 為您的 B2C 租使用者名稱) ：

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

在 web 應用程式執行期間建立端點字串時，會在要求權杖時使用以 b2clogin.com 為基礎的端點。

## <a name="next-steps"></a>後續步驟

本文提供了一種方法來設定執行 Microsoft OWIN 中介軟體的 web API， (Katana) 接受來自多個簽發者端點的權杖。 您可能會注意到，當您想要針對自己的租使用者建立並執行這些專案時，TaskService 和 TaskWebApp 專案的 *Web.Config* 檔案中有其他數個字串需要變更。 如果您想要查看這些專案的實際運作情況，歡迎您適當地修改專案，不過，在本文的討論範圍內，您將會有完整的逐步解說。

如需 Azure AD B2C 所發出之不同型別安全性權杖的詳細資訊，請參閱 [Azure Active Directory B2C 中的權杖總覽](tokens-overview.md)。

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: /aspnet/aspnet/overview/owin-and-katana/
[validissuers]: /dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: /dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters