---
title: MSAL Android)  (的錯誤和例外狀況 |蔚藍
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
ms.openlocfilehash: 4185206e92a78c2684ba1690f03700ef2532cc5e
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761390"
---
# <a name="handle-exceptions-and-errors-in-msal-for-android"></a>處理 Android MSAL 中的例外狀況和錯誤

Microsoft 驗證程式庫 (MSAL) 中的例外狀況是用來協助應用程式開發人員針對應用程式進行疑難排解。 例外狀況訊息不會當地語系化。

在處理例外狀況和錯誤時，您可以使用例外狀況類型本身和錯誤碼來區別例外狀況。  如需錯誤碼清單，請參閱[驗證與授權錯誤碼](reference-aadsts-error-codes.md)。

在登入體驗期間，您可能會遇到關於同意、條件式存取 (MFA、裝置管理、位置型限制)、權杖發行和兌換，以及使用者屬性的錯誤。


|錯誤類別 | 原因/錯誤字串| 如何處理 |
|-----------|------------|----------------|
|`MsalUiRequiredException`| <ul><li>`INVALID_GRANT`：用來兌換存取權杖的重新整理權杖無效、已過期或已撤銷。 這個例外狀況可能是因為密碼變更所導致。 </li><li>`NO_TOKENS_FOUND`：存取權杖不存在，而且找不到可兌換存取權杖的重新整理權杖。</li> <li>需要逐步執行<ul><li>MFA</li><li>遺漏宣告</li></ul></li><li>由條件式存取封鎖 (例如，需要 [驗證代理](./msal-android-single-sign-on.md) 程式安裝) </li><li>`NO_ACCOUNT_FOUND`：快取中沒有可無訊息驗證的帳戶。</li></ul> |呼叫 `acquireToken()` 以提示使用者輸入其使用者名稱和密碼，並可能同意並執行多重要素驗證。|
|`MsalDeclinedScopeException`|<ul><li>`DECLINED_SCOPE`：使用者或伺服器尚未接受所有的範圍。 如果不支援、無法辨識或不支援特定帳戶的要求範圍，伺服器可能會拒絕範圍。 </li></ul>| 開發人員應該決定是否要以授與的範圍繼續驗證，或結束驗證程式。 只針對授與的範圍重新提交取得權杖要求的選項，並提供透過傳遞和呼叫授與許可權的提示 `silentParametersForGrantedScopes` `acquireTokenSilent` 。 |
|`MsalServiceException`|<ul><li>`INVALID_REQUEST`：此要求遺漏必要的參數、包含不正確參數、多次包含參數，或格式不正確。 </li><li>`SERVICE_NOT_AVAILABLE`：表示因為服務關閉的500/503/506 錯誤碼。 </li><li>`UNAUTHORIZED_REQUEST`：用戶端未經授權，無法要求授權碼。</li><li>`ACCESS_DENIED`：資源擁有者或授權伺服器拒絕要求。</li><li>`INVALID_INSTANCE`： `AuthorityMetadata` 驗證失敗</li><li>`UNKNOWN_ERROR`：對伺服器的要求失敗，但不會傳回任何錯誤，且 `error_description` 會從服務傳回。</li><ul>| 這個例外狀況類別代表與服務進行通訊時的錯誤，可以來自授權或權杖端點。 MSAL 會從伺服器回應讀取錯誤並 error_description。 一般來說，您可以在程式碼或應用程式註冊入口網站中修正應用程式設定，以解決這些錯誤。 服務中斷很少會觸發此警告，這項警告只能藉由等待服務復原來降低。  |
|`MsalClientException`|<ul><li> `MULTIPLE_MATCHING_TOKENS_DETECTED`：找到多個快取專案，且 sdk 無法從快取中識別正確的存取或重新整理權杖。 此例外狀況通常表示 sdk 中用於儲存權杖的錯誤，或未在無訊息要求中提供授權單位，但找到多個相符的權杖。 </li><li>`DEVICE_NETWORK_NOT_AVAILABLE`：裝置上沒有可用的網路。 </li><li>`JSON_PARSE_FAILURE`： Sdk 無法剖析 JSON 格式。</li><li>`IO_ERROR`： `IOException` 發生問題，可能是裝置或網路錯誤。 </li><li>`MALFORMED_URL`： Url 的格式不正確。 可能是在建立驗證要求、授權單位或重新導向 URI 時所造成。 </li><li>`UNSUPPORTED_ENCODING`：裝置不支援編碼。 </li><li>`NO_SUCH_ALGORITHM`：不支援用來產生 [PKCE](https://tools.ietf.org/html/rfc7636) 挑戰的演算法。 </li><li>`INVALID_JWT`： `JWT` 伺服器傳回無效或空白或格式不正確。 </li><li>`STATE_MISMATCH`：來自授權回應的狀態與授權要求中的狀態不相符。 針對授權要求，sdk 會驗證從重新導向傳回的狀態，以及在要求中傳送的狀態。 </li><li>`UNSUPPORTED_URL`：不支援的 url，無法執行 ADFS 授權單位驗證。 </li><li> `AUTHORITY_VALIDATION_NOT_SUPPORTED`：授權單位不支援授權單位驗證。 Sdk 支援 B2C 授權單位，但不支援 B2C 授權單位驗證。 只有已知的主機才會受到支援。 </li><li>`CHROME_NOT_INSTALLED`：裝置上未安裝 Chrome。 Sdk 會針對授權要求使用 chrome 自訂索引標籤（如果有的話），並切換回 chrome 瀏覽器。 </li><li>`USER_MISMATCH`：取得權杖要求中提供的使用者與從伺服器傳回的使用者不符。</li></ul>|此例外狀況類別代表程式庫區域的一般錯誤。 您可以藉由更正要求來處理這些例外狀況。|
|`MsalUserCancelException`|<ul><li>`USER_CANCELED`：使用者起始的互動式流程，而在接收權杖之前，他們已取消要求。 </li></ul>||
|`MsalArgumentException`|<ul><li>`ILLEGAL_ARGUMENT_ERROR_CODE`</li><li>`AUTHORITY_REQUIRED_FOR_SILENT`：必須為指定授權 `acquireTokenSilent` 。</li></ul>|開發人員更正引數，以及確保互動式驗證、完成回呼、範圍和具有有效識別碼的帳戶等活動都可減輕這些錯誤。|


## <a name="catching-errors"></a>攔截錯誤

下列程式碼片段顯示在無訊息呼叫時攔截錯誤的範例 `acquireToken` 。

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

深入瞭解 [MSAL For Android 的登入](msal-logging-android.md)。