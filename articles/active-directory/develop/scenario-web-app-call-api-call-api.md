---
title: 從 web 應用程式呼叫 web api-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立呼叫 web Api 的 web 應用程式（呼叫受保護的 Web API）
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 12802ab6dcfbbe5a1c5576ab672ead864dd0b4ae
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559870"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>呼叫 web Api 的 web 應用程式：呼叫 Web API

現在您已有權杖，您可以呼叫受保護的 Web API。

## <a name="call-a-protected-web-api"></a>呼叫受保護的 Web API

呼叫受保護的 Web API 取決於您選擇的語言和架構：

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

以下是的動作簡化的程式碼`HomeController`。 此程式碼會取得權杖以呼叫 Microsoft Graph。 已新增程式碼，以示範如何呼叫 Microsoft Graph 做為 REST API。 Appsettings 中提供了 Microsoft Graph API 的 URL，並會讀取名為`webOptions`的變數：

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    ...
  },
  ...
  "GraphApiUrl": "https://graph.microsoft.com"
}
```

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
> 您可以使用相同的原則來呼叫任何 Web API。
>
> 大部分的 Azure web Api 都會提供可簡化呼叫 API 的 SDK。 這也適用于 Microsoft Graph。 在下一篇文章中，您將瞭解哪裡可以找到說明 API 使用的教學課程。

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
> [移動至生產環境](scenario-web-app-call-api-production.md)
