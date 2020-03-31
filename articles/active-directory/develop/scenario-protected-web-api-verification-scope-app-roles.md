---
title: 驗證受保護的 Web API 的範圍和應用角色 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何構建受保護的 Web API 並配置應用程式的代碼。
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
ms.openlocfilehash: 816a9620a3486b534f9293084b7c4f5b4f748033
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768125"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>受保護的 Web API：驗證作用域和應用角色

本文介紹如何向 Web API 添加授權。 此保護可確保僅由以下者調用 API：

- 代表具有正確作用域的使用者的應用程式。
- 具有正確應用程式角色的守護進程應用。

> [!NOTE]
> 本文的程式碼片段是從以下示例中提取的，這些示例功能齊全：
>
> - [ASP.NET GitHub 上的核心 Web API 增量教程](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37)
> - [ASP.NET Web API 示例](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

要保護ASP.NET或ASP.NET核心 Web API，必須將`[Authorize]`該屬性添加到以下專案之一：

- 如果希望保護所有控制器操作，則控制器本身
- API 的單個控制器操作

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

但是這種保護是不夠的。 它僅保證ASP.NET，ASP.NET核心驗證權杖。 API 需要驗證用於調用 API 的權杖是否與預期聲明一起請求。 這些索賠尤其需要核實：

- 代表使用者調用 API 時*的範圍*。
- 可以從守護進程應用調用 API 時 *，應用角色*。

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>驗證代表使用者調用的 API 中的範圍

如果用戶端應用代表使用者調用 API，則 API 需要請求具有 API 特定作用域的承載權杖。 有關詳細資訊，請參閱[代碼配置 |承載權杖](scenario-protected-web-api-app-configuration.md#bearer-token).

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

該方法`VerifyUserHasAnyAcceptedScope`執行以下步驟：

- 驗證有名為`http://schemas.microsoft.com/identity/claims/scope`或`scp`的聲明。
- 驗證聲明具有包含 API 預期範圍的值。

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

前面的[示例代碼](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47)用於ASP.NET核心。 對於ASP.NET，只需替換為`HttpContext.User``ClaimsPrincipal.Current`，並將聲明類型`"http://schemas.microsoft.com/identity/claims/scope"`替換為`"scp"`。 本文稍後將查看程式碼片段。

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>驗證守護進程應用調用的 API 中的應用角色

如果 Web API 由[守護進程應用](scenario-daemon-overview.md)調用，則該應用應需要 Web API 的應用程式許可權。 如[公開應用程式許可權（應用角色）所示](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles)，API 公開此類許可權。 應用角色就是一`access_as_application`個示例。

現在，您需要讓 API 驗證它收到的權杖是否包含`roles`聲明，以及此聲明是否具有預期值。 驗證代碼類似于驗證委派許可權的代碼，只不過控制器操作測試角色而不是作用域：

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

該方法`ValidateAppRole`可以是這樣的：

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

這一次，程式碼片段用於ASP.NET。 對於ASP.NET核心，只需替換為`ClaimsPrincipal.Current``HttpContext.User`，並將`"roles"`聲明名稱替換為`"http://schemas.microsoft.com/identity/claims/roles"`。 另請參閱本文前面的程式碼片段。

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>如果僅由守護程式應用調用 Web API，則接受僅應用權杖

使用者還可以在使用者分配模式中使用角色聲明，如["如何：在應用程式中添加應用角色並在權杖中接收它們](howto-add-app-roles-in-azure-ad-apps.md)"。 如果角色可以分配給這兩個角色，則檢查角色將允許應用以使用者身份登錄，使用者可以以應用身份登錄。 我們建議您為使用者和應用聲明不同的角色，以防止這種混淆。

如果只想守護進程應用調用 Web API，請添加驗證應用角色時權杖為僅應用權杖的條件。

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

檢查相反條件只允許登錄使用者的應用調用 API。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移動至生產環境](scenario-protected-web-api-production.md)
