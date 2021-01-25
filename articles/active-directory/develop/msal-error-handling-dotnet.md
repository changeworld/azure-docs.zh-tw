---
title: 處理 MSAL.NET 中的錯誤和例外狀況
titleSuffix: Microsoft identity platform
description: 瞭解如何處理 MSAL.NET 中的錯誤和例外狀況、條件式存取宣告挑戰和重試。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 381416384cacd44bdb1b08801f7b3174c9504d0b
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761186"
---
# <a name="handle-errors-and-exceptions-in-msalnet"></a>處理 MSAL.NET 中的錯誤和例外狀況

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msalnet"></a>MSAL.NET 中的錯誤處理

在處理 .NET 例外狀況時，您可以使用例外狀況類型本身和 `ErrorCode` 成員來區別例外狀況。 `ErrorCode` 值是 [MsalError](/dotnet/api/microsoft.identity.client.msalerror) 類型的常數。

您也可以查看 [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception)、[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) 和 [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) 的欄位。

如果擲回 [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception)，請嘗試[驗證與授權錯誤碼](reference-aadsts-error-codes.md)以查看該錯誤碼是否有列於該處。

### <a name="common-net-exceptions"></a>常見的 .NET 例外狀況

以下是可能會擲回的常見例外狀況和一些可能的補救措施：  

| 例外狀況 | 錯誤碼 | 降低|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS65001：使用者或系統管理員未同意使用識別碼為 '{appId}'、名稱為 '{appName}' 的應用程式。 請傳送此使用者和資源的互動式授權要求。| 您必須先取得使用者同意。 如果您未使用 .NET Core (因此就沒有任何 Web UI)，請呼叫 `AcquireTokeninteractive` (一次即可)。 如果您使用 .NET core 或不想要執行 `AcquireTokenInteractive`，則使用者可以瀏覽至下列 URL 表示同意：`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read`。 以呼叫 `AcquireTokenInteractive`：`app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS50079：使用者必須使用[多重要素驗證 (MFA)](../authentication/concept-mfa-howitworks.md)。| 沒有風險降低措施。 如果您的租用戶已設定 MFA，且 Azure Active Directory (AAD) 決定加以強制執行，您就必須退回到 `AcquireTokenInteractive` 或 `AcquireTokenByDeviceCode` 之類的互動式流程。|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) |AADSTS90010：不支援透過 */common* 或 */consumers* 端點的授與類型。 請使用 */organizations* 或租用戶專屬端點。 您使用的是 */common*。| 如 Azure AD 的訊息所說明，授權單位必須有一個租用戶或 */organizations*。|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) | AADSTS70002：要求本文必須包含下列參數：`client_secret or client_assertion`。| 如果您的應用程式未在 Azure AD 中註冊為公用用戶端應用程式，就會擲回這個例外狀況。 在 Azure 入口網站中，為您的應用程式編輯資訊清單，並將 `allowPublicClient` 設為 `true`。 |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)| `unknown_user Message`:無法識別登入的使用者| 程式庫無法查詢目前的 Windows 登入使用者，或此使用者不是 AD 或 Azure AD 加入 (不支援已加入工作的使用者) 。 風險降低措施 1：在 UWP 上，確認應用程式具有下列功能：企業驗證、私人網路 (用戶端和伺服器)、使用者帳戶資訊。 降低風險 2：實作您自己的邏輯以擷取使用者名稱 (例如john@contoso.com)，並使用採用該使用者名稱的 `AcquireTokenByIntegratedWindowsAuth` 表單。|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)|integrated_windows_auth_not_supported_managed_user| 此方法需依賴由 Active Directory (AD) 公開的通訊協定。 如果在 Azure AD 中建立的使用者沒有 AD 支援 ( 「受管理」使用者) ，則此方法將會失敗。 在 AD 中建立並受 Azure AD ( 「同盟」使用者支援的使用者，) 可受益于這種非互動式驗證方法。 風險降低：使用互動式驗證。|

### `MsalUiRequiredException`

在呼叫 `AcquireTokenSilent()` 時，其中一個會從 MSAL.NET 傳回的常見狀態碼為 `MsalError.InvalidGrantError`。 此狀態碼表示應用程式應該再次呼叫驗證程式庫，但在互動模式中 (公用用戶端應用程式的 AcquireTokenInteractive 或 AcquireTokenByDeviceCodeFlow，在 Web 應用程式) 中有一項挑戰。 這是因為必須先進行額外的使用者互動，然後才能發行驗證權杖。

`AcquireTokenSilent` 會失敗大多數是因為權杖快取沒有符合您要求的權杖。 存取權杖會在 1 小時後到期，然後 `AcquireTokenSilent` 會嘗試根據重新整理權杖來擷取新的權杖 (若在 OAuth2 中，這會是「重新整理權杖」流程)。 此流程也可能會因為各種原因而失敗，例如，如果租用戶管理員設定更嚴格的登入原則。 

互動是為了讓使用者執行動作。 使用者輕易就能解決其中某些情況 (例如，按一下來接受使用規定)，但某些情況在目前的設定下則無法獲得解決 (例如，有問題的電腦需要連線到特定公司網路)。 某些情況可協助使用者設定多重要素驗證，或在其裝置上安裝 Microsoft Authenticator。

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException` 分類列舉

