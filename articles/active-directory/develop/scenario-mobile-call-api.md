---
title: 從行動應用程式呼叫 Web API | Azure
titleSuffix: Microsoft identity platform
description: 了解如何建置會呼叫 Web API 的行動應用程式。 (呼叫 Web API)。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: d7cad0592c5c4c0487f582ce5405c275b94b7bd0
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444022"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>從行動應用程式呼叫 Web API

當您的應用程式登入使用者並接收權杖之後，Microsoft 驗證程式庫 (MSAL) 會公開使用者、使用者環境以及所發行權杖的相關資訊。 您的應用程式可以使用這些值來呼叫 Web API 或向使用者顯示歡迎訊息。

在本文中，我們會先探討 MSAL 結果。 然後，我們會探討如何使用 `AuthenticationResult` 或 `result` 中的存取權杖來呼叫受保護的 Web API。

## <a name="msal-result"></a>MSAL 結果
MSAL 會提供下列值： 

- `AccessToken` 會在 HTTP 持有人要求中呼叫受保護的 Web API。
- `IdToken` 包含有關已登入使用者的實用資訊。 此資訊包括使用者的名稱、主租用戶，以及儲存體的唯一識別碼。
- `ExpiresOn` 是權杖的到期時間。 MSAL 會處理應用程式的自動重新整理。
- `TenantId` 是使用者登入所在租用戶的識別碼。 對於 Azure Active Directory (Azure AD) B2B 中的來賓使用者，此值可識別使用者登入所在的租用戶。 此值無法識別使用者的主租用戶。  
- `Scopes` 會指出已授與權杖的範圍。 授與的範圍可能是您所要求範圍的子集。

MSAL 也會提供 `Account` 值的摘要。 `Account` 值代表目前使用者的已登入帳戶：

- `HomeAccountIdentifier` 可識別使用者的主租用戶。
- `UserName` 是使用者慣用的使用者名稱。 Azure AD B2C 使用者的這個值可能是空的。
- `AccountIdentifier` 可識別已登入的使用者。 在大部分情況下，此值與 `HomeAccountIdentifier` 值相同，除非使用者是另一個租用戶中的來賓。

## <a name="call-an-api"></a>呼叫 API

取得存取權杖之後，您可以呼叫 Web API。 您的應用程式會使用權杖來建置 HTTP 要求，然後執行要求。

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

用來取得權杖的方法會傳回 `MSALResult` 物件。 `MSALResult` 會公開 `accessToken` 屬性。 您可以使用 `accessToken` 來呼叫 Web API。 請先將此屬性新增至 HTTP 授權標頭，然後才進行呼叫以存取受保護的 Web API。

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

## <a name="make-several-api-requests"></a>提出數個 API 要求

如果您需要呼叫相同 API 數次，或如果您需要呼叫多個 API，請在建置應用程式時思考下列主題：

- **增量同意** ：Microsoft 身分識別平台可讓應用程式在需要權限時才取得使用者同意，而非全都在一開始就取得。 每次應用程式準備呼叫 API 時，就只應要求所需的範圍。

- **條件式存取** ：當您提出數個 API 要求時，在某些情況下，您可能必須符合其他條件式存取需求。 如果第一個要求沒有條件式存取原則，而您的應用程式嘗試以無訊息模式存取需要條件存取的新 API，則需求可能會以這種方式增加。 若要處理此問題，請務必擷取無訊息要求中的錯誤，並準備好進行互動式要求。  如需詳細資訊，請參閱[條件式存取的指導方針](../azuread-dev/conditional-access-dev-guide.md)。

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>使用增量同意和條件式存取來呼叫數個 API

如果您需要為相同的使用者呼叫數個 API，在您取得使用者的權杖之後，您可以藉由後續呼叫 `AcquireTokenSilent` 來取得權杖，以避免重複要求使用者提供認證：

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

下列情況時需要互動：

- 使用者已同意第一個 API，但現在需要同意更多範圍。 在此情況下，您可以使用增量同意。
- 第一個 API 不需要[多重要素驗證](../authentication/concept-mfa-howitworks.md)，但下一個 API 需要。

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

移至本案例的下一篇文章， [移至生產環境](scenario-mobile-production.md)。
