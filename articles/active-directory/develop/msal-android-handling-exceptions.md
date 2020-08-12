---
title: MSAL Android)  (的錯誤和例外狀況 |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何處理 MSAL Android 應用程式中的錯誤和例外狀況、條件式存取和宣告挑戰。
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 08/07/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: c0b08a6c1a784216abe2bd562109dbb1586252c9
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88119805"
---
# <a name="handle-exceptions-and-errors-in-msal-for-android"></a>處理 MSAL for Android 中的例外狀況和錯誤

 (MSAL) 的 Microsoft 驗證程式庫中的例外，是為了協助應用程式開發人員針對其應用程式進行疑難排解。 例外狀況訊息不會當地語系化。

在處理例外狀況和錯誤時，您可以使用例外狀況類型本身和錯誤碼來區別例外狀況。  如需錯誤碼清單，請參閱[驗證與授權錯誤碼](reference-aadsts-error-codes.md)。

在登入體驗期間，您可能會遇到關於同意、條件式存取 (MFA、裝置管理、位置型限制)、權杖發行和兌換，以及使用者屬性的錯誤。


|錯誤類別 | 原因/錯誤字串| 如何處理 |
|-----------|------------|----------------|
|`MsalUiRequiredException`| <ul><li>`INVALID_GRANT`：用來兌換存取權杖的重新整理權杖無效、已過期或已撤銷。 這個例外狀況可能是因為密碼變更所導致。 </li><li>`NO_TOKENS_FOUND`：存取權杖不存在，而且找不到可兌換存取權杖的重新整理權杖。</li> <li>需要逐步執行<ul><li>MFA</li><li>遺漏宣告</li></ul></li><li>由條件式存取封鎖 (例如，需要[驗證代理](./brokered-auth.md)程式安裝) </li><li>`NO_ACCOUNT_FOUND`：快取中沒有可用於無訊息驗證的帳戶。</li></ul> |呼叫 `acquireToken()` 以提示使用者輸入其使用者名稱和密碼，並可能同意並執行多重要素驗證。|
|`MsalDeclinedScopeException`|<ul><li>`DECLINED_SCOPE`：使用者或伺服器尚未接受所有範圍。 如果要求的範圍不受支援、無法辨識，或不支援特定帳戶，伺服器可能會拒絕範圍。 </li></ul>| 開發人員應決定是否要繼續使用授與的範圍進行驗證，或是結束驗證程式。 僅針對授與的範圍重新提交取得權杖要求，並提供透過傳遞和呼叫授與許可權的提示選項 `silentParametersForGrantedScopes` `acquireTokenSilent` 。 |
|`MsalServiceException`|<ul><li>`INVALID_REQUEST`：此要求缺少必要參數、包含不正確參數、包含參數多次，或格式不正確。 </li><li>`SERVICE_NOT_AVAILABLE`：代表服務關閉時的500/503/506 錯誤碼。 </li><li>`UNAUTHORIZED_REQUEST`：用戶端未獲授權，無法要求授權碼。</li><li>`ACCESS_DENIED`：資源擁有者或授權伺服器已拒絕要求。</li><li>`INVALID_INSTANCE`： `AuthorityMetadata` 驗證失敗</li><li>`UNKNOWN_ERROR`：對伺服器的要求失敗，但不會發生錯誤，而且 `error_description` 會從服務傳回。</li><ul>| 此例外狀況類別代表與服務通訊時的錯誤，可以來自授權或權杖端點。 MSAL 會讀取錯誤，並從伺服器回應 error_description。 一般而言，這些錯誤會藉由在程式碼或應用程式註冊入口網站中修正應用程式設定來解決。 服務中斷很少會觸發此警告，只有等到服務復原之後，才能夠減輕這種問題。  |
|`MsalClientException`|<ul><li> `MULTIPLE_MATCHING_TOKENS_DETECTED`：找到多個快取專案，而且 sdk 無法從快取中識別正確的存取或重新整理權杖。 這個例外狀況通常表示 sdk 中用來儲存權杖的錯誤，或未在無訊息要求中提供授權單位，而且找到多個相符的標記。 </li><li>`DEVICE_NETWORK_NOT_AVAILABLE`：裝置上沒有使用中的網路。 </li><li>`JSON_PARSE_FAILURE`： Sdk 無法剖析 JSON 格式。</li><li>`IO_ERROR`： `IOException` 發生的原因可能是裝置或網路錯誤。 </li><li>`MALFORMED_URL`： Url 的格式不正確。 可能是在建立驗證要求、授權單位或重新導向 URI 時造成的。 </li><li>`UNSUPPORTED_ENCODING`：裝置不支援編碼。 </li><li>`NO_SUCH_ALGORITHM`：不支援用來產生[PKCE](https://tools.ietf.org/html/rfc7636)挑戰的演算法。 </li><li>`INVALID_JWT`： `JWT` 伺服器傳回的無效，或其為空白或格式不正確。 </li><li>`STATE_MISMATCH`：來自授權回應的狀態不符合授權要求中的狀態。 針對授權要求，sdk 會驗證從重新導向傳回的狀態，以及在要求中傳送的狀態。 </li><li>`UNSUPPORTED_URL`：不支援的 url，無法執行 ADFS 授權單位驗證。 </li><li> `AUTHORITY_VALIDATION_NOT_SUPPORTED`：授權單位不支援授權單位驗證。 Sdk 支援 B2C 授權單位，但不支援 B2C 授權單位驗證。 只有知名的主機才會受到支援。 </li><li>`CHROME_NOT_INSTALLED`： Chrome 未安裝在裝置上。 Sdk 會針對授權要求使用 chrome 自訂索引標籤（如果有的話），並會切換回 chrome 瀏覽器。 </li><li>`USER_MISMATCH`：取得權杖要求中提供的使用者不符合從伺服器傳回的使用者。</li></ul>|這個例外狀況類別代表程式庫的本機一般錯誤。 您可以藉由更正要求來處理這些例外狀況。|
|`MsalUserCancelException`|<ul><li>`USER_CANCELED`：使用者已起始互動式流程，而在收到權杖之前，他們已取消要求。 </li></ul>||
|`MsalArgumentException`|<ul><li>`ILLEGAL_ARGUMENT_ERROR_CODE`</li><li>`AUTHORITY_REQUIRED_FOR_SILENT`：必須為指定授權單位 `acquireTokenSilent` 。</li></ul>|這些錯誤可由開發人員更正引數，並確保互動式驗證、完成回呼、範圍以及已提供有效識別碼的帳戶的活動。|


## <a name="catching-errors"></a>攔截錯誤

下列程式碼片段顯示在無訊息呼叫的情況下攔截錯誤的範例 `acquireToken` 。

```java
/**
 * Callback used in for silent acquireToken calls.
 */
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");

            /* Successfully got a token, use it to call a protected resource - MSGraph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    };
}
```

## <a name="next-steps"></a>後續步驟 

深入瞭解[記錄錯誤](./msal-logging.md?tabs=android)