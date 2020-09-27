---
title: 從 web 應用程式呼叫 web api |蔚藍
titleSuffix: Microsoft identity platform
description: '瞭解如何建立 web 應用程式，以呼叫受保護的 web API (呼叫 web Api) '
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 815b1789c54d1ce505c16dc89e199d451ae9a588
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396122"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>呼叫 web api 的 web 應用程式：呼叫 web API

現在您已有權杖，您可以呼叫受保護的 web API。 您通常會從 web 應用程式的控制器或頁面呼叫下游 API。

## <a name="call-a-protected-web-api"></a>呼叫受保護的 web API

呼叫受保護的 web API 取決於您選擇的語言和架構：

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

當您使用的是 *web.config*時，有三種使用方法可供您呼叫 API：

- [選項1：使用 Microsoft Graph SDK 呼叫 Microsoft Graph](#option-1-call-microsoft-graph-with-the-sdk)
- [選項2：使用 helper 類別呼叫下游 web API](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [選項3：不使用 helper 類別來呼叫下游 web API](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>選項1：使用 SDK 呼叫 Microsoft Graph

您想要呼叫 Microsoft Graph。 在此案例中，您已依照 `AddMicrosoftGraph` 程式[代碼](scenario-web-app-call-api-app-configuration.md#option-1-call-microsoft-graph)設定中的指定在*Startup.cs*中新增，而且您可以直接在控制器或頁面的函式中插入，以用於 `GraphServiceClient` 動作。 下列範例 Razor 頁面會顯示已登入使用者的相片。

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

您想要呼叫 Microsoft Graph 以外的 web API。 在此情況下，您已在程式 `AddDownstreamWebApi` [代碼](scenario-web-app-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph)設定中指定的*Startup.cs*中新增，而且您可以直接在控制器或頁面的函式中插入服務， `IDownstreamWebApi` 並在動作中使用它：

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
}
```

`CallWebApiForUserAsync`也有強型別的泛型覆寫，可讓您直接接收物件。 例如，下列方法 `Todo` 會接收實例，這是 WEB API 所傳回的 JSON 強型別標記法。

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

您已決定使用服務手動取得權杖 `ITokenAcquisition` ，而您現在需要使用權杖。 在此情況下，下列程式碼會繼續在 web 應用程式中顯示的範例 [程式碼，以呼叫 Web api：取得應用程式的權杖](scenario-web-app-call-api-acquire-token.md)。 程式碼會在 web 應用程式控制器的動作中呼叫。

取得權杖之後，請使用它作為持有人權杖來呼叫下游 API，在此案例中 Microsoft Graph。

 ```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

  var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

  if (response.StatusCode == HttpStatusCode.OK)
  {
   var content = await response.Content.ReadAsStringAsync();

   dynamic me = JsonConvert.DeserializeObject(content);
   ViewData["Me"] = me;
  }

  return View();
}
```
> [!NOTE]
> 您可以使用相同的原則來呼叫任何 web API。
>
> 大部分的 Azure web Api 都會提供 SDK，以簡化 Microsoft Graph 的情況下呼叫 API 的方式。 比方說，您可以 [建立一個 web 應用程式，以使用 Azure AD 來授與 Blob 儲存體的存取權](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=dotnet) ，並使用 Azure 儲存體 SDK 的 web 應用程式範例。

# <a name="java"></a>[Java](#tab/java)

```Java
private String getUserInfoFromGraph(String accessToken) throws Exception {
    // Microsoft Graph user endpoint
    URL url = new URL("https://graph.microsoft.com/v1.0/me");

    HttpURLConnection conn = (HttpURLConnection) url.openConnection();

    // Set the appropriate header fields in the request header.
    conn.setRequestProperty("Authorization", "Bearer " + accessToken);
    conn.setRequestProperty("Accept", "application/json");

    String response = HttpClientHelper.getResponseStringFromConn(conn);

    int responseCode = conn.getResponseCode();
    if(responseCode != HttpURLConnection.HTTP_OK) {
        throw new IOException(response);
    }

    JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
    return responseObject.toString();
}

```

# <a name="python"></a>[Python](#tab/python)

```Python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移至生產環境](scenario-web-app-call-api-production.md)
