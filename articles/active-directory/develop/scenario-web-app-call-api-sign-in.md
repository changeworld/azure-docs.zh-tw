---
title: 登出時從權杖快取移除帳戶-Microsoft 身分識別平臺 |Azure
description: 瞭解如何在登出時從權杖快取移除帳戶
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: a77bb59afa753fa9d1655e787d4f7a18715ed2ca
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701581"
---
# <a name="remove-accounts-from-the-cache-on-global-sign-out"></a>從全域登出的快取中移除帳戶

您已經知道如何將登入新增至您的 web 應用程式。 您會在[登入使用者的 Web 應用程式中瞭解如何新增登入](scenario-web-app-sign-user-sign-in.md)。

這裡的差異在於，當使用者已登出、來自此應用程式或任何應用程式時，您想要從權杖快取中移除，這是與使用者相關聯的權杖。

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>在登出後攔截回呼-單一登出

例如，您的應用程式可以在 `logout` 事件之後攔截，以清除與已登出之帳戶相關聯的權杖快取專案。Web 應用程式會將使用者的存取權杖儲存在快取中。 在 `logout` 回呼之後攔截，可讓您的 web 應用程式從權杖快取中移除使用者。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

這項機制會在 WebAppServiceCollectionExtensions 的 `AddMsal()` 方法中說明[# L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157)

您為應用程式註冊的**登出 Url**可讓您執行單一登出。Microsoft 身分識別平臺 `logout` 端點會呼叫向您的應用程式註冊的**登出 URL** 。 如果登出是從您的 web 應用程式或從另一個 web 應用程式或瀏覽器起始，就會發生此呼叫。 如需詳細資訊，請參閱[單一登出](v2-protocols-oidc.md#single-sign-out)。

```csharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: removing the account from MSAL.NET cache
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

RemoveAccountAsync 的程式碼可從 TokenAcquisition 取得，[網址為 Web/.cs # L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288)。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 範例不會從全域登出的快取中移除帳戶

# <a name="javatabjava"></a>[Java](#tab/java)

JAVA 範例不會從全域登出的快取中移除帳戶

# <a name="pythontabpython"></a>[Python](#tab/python)

Python 範例不會從全域登出的快取中移除帳戶

---

## <a name="next-steps"></a>後續步驟

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [取得 web 應用程式的權杖](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [取得 web 應用程式的權杖](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [取得 web 應用程式的權杖](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [取得 web 應用程式的權杖](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
