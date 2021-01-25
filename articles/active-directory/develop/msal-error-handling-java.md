---
title: 處理 MSAL4J 中的錯誤和例外狀況
titleSuffix: Microsoft identity platform
description: 瞭解如何處理 MSAL4J 應用程式中的錯誤和例外狀況、條件式存取宣告挑戰和重試。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/27/2020
ms.author: marsma
ms.reviewer: saeeda, nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8563804a736c37acc9a96eb4a186933507f34200
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98760693"
---
# <a name="handle-errors-and-exceptions-in-msal-for-java"></a>處理 Java 版 MSAL 中的錯誤和例外狀況

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-java"></a>JAVA 中的 MSAL 錯誤處理

在適用於 Java 的 MSAL 中有三種類型的例外狀況：`MsalClientException`、`MsalServiceException` 和 `MsalInteractionRequiredException`；這三個類型全都繼承自 `MsalException`。

- 當程式庫或裝置的本機錯誤發生時，就會擲回 `MsalClientException`。
- 當安全權杖服務 (STS) 傳回錯誤回應或發生另一個網路錯誤時，就會擲回 `MsalServiceException`。
- 當需要 UI 互動才能成功驗證時，就會擲回 `MsalInteractionRequiredException`。

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException` 會向 STS 公開要求中所傳回的 HTTP 標頭。 請透過 `MsalServiceException.headers()` 來存取這些標頭

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

在呼叫 `AcquireTokenSilently()` 時，其中一個會從適用於 Java 的 MSAL 傳回的常見狀態碼為 `InvalidGrantError`。 這表示必須先進行額外的使用者互動，然後才能發行驗證權杖。 您的應用程式應該再次呼叫驗證程式庫，但要在互動模式下，方法是傳送公用用戶端應用程式的 `AuthorizationCodeParameters` 或 `DeviceCodeParameters`。

`AcquireTokenSilently` 會失敗大多數是因為權杖快取沒有符合您要求的權杖。 存取權杖會在一小時後到期，然後 `AcquireTokenSilently` 會嘗試根據重新整理權杖來取得新的存取權杖。 若在 OAuth2 中，這會是「重新整理權杖」流程。 此流程也可能會因為各種原因而失敗，例如，當租用戶管理員設定更嚴格的登入原則時。

使用者輕易就能解決導致此錯誤的某些情況。 例如，他們可能需要接受使用規定，否則無法使用目前的設定來滿足要求，因為電腦需要連線到特定的公司網路。

MSAL 會公開 `reason` 欄位，以供您用來提供更好的使用者體驗。 例如，`reason` 欄位可能會引導您告訴使用者其密碼已過期，或其必須提供同意才能使用某些資源。 支援的值為 `InteractionRequiredExceptionReason` 列舉的一部分：

| 原因 | 意義 | 建議的處理 |
|---------|-----------|-----------------------------|
| `BasicAction` | 在互動式驗證流程期間，此情況可透過使用者互動加以解決。 | `acquireToken`使用互動式參數進行呼叫。 |
| `AdditionalAction` | 在互動式驗證流程之外，此情況可透過與系統進行其他補救互動來加以解決。 | 使用互動式參數呼叫 `acquireToken`，以顯示訊息來說明要採取的補救動作。 如果使用者不太可能完成補救動作，則呼叫端應用程式可選擇隱藏需要其他動作的流程。 |
| `MessageOnly` | 此情況目前無法獲得解決。 啟動互動式驗證流程以顯示會說明情況的訊息。 | 使用互動式參數呼叫 `acquireToken`，以顯示訊息來說明情況。 `acquireToken` 會在使用者讀取訊息並關閉視窗之後傳回 `UserCanceled` 錯誤。 如果使用者不太可能受益於訊息，則應用程式可選擇隱藏會導致訊息的流程。 |
| `ConsentRequired`| 使用者同意已遺失或遭到撤銷。 |使用互動式參數呼叫 `acquireToken`，讓使用者可以提供同意。 |
| `UserPasswordExpired` | 使用者的密碼已過期。 | `acquireToken`使用互動式參數進行呼叫，讓使用者可以重設其密碼。 |
| `None` |  系統會提供進一步的詳細資料。 在互動式驗證流程期間，此情況可透過使用者互動加以解決。 | `acquireToken`使用互動式參數進行呼叫。 |

### <a name="code-example"></a>程式碼範例

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>後續步驟

請考慮 [在 MSAL For JAVA 中啟用記錄功能](msal-logging-java.md) ，以協助您診斷和偵測問題。
