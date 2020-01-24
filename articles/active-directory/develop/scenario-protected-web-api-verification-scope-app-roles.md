---
title: 驗證範圍 & 應用程式角色保護的 Web API |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何建立受保護的 Web API，並設定應用程式的程式碼。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e7bf7464411aa85bd89ef8e186d2fe526bd8b17d
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701938"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>受保護的 Web API：確認範圍和應用程式角色

本文說明如何將授權新增至您的 Web API。 這項保護可確保只會呼叫 API：

- 應用程式代表具有正確範圍的使用者。
- 具有正確應用程式角色的 Daemon 應用程式。

> [!NOTE]
> 本文中的程式碼片段會從下列可完整運作的範例中解壓縮。
>
> - GitHub 上的[ASP.NET Core WEB API 增量教學](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37)課程
> - [ASP.NET Web API 範例](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

若要保護 ASP.NET/ASP.NET Core Web API，您必須在下列其中一項上新增 `[Authorize]` 屬性：

- 控制器本身，如果您想要保護控制器的所有動作
- 適用于您 API 的個別控制器動作

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

但這種保護不夠。 它只保證 ASP.NET/ASP.NET 核心會驗證權杖。 您的 API 必須確認用來呼叫您的 Web API 的權杖是以預期的宣告來要求，特別是：

- 如果代表使用者呼叫 API，則為*範圍*。
- *應用程式角色*（如果可以從 daemon 應用程式呼叫 API）。

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>在代表使用者呼叫的 Api 中驗證範圍

如果您的 API 是由用戶端應用程式代表使用者呼叫，它就必須要求具有 API 特定範圍的持有人權杖。 （請參閱程式[代碼設定 |持有人權杖](scenario-protected-web-api-app-configuration.md#bearer-token)）。

```csharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result.
        ...
    }
...
}
```

`VerifyUserHasAnyAcceptedScope` 方法會執行如下的動作：

- 確認有名為 `http://schemas.microsoft.com/identity/claims/scope` 或 `scp`的宣告。
- 確認宣告的值包含 API 所預期的範圍。

```csharp
    /// <summary>
    /// When applied to a <see cref="HttpContext"/>, verifies that the user authenticated in the 
    /// web API has any of the accepted scopes.
    /// If the authenticated user doesn't have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized error with a message noting which scopes are expected in the token.
    /// </summary>
    /// <param name="acceptedScopes">Scopes accepted by this API</param>
    /// <exception cref="HttpRequestException"/> with a <see cref="HttpResponse.StatusCode"/> set to 
    /// <see cref="HttpStatusCode.Unauthorized"/>
    public static void VerifyUserHasAnyAcceptedScope(this HttpContext context,
                                                     params string[] acceptedScopes)
    {
        if (acceptedScopes == null)
        {
            throw new ArgumentNullException(nameof(acceptedScopes));
        }
        Claim scopeClaim = HttpContext?.User
                                      ?.FindFirst("http://schemas.microsoft.com/identity/claims/scope");
        if (scopeClaim == null || !scopeClaim.Value.Split(' ').Intersect(acceptedScopes).Any())
        {
            context.Response.StatusCode = (int)HttpStatusCode.Unauthorized;
            string message = $"The 'scope' claim does not contain scopes '{string.Join(",", acceptedScopes)}' or was not found";
            throw new HttpRequestException(message);
        }
    }
```

這個[範例程式碼](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47)適用于 ASP.NET Core。 針對 ASP.NET，只要以 `ClaimsPrincipal.Current`取代 `HttpContext.User`，並以 `"scp"`取代宣告類型 `"http://schemas.microsoft.com/identity/claims/scope"`。 （另請參閱本文稍後的程式碼片段）。

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>在由 daemon 應用程式呼叫的 Api 中驗證應用程式角色

如果您的 Web API 是由「背景程式」[應用程式](scenario-daemon-overview.md)所呼叫，該應用程式應該需要您 Web API 的「應用程式」許可權。 我們已看到[公開應用程式許可權（應用程式角色）](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles) ，您的 API 會公開這類許可權（例如，`access_as_application` 應用程式角色）。
您現在必須讓 Api 驗證它所收到的權杖包含 `roles` 宣告，而且此宣告具有預期的值。 執行這項驗證的程式碼類似于驗證委派許可權的程式碼，不同的是，您的控制器動作會測試 `roles`，而不是測試 `scopes`：

```csharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

`ValidateAppRole()` 的方法可能如下所示：

```csharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage 
        { StatusCode = HttpStatusCode.Unauthorized, 
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found" 
        });
    }
}
}
```

這次，程式碼片段適用于 ASP.NET。 針對 ASP.NET Core，只需以 `HttpContext.User`取代 `ClaimsPrincipal.Current`，並將 `"roles"` 宣告名稱取代為 `"http://schemas.microsoft.com/identity/claims/roles"`。 （另請參閱本文稍早的程式碼片段）。

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>如果只應由 daemon 應用程式呼叫 Web API，即接受僅限應用程式權杖

`roles` 宣告也會用於使用者指派模式中的使用者。 （請參閱[如何：在應用程式中新增應用程式角色，並在權杖中接收它們](howto-add-app-roles-in-azure-ad-apps.md)）。因此，只要將角色指派給這兩個角色，就能讓應用程式以使用者身分登入，以及另一種方式。 我們建議您為使用者和應用程式宣告不同的角色，以避免發生混淆。

如果您只想要允許 daemon 應用程式呼叫您的 Web API，請新增條件，當您驗證應用程式角色時，該權杖就是僅限應用程式的權杖：

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

檢查反向條件只會允許登入使用者的應用程式呼叫您的 API。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移至生產環境](scenario-protected-web-api-production.md)
