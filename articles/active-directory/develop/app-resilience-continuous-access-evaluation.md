---
title: 如何在您的應用程式中使用持續存取評估啟用的 Api |蔚藍
titleSuffix: Microsoft identity platform
description: 如何藉由新增持續存取評估的支援來提高應用程式安全性和恢復功能，並啟用可根據重大事件和原則評估來撤銷的長期存取權杖。
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2020
ms.author: nichola
ms.reviewer: ''
ms.openlocfilehash: 975c92256ea0993badde0faf840a939f42901059
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95753692"
---
# <a name="how-to-use-continuous-access-evaluation-enabled-apis-in-your-applications"></a>如何在您的應用程式中使用持續存取評估啟用的 Api

[持續存取評估](../conditional-access/concept-continuous-access-evaluation.md) (CAE) 是一種新興的產業標準，可讓您根據 [重大事件](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation) 和 [原則評估](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview) 來撤銷存取權杖，而不需依賴根據存留期的權杖到期時間。 針對某些資源 Api，因為風險和原則會即時評估，這可能會增加最多28小時的權杖存留期。 這些長期的權杖將會由 Microsoft 驗證程式庫 (MSAL) 主動重新整理，從而提高應用程式的復原能力。

本文說明如何在您的應用程式中使用啟用 CAE 的 Api。

## <a name="implementation-considerations"></a>實作考量

若要使用持續存取評估，您的應用程式和其所存取的資源 API 都必須啟用 CAE。 不過，準備您的程式碼以使用啟用 CAE 的資源，並不會讓您無法使用未啟用 CAE 的 Api。 

如果資源 API 會執行 CAE，而您的應用程式宣告它可以處理 CAE，則您的應用程式會取得該資源的 CAE 權杖。 基於這個理由，如果您將應用程式 CAE 宣告為就緒，您的應用程式必須針對所有接受 Microsoft 身分識別存取權杖的資源 Api，處理 CAE 宣告挑戰。 如果您未在這些 API 呼叫中處理 CAE 回應，則您的應用程式可能會在迴圈中結束，並以仍在權杖的傳回存留期內的權杖重試 API 呼叫，但由於 CAE 而遭到撤銷。 

## <a name="the-code"></a>代碼

第一個步驟是新增程式碼，以處理因 CAE 而拒絕呼叫的資源 API 回應。 使用 CAE 時，Api 會在存取權杖已撤銷或 API 偵測到所使用的 IP 位址有所變更時，傳回401狀態和 WWW-Authenticate 標頭。 WWW-Authenticate 標頭包含的宣告挑戰，可讓應用程式用來取得新的存取權杖。

例如：

```console
HTTP 401; Unauthorized
WWW-Authenticate=Bearer
 authorization_uri="https://login.windows.net/common/oauth2/authorize",
 error="insufficient_claims",
 claims="eyJhY2Nlc3NfdG9rZW4iOnsibmJmIjp7ImVzc2VudGlhbCI6dHJ1ZSwgInZhbHVlIjoiMTYwNDEwNjY1MSJ9fX0="
```

您的應用程式會檢查：

- 傳回401狀態的 API 呼叫
- WWW-Authenticate 標頭是否存在，其包含：
  - 值為 "insufficient_claims" 的 "error" 參數
  - "宣告" 參數

當符合這些條件時，應用程式可以將宣告挑戰解壓縮和解碼。

```csharp
if (APIresponse.IsSuccessStatusCode)
{
    // . . .
}
else
{
    if (APIresponse.StatusCode == System.Net.HttpStatusCode.Unauthorized
        && APIresponse.Headers.WwwAuthenticate.Any())
    {
        AuthenticationHeaderValue bearer = APIresponse.Headers.WwwAuthenticate.First
            (v => v.Scheme == "Bearer");
        IEnumerable<string> parameters = bearer.Parameter.Split(',').Select(v => v.Trim()).ToList();
        var error = GetParameter(parameters, "error");

        if (null != error && "insufficient_claims" == error)
        {
            var claimChallengeParameter = GetParameter(parameters, "claims");
            if (null != claimChallengeParameter)
            {
                var claimChallengebase64Bytes = System.Convert.FromBase64String(claimChallengeParameter);
                var claimChallenge = System.Text.Encoding.UTF8.GetString(claimChallengebase64Bytes);
                var newAccessToken = await GetAccessTokenWithClaimChallenge(scopes, claimChallenge);
```

然後，您的應用程式會使用宣告挑戰來取得資源的新存取權杖。

```csharp
try
{
    authResult = await _clientApp.AcquireTokenSilent(scopes, firstAccount)
        .WithClaims(claimChallenge)
        .ExecuteAsync()
        .ConfigureAwait(false);
}
catch (MsalUiRequiredException)
{
    try
    {
        authResult = await _clientApp.AcquireTokenInteractive(scopes)
            .WithClaims(claimChallenge)
            .WithAccount(firstAccount)
            .ExecuteAsync()
            .ConfigureAwait(false);
    }
    // . . .
```

當您的應用程式準備好處理已啟用 CAE 的資源所傳回的宣告挑戰之後，您就可以告訴 Microsoft 身分識別您的應用程式已 CAE 就緒。 若要在您的 MSAL 應用程式中這麼做，請使用 "cp1" 的用戶端功能來建立您的公用用戶端。

```csharp
_clientApp = PublicClientApplicationBuilder.Create(App.ClientId)
    .WithDefaultRedirectUri()
    .WithAuthority(authority)
    .WithClientCapabilities(new [] {"cp1"})
    .Build();
```

您可以藉由將使用者登入應用程式，然後使用 Azure 入口網站撤銷使用者的會話，來測試您的應用程式。 下次應用程式呼叫啟用 CAE 的 API 時，系統會要求使用者重新驗證。

## <a name="next-steps"></a>後續步驟

若要深入瞭解，請參閱 [持續存取評估](/conditional-access/concept-continuous-access-evaluation.md)。
