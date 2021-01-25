---
title: 處理 MSAL.js 中的錯誤和例外狀況
titleSuffix: Microsoft identity platform
description: 瞭解如何處理 MSAL.js 應用程式中的錯誤和例外狀況、條件式存取宣告挑戰和重試。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, hahamil
ms.custom: aaddev
ms.openlocfilehash: 20d276aba2ee3260911748cbee0a16020270059a
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761344"
---
# <a name="handle-errors-and-exceptions-in-msaljs"></a>處理 MSAL.js 中的錯誤和例外狀況

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msaljs"></a>MSAL.js 中的錯誤處理

MSAL.js 會提供錯誤物件，以摘要和分類不同類型的常見錯誤。 它也會提供介面來存取錯誤的特定詳細資料，例如錯誤訊息來適當地處理它們。

### <a name="error-object"></a>Error 物件

```javascript
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```

藉由擴充錯誤類別，您將可存取下列屬性：
- `AuthError.message`:與 `errorMessage` 相同。
- `AuthError.stack`:擲回錯誤的堆疊追蹤。

### <a name="error-types"></a>錯誤類型

可用的錯誤類型如下：

- `AuthError`:MSAL.js 程式庫的基本錯誤類別，也可用於非預期的錯誤。

- `ClientAuthError`:表示用戶端驗證有問題的錯誤類別。 來自程式庫的錯誤大多為 ClientAuthError。 這些錯誤起因於在登入已在進行時呼叫登入方法或使用者取消登入等等的事件。

- `ClientConfigurationError`:錯誤類別，會在指定的使用者設定參數格式不正確或遺失時，於提出要求之前擴充擲回的 `ClientAuthError`。

- `ServerError`:錯誤類別，代表驗證伺服器所傳送的錯誤字串。 這些可能是要求格式或參數無效之類的錯誤，或導致伺服器無法對使用者進行驗證或授權的任何其他錯誤。

- `InteractionRequiredAuthError`:錯誤類別，擴充 `ServerError` 以代表需要互動式呼叫的伺服器錯誤。 如果使用者必須與伺服器互動以提供驗證/授權所需的認證或同意，`acquireTokenSilent` 就會擲回此錯誤。 錯誤碼包括 `"interaction_required"`、`"login_required"` 和 `"consent_required"`。

若要對使用重新導向方法 (`loginRedirect`、`acquireTokenRedirect`) 的驗證流程進行錯誤處理，您必須註冊在使用 `handleRedirectCallback()` 方法的重新導向之後依據作業成功與否呼叫的回呼，如下所示：

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

快顯體驗的方法 (`loginPopup`、`acquireTokenPopup`) 會傳回承諾，因此您可以使用承諾模式 (.then 和 .catch) 處理錯誤，如下所示：

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>需要互動的錯誤

當您嘗試使用非互動式方法來取得 `acquireTokenSilent` 之類的權杖，但 MSAL 無法以無訊息模式執行時，便會傳回錯誤。

可能的原因包括：

- 您必須登入
- 您必須同意
- 您需要通過多重要素驗證。

補救措施是呼叫互動式方法，例如 `acquireTokenPopup` 或 `acquireTokenRedirect`：

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

使用 MSAL.js 以無訊息模式取得權杖時 (使用 `acquireTokenSilent`)，若您嘗試存取的 API 需要[條件式存取宣告挑戰](../azuread-dev/conditional-access-dev-guide.md) (例如 MFA 原則)，則您的應用程式可能會收到錯誤。

處理此錯誤的模式，是發出在 MSAL.js 中取得權杖的互動式呼叫 (例如 `acquireTokenPopup` 或 `acquireTokenRedirect`)，如下列範例所示：

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // call API
}).catch(function(error) {
    if (error instanceof InteractionRequiredAuthError) {
    
        // extract, if exists, claims from error message
        if (error.ErrorMessage.claims) {
            accessTokenRequest.claimsRequest = JSON.stringify(error.ErrorMessage.claims);
        }
        
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // call API
        }).catch(function(error) {
            console.log(error);
        });
    }
});
```

以互動方式取得權杖時，系統會對使用者發出提示，讓他們有機會符合所需的條件式存取原則。

當您呼叫需要條件式存取的 API 時，您可能會在 API 發出的錯誤中收到宣告挑戰。 在此情況下，您可以將錯誤中傳回的宣告傳遞至 `AuthenticationParameters.ts` 類別的 `claimsRequest` 欄位，以滿足適當原則。 

如需詳細資訊，請參閱[要求其他宣告](active-directory-optional-claims.md)。


[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>後續步驟

請考慮 [在 MSAL.js中啟用記錄 ](msal-logging-js.md) ，以協助您診斷和偵測問題。
