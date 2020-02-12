---
title: 從行動應用程式呼叫 Web API |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何建立會呼叫 web Api 的行動應用程式。 （呼叫 Web API）。
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
ms.openlocfilehash: f41c9a0e4754c60fd248e540a81e2afa833d655b
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132412"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>從行動應用程式呼叫 Web API

當您的應用程式登入使用者並接收權杖之後，Microsoft 驗證程式庫（MSAL）會公開使用者的相關資訊、使用者的環境，以及已發行的權杖。 您的應用程式可以使用這些值來呼叫 Web API 或向使用者顯示歡迎訊息。

在本文中，我們會先探討 MSAL 結果。 然後，我們將探討如何使用 `AuthenticationResult` 或 `result` 中的存取權杖來呼叫受保護的 Web API。

## <a name="msal-result"></a>MSAL 結果
MSAL 提供下列值： 

- `AccessToken` 會在 HTTP 持有人要求中呼叫受保護的 web Api。
- `IdToken` 包含有關已登入使用者的實用資訊。 此資訊包括使用者的名稱、主租使用者，以及儲存體的唯一識別碼。
- `ExpiresOn` 是權杖的到期時間。 MSAL 會處理應用程式的自動重新整理。
- `TenantId` 是使用者已登入的租使用者識別碼。 對於 Azure Active Directory （Azure AD） B2B 中的來賓使用者，此值會識別使用者已登入的租使用者。 此值不會識別使用者的主租使用者。  
- `Scopes` 表示已授與您的權杖的範圍。 授與的範圍可能是您所要求範圍的子集。

MSAL 也會提供 `Account` 值的抽象概念。 `Account` 值，代表目前使用者的登入帳戶：

- `HomeAccountIdentifier` 識別使用者的主租使用者。
- `UserName` 是使用者慣用的使用者名稱。 Azure AD B2C 使用者的此值可能是空的。
- `AccountIdentifier` 識別已登入的使用者。 在大部分情況下，此值與 `HomeAccountIdentifier` 值相同，除非使用者是另一個租使用者中的來賓。

## <a name="call-an-api"></a>呼叫 API

取得存取權杖之後，您可以呼叫 Web API。 您的應用程式會使用權杖來建立 HTTP 要求，然後執行要求。

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

取得權杖的方法會傳回 `MSALResult` 物件。 `MSALResult` 會公開 `accessToken` 屬性。 您可以使用 `accessToken` 來呼叫 Web API。 請先將此屬性新增至 HTTP 授權標頭，然後再呼叫以存取受保護的 Web API。

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

## <a name="make-several-api-requests"></a>建立數個 API 要求

如果您需要呼叫相同的 API 數次，或如果您需要呼叫多個 Api，請在建立應用程式時考慮下列主題：

- **增量同意**： Microsoft 身分識別平臺可讓應用程式在需要許可權而不是在啟動時取得使用者同意。 每次您的應用程式準備呼叫 API 時，它只會要求所需的範圍。

- **條件式存取**：當您進行數個 API 要求時，在某些情況下，您可能必須符合其他條件式存取需求。 如果第一個要求沒有條件式存取原則，而您的應用程式嘗試以無訊息模式存取需要條件存取的新 API，則需求可能會以這種方式增加。 若要處理此問題，請務必攔截無訊息要求的錯誤，並準備好進行互動式要求。  如需詳細資訊，請參閱[條件式存取的指引](conditional-access-dev-guide.md)。

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>使用累加同意和條件式存取來呼叫數個 Api

如果您需要為相同的使用者呼叫數個 Api，在您取得使用者的權杖之後，您可以藉由後續呼叫 `AcquireTokenSilent` 來取得權杖，以避免重複要求使用者提供認證：

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

下列情況需要互動：

- 使用者同意第一個 API，但現在需要同意更多的範圍。 在此情況下，您會使用累加式同意。
- 第一個 API 不需要多重要素驗證，但下一個 API 會執行。

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
> [移至生產環境](scenario-mobile-production.md)
