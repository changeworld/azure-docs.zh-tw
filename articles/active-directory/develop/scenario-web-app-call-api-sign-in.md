---
title: 從登出時從權杖緩存中刪除帳戶 - 微軟標識平臺 |蔚藍
description: 瞭解如何在登出時從權杖緩存中刪除帳戶
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
ms.openlocfilehash: ea18538662dc63876a50f52e9e6a8b3fffb3b35a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76758865"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>調用 Web API 的 Web 應用：從全域登出的權杖緩存中刪除帳戶

您學習了如何在[Web 應用中向登錄使用者的 Web 應用添加登錄：登錄和登出](scenario-web-app-sign-user-sign-in.md)。

對於調用 Web api 的 Web 應用，登出是不同的。 當使用者從應用程式或任何應用程式退出時，必須從權杖緩存中刪除與該使用者關聯的權杖。

## <a name="intercept-the-callback-after-single-sign-out"></a>單次登出後攔截回檔

要清除與登出的帳戶關聯的權杖緩存條目，應用程式可以攔截後一`logout`個事件。 Web 應用在權杖緩存中存儲每個使用者的訪問權杖。 通過攔截後`logout`回檔，Web 應用程式可以從緩存中刪除使用者。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

對於ASP.NET核心，攔截機制在`AddMsal()`[WebAppService 收集擴展.cs_L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157)的方法中進行了說明。

您以前為應用程式註冊的登出 URL 使您能夠實現單一登出。Microsoft 標識平臺`logout`終結點調用登出 URL。 如果登出從 Web 應用或從其他 Web 應用或瀏覽器啟動，則會發生此調用。 有關詳細資訊，請參閱[單一登出](v2-protocols-oidc.md#single-sign-out)。

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

ASP.NET示例不會從全域登出的緩存中刪除帳戶。

# <a name="java"></a>[JAVA](#tab/java)

JAVA 示例不會從全域登出的緩存中刪除帳戶。

# <a name="python"></a>[Python](#tab/python)

Python 示例不會從全域登出的緩存中刪除帳戶。

---

## <a name="next-steps"></a>後續步驟

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [獲取 Web 應用的權杖](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [獲取 Web 應用的權杖](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="java"></a>[JAVA](#tab/java)

> [!div class="nextstepaction"]
> [獲取 Web 應用的權杖](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [獲取 Web 應用的權杖](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
