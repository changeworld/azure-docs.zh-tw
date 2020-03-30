---
title: 從移動應用調用 Web API |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何構建調用 Web API 的移動應用。 （調用 Web API。
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.openlocfilehash: bd848fa6f74f049f97956ef1736ac2b08f3a6148
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160146"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>從移動應用調用 Web API

應用在使用者中簽名並接收權杖後，Microsoft 身份驗證庫 （MSAL） 會公開有關使用者、使用者環境和已頒發的權杖的資訊。 你的應用可以使用這些值調用 Web API 或向使用者顯示歡迎消息。

在本文中，我們將首先查看 MSAL 結果。 然後，我們將瞭解如何使用來自`AuthenticationResult`或`result`調用受保護 Web API 的訪問權杖。

## <a name="msal-result"></a>MSAL 結果
MSAL 提供以下值： 

- `AccessToken`在 HTTP 承載請求中調用受保護的 Web API。
- `IdToken`包含有關登錄使用者的有用資訊。 此資訊包括使用者名、主租戶和存儲的唯一識別碼。
- `ExpiresOn`是權杖的過期時間。 MSAL 處理應用的自動刷新。
- `TenantId`是使用者登錄的租戶的識別碼。 對於 Azure 活動目錄 （Azure AD） B2B 中的來賓使用者，此值標識使用者登錄的租戶。 該值不標識使用者的家庭租戶。  
- `Scopes`指示使用權杖授予的範圍。 授予的範圍可能是您請求的範圍的子集。

MSAL 還為`Account`值提供了抽象。 值`Account`表示當前使用者的登錄帳戶：

- `HomeAccountIdentifier`標識使用者的家庭租戶。
- `UserName`是使用者的首選使用者名。 對於 Azure AD B2C 使用者，此值可能為空。
- `AccountIdentifier`標識登錄使用者。 在大多數情況下，此值與值相同，`HomeAccountIdentifier`除非使用者是另一個租戶中的來賓。

## <a name="call-an-api"></a>調用 API

獲得訪問權杖後，可以調用 Web API。 你的應用將使用權杖生成 HTTP 要求，然後運行該請求。

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="msal-for-ios-and-macos"></a>適用於 iOS 和 macOS 的 MSAL

獲取權杖的方法返回物件`MSALResult`。 `MSALResult`公開`accessToken`屬性。 您可以使用`accessToken`調用 Web API。 在調用訪問受保護的 Web API 之前，將此屬性添加到 HTTP 授權標頭。

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };
        
NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="make-several-api-requests"></a>發出多個 API 請求

如果需要多次調用同一 API，或者需要調用多個 API，則在構建應用時請考慮以下主題：

- **增量同意**：Microsoft 標識平臺允許應用在需要許可權時獲得使用者同意，而不是在開始時獲得擁有權限。 每次應用準備好調用 API 時，它應僅請求所需的作用域。

- **條件訪問**：當您發出多個 API 請求時，在某些情況下，您可能需要滿足其他條件訪問要求。 如果第一個請求沒有條件訪問策略，並且你的應用嘗試靜默訪問需要條件訪問的新 API，則需求可能會以這種方式增加。 要處理此問題，請確保捕獲來自靜默請求的錯誤，並準備好發出互動式請求。  有關詳細資訊，請參閱[條件訪問指南](../azuread-dev/conditional-access-dev-guide.md)。

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>使用增量同意和條件訪問調用多個 API

如果需要為同一使用者調用多個 API，在為使用者獲取權杖後，可以通過隨後調用`AcquireTokenSilent`以獲取權杖來避免重複向使用者請求憑據：

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

在：

- 使用者同意第一個 API，但現在需要同意更多作用域。 在這種情況下，您使用增量同意。
- 第一個 API 不需要多重要素驗證，但下一個 API 需要多重要素驗證。

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移動至生產環境](scenario-mobile-production.md)