MSAL 會公開一個 `Classification` 欄位，您可以閱讀該欄位以提供更好的使用者體驗。 例如，告知使用者其密碼已過期，或必須同意才能使用某些資源。 支援的值為 `UiRequiredExceptionClassification` 列舉的一部分：

| 分類    | 意義           | 建議的處理 |
|-------------------|-------------------|----------------------|
| BasicAction | 在互動式驗證流程期間，此情況可透過使用者互動加以解決。 | 呼叫 AcquireTokenInteractively()。 |
| AdditionalAction | 在互動式驗證流程之外，此情況可透過與系統進行其他補救互動來加以解決。 | 呼叫 AcquireTokenInteractively() 以顯示訊息來說明補救動作。 如果使用者不太可能完成補救動作，則呼叫端應用程式可選擇隱藏需要 additional_action 的流程。 |
| MessageOnly      | 此情況目前無法獲得解決。 啟動互動式驗證流程將會顯示訊息以說明情況。 | 呼叫 AcquireTokenInteractively() 以顯示訊息來說明情況。 AcquireTokenInteractively() 會在使用者讀取訊息並關閉視窗之後，傳回 UserCanceled 錯誤。 如果使用者不太可能受益於訊息，則呼叫端應用程式可選擇隱藏會導致 message_only 的流程。|
| ConsentRequired  | 使用者同意已遺失或遭到撤銷。 | 呼叫 AcquireTokenInteractively() 以便讓使用者提供同意。 |
| UserPasswordExpired | 使用者的密碼已過期。 | 呼叫 AcquireTokenInteractively()，讓使用者可以重設其密碼。 |
| PromptNeverFailed| 使用參數提示=never 呼叫了互動式驗證，而強制 MSAL 依賴瀏覽器 Cookie，而不是顯示瀏覽器。 這已失敗。 | 在沒有 Prompt.None 的情況下呼叫 AcquireTokenInteractively() |
| AcquireTokenSilentFailed | MSAL SDK 沒有足夠的資訊可從快取中擷取權杖。 這可能是因為快取中沒有任何權杖，或找不到帳戶。 錯誤訊息中會有更多詳細資料。  | 呼叫 AcquireTokenInteractively()。 |
| None    | 未提供進一步的詳細資料。 在互動式驗證流程期間，此情況可透過使用者互動加以解決。 | 呼叫 AcquireTokenInteractively()。 |

## <a name="net-code-example"></a>.NET 程式碼範例

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
      res = await application.AcquireTokenInteractive(scopes).ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```
[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

從 MSAL.NET 呼叫需要條件式存取的 API 時，您的應用程式將必須處理宣告挑戰例外狀況。 此例外狀況會呈現為[宣告](/dotnet/api/microsoft.identity.client.msalserviceexception.claims)屬性非空白的 [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception)。

若要處理宣告挑戰，您必須使用 `PublicClientApplicationBuilder` 類別的 `.WithClaim()` 方法。

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

### <a name="http-error-codes-500-600"></a>HTTP 錯誤碼 500-600

針對 HTTP 錯誤碼為 500-600 的錯誤，MSAL.NET 會實作簡單的單次重試機制。

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) 會將 `System.Net.Http.Headers.HttpResponseHeaders` 顯示為屬性 `namedHeaders`。 您可以使用錯誤碼中的其他資訊改善應用程式的可靠性。 在上述案例中，您可以使用 `RetryAfterproperty` (類型 `RetryConditionHeaderValue`) 並計算何時可重試。

以下是使用用戶端認證流程的精靈應用程式範例。 您可以將其調整為任何可用於取得權杖的方法。

```csharp

bool retry = false;
do
{
    TimeSpan? delay;
    try
    {
         result = await publicClientApplication.AcquireTokenForClient(scopes, account).ExecuteAsync();
    }
    catch (MsalServiceException serviceException)
    {
         if (serviceException.ErrorCode == "temporarily_unavailable")
         {
             RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
             if (retryAfter.Delta.HasValue)
             {
                 delay = retryAfter.Delta;
             }
             else if (retryAfter.Date.HasValue)
             {
                 delay = retryAfter.Date.Value.Offset;
             }
         }
    }
    // . . .
    if (delay.HasValue)
    {
        Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
        retry = true;
    }
} while (retry);
```

## <a name="next-steps"></a>後續步驟

請考慮 [在 MSAL.NET 中啟用記錄](msal-logging-dotnet.md) ，以協助您診斷和偵測問題。
