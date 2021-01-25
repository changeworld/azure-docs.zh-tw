---
title: 呼叫 web Api 的 web API |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何建立可呼叫 web Api 的 web API。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 2c69cea7055476bcc4c4a28cea0a0cf8c8f66f88
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753459"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>呼叫 web api 的 web API：呼叫 API

擁有權杖之後，您可以呼叫受保護的 web API。 您通常會從 web API 的控制器或頁面呼叫下游 Api。

## <a name="controller-code"></a>控制器程式碼

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

當您使用的是 *web.config* 時，有三種使用案例：

- [選項1：使用 Microsoft Graph SDK 呼叫 Microsoft Graph](#option-1-call-microsoft-graph-with-the-sdk)
- [選項2：使用 helper 類別呼叫下游 web API](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [選項3：不使用 helper 類別來呼叫下游 web API](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>選項1：使用 SDK 呼叫 Microsoft Graph

在此案例中，您已依照 `.AddMicrosoftGraph()` 程式 [代碼](scenario-web-api-call-api-app-configuration.md#option-1-call-microsoft-graph)設定中的指定在 *Startup.cs* 中新增，而且您可以直接在控制器或頁面的函式中插入，以用於 `GraphServiceClient` 動作。 下列範例 Razor 頁面會顯示已登入使用者的相片。

```CSharp
 [Authorize]
 [AuthorizeForScopes(Scopes = new[] { "user.read" })]
 public class IndexModel : PageModel
 {
     private readonly GraphServiceClient _graphServiceClient;

     public IndexModel(GraphServiceClient graphServiceClient)
     {
         _graphServiceClient = graphServiceClient;
     }

     public async Task OnGet()
     {
         var user = await _graphServiceClient.Me.Request().GetAsync();
         try
         {
             using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
             {
                 byte[] photoByte = ((MemoryStream)photoStream).ToArray();
                 ViewData["photo"] = Convert.ToBase64String(photoByte);
             }
             ViewData["name"] = user.DisplayName;
         }
         catch (Exception)
         {
             ViewData["photo"] = null;
         }
     }
 }
```

#### <a name="option-2-call-a-downstream-web-api-with-the-helper-class"></a>選項2：使用 helper 類別呼叫下游 web API

在此案例中，您已在 `.AddDownstreamWebApi()` 程式 [代碼](scenario-web-api-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph)設定中指定的 *Startup.cs* 中新增，而且您可以直接在控制器或頁面的函式中插入 `IDownstreamWebApi` 服務，並在動作中使用它：

```CSharp
 [Authorize]
 [AuthorizeForScopes(ScopeKeySection = "TodoList:Scopes")]
 public class TodoListController : Controller
 {
     private IDownstreamWebApi _downstreamWebApi;
     private const string ServiceName = "TodoList";

     public TodoListController(IDownstreamWebApi downstreamWebApi)
     {
         _downstreamWebApi = downstreamWebApi;
     }

     public async Task<ActionResult> Details(int id)
     {
         var value = await _downstreamWebApi.CallWebApiForUserAsync(
             ServiceName,
             options =>
             {
                 options.RelativePath = $"me";
             });
         return View(value);
     }
```

`CallWebApiForUserAsync`方法也有強型別的泛型覆寫，可讓您直接接收物件。 例如，下列方法 `Todo` 會收到實例，這是 WEB API 所傳回的 JSON 強型別標記法。

```CSharp
 // GET: TodoList/Details/5
 public async Task<ActionResult> Details(int id)
 {
     var value = await _downstreamWebApi.CallWebApiForUserAsync<object, Todo>(
         ServiceName,
         null,
         options =>
         {
             options.HttpMethod = HttpMethod.Get;
             options.RelativePath = $"api/todolist/{id}";
         });
     return View(value);
 }
```

#### <a name="option-3-call-a-downstream-web-api-without-the-helper-class"></a>選項3：不使用 helper 類別來呼叫下游 web API

如果您已決定使用服務手動取得權杖 `ITokenAcquisition` ，您現在必須使用權杖。 在此情況下，下列程式碼會繼續 web API 中所顯示的範例 [程式碼，以呼叫 Web api：取得應用程式的權杖](scenario-web-api-call-api-acquire-token.md)。 程式碼會在 API 控制器的動作中呼叫。 它會呼叫名為 *todolist* 的下游 API。

 取得權杖之後，請使用它作為持有人權杖來呼叫下游 API。

```csharp
 private async Task CallTodoListService(string accessToken)
 {
  // After the token has been returned by Microsoft.Identity.Web, add it to the HTTP authorization header before making the call to access the todolist service.
 _httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

 // Call the todolist service.
 HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
 // ...
 }
 ```

# <a name="java"></a>[Java](#tab/java)

下列程式碼會繼續執行 web API 中所顯示的範例程式碼，該程式碼會 [呼叫 Web api：取得應用程式的權杖](scenario-web-api-call-api-acquire-token.md)。 程式碼會在 API 控制器的動作中呼叫。 它會呼叫下游 API MS Graph。

取得權杖之後，請使用它作為持有人權杖來呼叫下游 API。

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
使用 MSAL Python 示範此流程的範例還無法使用。

---

## <a name="next-steps"></a>後續步驟

移至本案例的下一篇文章， [移至生產環境](scenario-web-api-call-api-production.md)。
