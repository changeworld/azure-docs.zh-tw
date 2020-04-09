---
title: 從註銷時從權杖快取中刪除帳戶 - 微軟標識平臺 |蔚藍
description: 瞭解如何在登出時從權杖快取中移除帳戶
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 934b756329065c466f21fca1480247065bdea28b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881607"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>呼叫 Web API 的 Web 應用:從全域登出的權杖快取中移除帳戶

您學習了如何在[Web 應用程式中向登入使用者的 Web 應用加入:登入和登出](scenario-web-app-sign-user-sign-in.md)。

對於調用 Web api 的 Web 應用,登出是不同的。 當使用者從應用程式或任何應用程式退出時,必須從令牌緩存中刪除與該使用者關聯的權杖。

## <a name="intercept-the-callback-after-single-sign-out"></a>單次登出後攔截回調

要清除與註銷的帳戶關聯的權杖緩存條目,應用程式可以攔截後一`logout`個事件。 Web 應用在令牌緩存中存儲每個使用者的訪問令牌。 通過攔截后`logout`回調,Web 應用程式可以從快取中刪除使用者。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

對於ASP.NET核心,攔截機制在`AddMsal()`[WebAppService 收集擴展.cs_L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157)的方法中進行了說明。

您以前為應用程式註冊的登出 URL 使您能夠實現單一登出。Microsoft 標識`logout`平台 終結點調用註銷 URL。 如果登出從 Web 應用或從其他 Web 應用或瀏覽器啟動,則會發生此調用。 有關詳細資訊,請參閱[單一登出](v2-protocols-oidc.md#single-sign-out)。

```csharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: Remove the account from MSAL.NET cache.
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache.
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

的代碼`RemoveAccountAsync`可從[Microsoft.標識.Web/TokenAcquisition.cs_L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288)獲得。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET示例不會從全域註銷的緩存中刪除帳戶。

# <a name="java"></a>[Java](#tab/java)

Java 示例不會從全域註銷的緩存中刪除帳戶。

# <a name="python"></a>[Python](#tab/python)

Python 範例不會從全域登出的快取中刪除帳戶。

---

## <a name="next-steps"></a>後續步驟

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [取得 Web 應用的權杖](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [取得 Web 應用的權杖](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [取得 Web 應用的權杖](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [取得 Web 應用的權杖](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
