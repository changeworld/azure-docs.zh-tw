---
title: 驗證受保護 Web API 的範圍和應用程式角色 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何建立受保護的 Web API，並設定應用程式的程式碼。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c3cf8bfd2810e9c26a6f65c50c1a22baeec892af
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855465"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>受保護的 Web API：驗證範圍和應用程式角色

本文說明如何在您的 Web API 中新增授權。 這項保護可確保 API 僅由下列項目呼叫：

- 代表具有適當範圍之使用者的應用程式。
- 具有適當應用程式角色的精靈應用程式。

> [!NOTE]
> 本文中的程式碼片段會從 GitHub 上的下列程式碼範例中解壓縮：
>
> - [ASP.NET Core web API 增量教學課程](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs)
> - [ASP.NET Web API 範例](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/master/TodoListService/Controllers/TodoListController.cs)

若要保護 ASP.NET 或 ASP.NET Core Web API，您必須將 `[Authorize]` 屬性新增至下列其中一個項目：

- 如果您希望所有控制器動作都受到保護，則為控制器本身
- 您 API 的個別控制器動作

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

但此種保護並不足夠。 這只能保證 ASP.NET 和 ASP.NET Core 會驗證權杖。 您的 API 必須驗證用來呼叫 API 的權杖是否以預期的宣告進行要求。 下列宣告特別需要驗證：

- 如果代表使用者呼叫 API，則需驗證「範圍」。
- 如果可從精靈程式應用程式呼叫 API，則需驗證「應用程式角色」。

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>在代表使用者呼叫的 API 中驗證範圍

如果用戶端應用程式代表使用者呼叫您的 API，則 API 必須要求具有 API 特定範圍的持有人權杖。 如需詳細資訊，請參閱[程式碼設 | 持有人權杖](scenario-protected-web-api-app-configuration.md#bearer-token)。

### <a name="net-core"></a>.NET Core

#### <a name="verify-the-scopes-on-each-controller-action"></a>確認每個控制器動作的範圍

```csharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
         HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);
        // Do the work and return the result.
        // ...
    }
...
}
```

`VerifyUserHasAnyAcceptedScope` 方法會執行類似下列步驟的動作：

- 確認有名為 `http://schemas.microsoft.com/identity/claims/scope` 或 `scp` 的宣告。
- 確認宣告的值包含 API 所預期的範圍。


#### <a name="verify-the-scopes-more-globally"></a>更全域地驗證範圍

為您的 web API 定義細微範圍，以及驗證每個控制器動作中的範圍是建議的方法。 不過，您也可以使用 ASP.NET Core 來驗證應用程式層級或控制器層級的範圍。 如需詳細資訊，請參閱 ASP.NET core 檔中的宣告 [型授權](/aspnet/core/security/authorization/claims) 。

### <a name="net-mvc"></a>.NET MVC

對於 ASP.NET，只要以 `ClaimsPrincipal.Current` 取代 `HttpContext.User`，並以 `"scp"` 取代宣告類型 `"http://schemas.microsoft.com/identity/claims/scope"`。 另請參閱本文稍後的程式碼片段。

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>在精靈應用程式所呼叫的 API 中驗證應用程式角色

如果您的 Web API 是由[精靈應用程式](scenario-daemon-overview.md)呼叫，該應用程式應該需要 Web API 的應用程式權限。 如[公開應用程式權限 (應用程式角色)](./scenario-protected-web-api-app-registration.md#exposing-application-permissions-app-roles) 所示，您的 API 會公開這類權限。 其中一個範例是 `access_as_application` 應用程式角色。

您現在必須讓 API 確認其所收到的權杖包含 `roles` 宣告，而且此宣告具有預期的值。 除了控制器動作會測試角色而非範圍以外，驗證碼類似於可驗證委派權限的程式碼：

### <a name="aspnet-core"></a>ASP.NET Core

```csharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        HttpContext.ValidateAppRole("access_as_application");
        ...
    }
```

`ValidateAppRole`方法會定義在[RolesRequiredHttpCoNtextExtensions.cs](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/Resource/RolesRequiredHttpContextExtensions.cs#L28)中的 web.config 中。

### <a name="aspnet-mvc"></a>ASP.NET MVC

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

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>如果只能由精靈應用程式呼叫 Web API，則接受僅限應用程式權杖

使用者也可以使用使用者指派模式中的角色宣告，詳情請參閱[如何：在您的應用程式中新增應用程式角色，並且在權杖中接收這些角色](howto-add-app-roles-in-azure-ad-apps.md)。 如果角色可指派給兩者，檢查角色將可讓應用程式以使用者身分登入，以及讓使用者以應用程式身分登入。 我們建議您為使用者和應用程式宣告不同的角色，以避免發生混淆。

如果您只希望精靈應用程式呼叫您的 Web API，請在驗證應用程式角色時，新增權杖為僅限應用程式權杖這個條件。

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

檢查反向條件，只能允許以使用者身分登入的應用程式呼叫您的 API。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移至生產環境](scenario-protected-web-api-production.md)