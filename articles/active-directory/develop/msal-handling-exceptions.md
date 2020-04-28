---
title: 錯誤和例外狀況（MSAL）
titleSuffix: Microsoft identity platform
description: 瞭解如何處理 MSAL 應用程式中的錯誤和例外狀況、條件式存取和宣告挑戰。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 93d07ab1740da68298478ae2dcc2ab46d8d8362e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80884013"
---
# <a name="handle-msal-exceptions-and-errors"></a>處理 MSAL 的例外狀況和錯誤

本文概述不同類型的錯誤，以及處理常見登入錯誤的建議。

## <a name="msal-error-handling-basics"></a>MSAL 錯誤處理基本概念

Microsoft 驗證程式庫（MSAL）中的例外狀況是供應用程式開發人員進行疑難排解，而不是向使用者顯示。 例外狀況訊息不會當地語系化。

在處理例外狀況和錯誤時，您可以使用例外狀況類型本身和錯誤碼來區別例外狀況。  如需錯誤碼清單，請參閱[驗證與授權錯誤碼](reference-aadsts-error-codes.md)。

在登入體驗期間，您可能會遇到關於同意、條件式存取（MFA、裝置管理、以位置為基礎的限制）、權杖發行和兌換，以及使用者屬性的錯誤。

如需有關應用程式錯誤處理的詳細資訊，請參閱下一節符合您所使用的語言。

## <a name="net"></a>[.NET](#tab/dotnet)

處理 .NET 例外狀況時，您可以使用例外狀況類型本身和`ErrorCode`成員來區別例外狀況。 `ErrorCode`值是[MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet)類型的常數。

您也可以查看[MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet)、 [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)和[MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet)的欄位。

如果擲回[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) ，請嘗試[驗證和授權錯誤碼](reference-aadsts-error-codes.md)，以查看程式碼是否列在該處。

### <a name="common-net-exceptions"></a>常見的 .NET 例外狀況

以下是可能會擲回的常見例外狀況和一些可能的緩和措施：  

| 例外狀況 | 錯誤碼 | 緩和措施|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001：使用者或系統管理員尚未同意使用識別碼為 ' {appId} ' 且名為 ' {appName} ' 的應用程式。 請傳送此使用者和資源的互動式授權要求。| 您必須先取得使用者同意。 如果您未使用 .NET Core （不具有任何 Web UI），請呼叫（僅限一`AcquireTokeninteractive`次）。 如果您使用 .NET core 或不想要執行`AcquireTokenInteractive`，則使用者可以流覽至 URL 以提供同意：。 `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read` 若要`AcquireTokenInteractive`呼叫：`app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079：使用者必須使用多重要素驗證（MFA）。| 不會降低風險。 如果已為您的租使用者設定 MFA，而 Azure Active Directory （AAD）決定強制執行，則您必須回到互動式流程，例如`AcquireTokenInteractive`或`AcquireTokenByDeviceCode`。|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010：不支援 */common*或 */Consumers*端點上的授與類型。 請使用 */organizations* 或租用戶專屬端點。 您使用的是 */common*。| 如 Azure AD 的訊息所說明，授權單位必須有一個租用戶或 */organizations*。|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002：要求主體必須包含下列參數： `client_secret or client_assertion`。| 如果您的應用程式未在 Azure AD 中註冊為公用用戶端應用程式，則會擲回這個例外狀況。 在 [Azure 入口網站中，編輯應用程式的資訊清單， `allowPublicClient`並`true`將設定為。 |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`：無法識別已登入的使用者| 程式庫無法查詢目前 Windows 登入的使用者，或此使用者未加入 AD 或 AAD （不支援工作-加入的使用者）。 緩和措施1：在 UWP 上，檢查應用程式是否具有下列功能：企業驗證、私人網路（用戶端和伺服器）、使用者帳戶資訊。 緩和措施2：執行您自己的邏輯來提取使用者名稱（例如john@contoso.com），並使用`AcquireTokenByIntegratedWindowsAuth`在使用者名稱中採用的格式。|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| 此方法需依賴由 Active Directory (AD) 公開的通訊協定。 如果使用者建立於不具 AD 支援 (「受控」使用者) 的 Azure Active Directory 中，此方法將會失敗。 使用者若建立於 AD 中，並且受 AAD 支援(「同盟」使用者)，則可受益於這種非互動式的驗證方法。 緩和：使用互動式驗證。|

### `MsalUiRequiredException`

呼叫`AcquireTokenSilent()` is 時， `MsalError.InvalidGrantError`從 MSAL.NET 傳回的其中一個常見狀態碼。 此狀態碼表示應用程式應該再次呼叫驗證程式庫，但在互動模式中（適用于公用用戶端應用程式的 AcquireTokenInteractive 或 AcquireTokenByDeviceCodeFlow，並在 Web apps 中執行挑戰）。 這是因為在可以發行驗證權杖之前，需要額外的使用者互動。

大部分的時間都`AcquireTokenSilent`失敗，這是因為權杖快取沒有符合您要求的權杖。 存取權杖會在1小時後到期`AcquireTokenSilent` ，並會嘗試根據重新整理權杖提取新的權杖（以 OAuth2 的詞彙而言，這是「重新整理權杖」流程）。 此流程也可能因各種原因而失敗，例如，租使用者系統管理員設定更嚴格的登入原則。 

互動的目標是讓使用者執行動作。 其中有些條件可讓使用者輕鬆解決（例如，只要按一下就能接受使用規定），而有些則無法使用目前的設定來解析（例如，有問題的電腦需要連線到特定的公司網路）。 有些可協助使用者設定多重要素驗證，或在其裝置上安裝 Microsoft Authenticator。

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException`分類列舉

MSAL 會公開`Classification`一個欄位，您可以加以閱讀以提供更佳的使用者體驗，例如，告訴使用者其密碼已過期，或需要同意使用某些資源。 支援的值是`UiRequiredExceptionClassification`列舉的一部分：

| 分類    | 意義           | 建議的處理 |
|-------------------|-------------------|----------------------|
| BasicAction | 在互動式驗證流程期間，使用者互動可以解決條件。 | 呼叫 AcquireTokenInteractively （）。 |
| AdditionalAction | 您可以透過其他與系統的補救互動來解決條件，這是互動式驗證流程外部。 | 呼叫 AcquireTokenInteractively （），以顯示說明補救動作的訊息。 如果使用者不太可能會完成補救動作，則呼叫應用程式可能會選擇隱藏需要 additional_action 的流程。 |
| MessageOnly      | 目前無法解決條件。 啟動互動式驗證流程將會顯示說明條件的訊息。 | 呼叫 AcquireTokenInteractively （）以顯示說明條件的訊息。 AcquireTokenInteractively （）會在使用者讀取訊息並關閉視窗之後，傳回 UserCanceled 錯誤。 如果使用者不太可能受益于訊息，則呼叫應用程式可能會選擇隱藏會導致 message_only 的流程。|
| ConsentRequired  | 使用者同意已遺失或已被撤銷。 | 呼叫 AcquireTokenInteractively （）讓使用者同意。 |
| UserPasswordExpired | 使用者的密碼已過期。 | 呼叫 AcquireTokenInteractively （），讓使用者可以重設其密碼。 |
| PromptNeverFailed| 呼叫互動式驗證時，參數提示 = 永不，強制 MSAL 依賴瀏覽器 cookie，而不是顯示瀏覽器。 這是失敗的。 | 呼叫 AcquireTokenInteractively （）而不提示。無 |
| AcquireTokenSilentFailed | MSAL SDK 沒有足夠的資訊可從快取中提取權杖。 這可能是因為快取中沒有任何權杖，或找不到帳戶。 錯誤訊息有更多詳細資料。  | 呼叫 AcquireTokenInteractively （）。 |
| None    | 未提供進一步的詳細資料。 在互動式驗證流程期間，使用者互動可能會解決條件。 | 呼叫 AcquireTokenInteractively （）。 |

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

## <a name="javascript"></a>[JavaScript](#tab/javascript)

MSAL 會提供錯誤物件，以抽象和分類不同類型的常見錯誤。 它也會提供介面來存取錯誤的特定詳細資料，例如錯誤訊息，以適當地處理它們。

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
- `AuthError.message`：與相同`errorMessage`。
- `AuthError.stack`：擲回錯誤的堆疊追蹤。

### <a name="error-types"></a>錯誤類型

可用的錯誤類型如下：

- `AuthError`： MSAL 程式庫的基底錯誤類別，也用於非預期的錯誤。

- `ClientAuthError`： Error 類別，這表示用戶端驗證的問題。 來自程式庫的錯誤大多為 ClientAuthError。 當登入已在進行時呼叫登入方法、使用者取消登入等等，會導致這些錯誤。

- `ClientConfigurationError`： Error 類別，會`ClientAuthError`在指定的使用者設定參數格式不正確或遺失時，于提出要求之前進行擴充。

- `ServerError`： Error 類別，代表驗證服務器所傳送的錯誤字串。 這些可能是要求格式或參數無效之類的錯誤，或導致伺服器無法對使用者進行驗證或授權的任何其他錯誤。

- `InteractionRequiredAuthError`： Error 類別，擴充`ServerError`以代表需要互動式呼叫的伺服器錯誤。 `acquireTokenSilent`如果使用者需要與伺服器互動以提供認證或同意驗證/授權，則會擲回這個錯誤。 錯誤碼包括`"interaction_required"`、 `"login_required"`和。 `"consent_required"`

對於使用重新導向方法（`loginRedirect`， `acquireTokenRedirect`）的驗證流程中的錯誤處理，您必須註冊回呼，這會在使用`handleRedirectCallback()`方法重新導向之後以成功或失敗來呼叫，如下所示：

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

當您嘗試使用非互動式方法來取得權杖（例如`acquireTokenSilent`），但 MSAL 無法以無訊息模式執行時，會傳回錯誤。

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

## <a name="python"></a>[Python](#tab/python)

在適用于 Python 的 MSAL 中，大部分的錯誤都是以 API 呼叫的傳回值來傳達。 此錯誤會以包含來自 Microsoft 身分識別平臺的 JSON 回應的字典來表示。

* 成功的回應會包含`"access_token"`索引鍵。 回應的格式是由 OAuth2 通訊協定所定義。 如需詳細資訊，請參閱[5.1 成功回應](https://tools.ietf.org/html/rfc6749#section-5.1)
* 錯誤回應包含`"error"` （通常是`"error_description"`）。 回應的格式是由 OAuth2 通訊協定所定義。 如需詳細資訊，請參閱[5.2 錯誤回應](https://tools.ietf.org/html/rfc6749#section-5.2)

傳回錯誤時，索引`"error_description"`鍵會包含人們可讀取的訊息;而這通常包含 Microsoft 身分識別平臺錯誤碼。 如需各種錯誤碼的詳細資訊，請參閱[驗證和授權錯誤碼](https://docs.microsoft.com/azure/active-directory/develop/reference-aadsts-error-codes)。

在 Python 的 MSAL 中，例外狀況很罕見，因為大部分的錯誤都是藉由傳回錯誤值來處理。 只有`ValueError`當您嘗試使用程式庫的方式發生問題時（例如，當 API 參數的格式不正確時，才會擲回例外狀況）。

## <a name="java"></a>[Java](#tab/java)

在 MSAL for JAVA 中，有三種類型的例外`MsalClientException`狀況`MsalServiceException`：、 `MsalInteractionRequiredException`和。繼承自`MsalException`的所有。

- `MsalClientException`當程式庫或裝置的本機錯誤發生時，就會擲回。
- `MsalServiceException`當安全權杖服務（STS）傳回錯誤回應或發生另一個網路錯誤時，就會擲回。
- `MsalInteractionRequiredException`當需要 UI 互動才能成功驗證時，就會擲回。

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException`將要求中傳回的 HTTP 標頭公開至 STS。 透過存取`MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

呼叫`AcquireTokenSilently()`時`InvalidGrantError`，從 MSAL for JAVA 傳回的其中一個常見狀態碼。 這表示必須先進行額外的使用者互動，才能發出驗證權杖。 您的應用程式應該再次呼叫驗證程式庫，但在互動模式`AuthorizationCodeParameters`中`DeviceCodeParameters` ，您可以傳送或公開用戶端應用程式。

大部分的時間都`AcquireTokenSilently`失敗，這是因為權杖快取沒有符合您要求的權杖。 存取權杖會在一小時後到期`AcquireTokenSilently` ，並會嘗試根據重新整理權杖取得新的權杖。 在 OAuth2 詞彙中，這是重新整理權杖流程。 此流程也可能因各種原因而失敗，例如當租使用者系統管理員設定更嚴格的登入原則時。

導致此錯誤的某些條件可讓使用者輕鬆解決。 例如，他們可能需要接受使用規定。 或者，可能是因為電腦需要連線到特定的公司網路，而無法使用目前的設定來完成要求。

MSAL 會公開`reason`一個欄位，您可以用它來提供更好的使用者體驗。 例如， `reason`欄位可能會引導您告訴使用者其密碼已過期，或他們必須同意使用一些資源。 支援的值是`InteractionRequiredExceptionReason`列舉的一部分：

| 原因 | 意義 | 建議的處理 |
|---------|-----------|-----------------------------|
| `BasicAction` | 在互動式驗證流程期間，使用者互動可以解決條件 | 使用`acquireToken`互動式參數呼叫 |
| `AdditionalAction` | 您可以透過與互動性驗證流程外的系統進行額外的補救互動來解決條件。 | 使用`acquireToken`互動式參數呼叫以顯示訊息，說明要採取的補救動作。 如果使用者不太可能會完成補救動作，則呼叫應用程式可能會選擇隱藏需要其他動作的流程。 |
| `MessageOnly` | 目前無法解決條件。 啟動互動式驗證流程，以顯示說明條件的訊息。 | 使用`acquireToken`互動式參數呼叫，以顯示說明條件的訊息。 `acquireToken`會在使用者`UserCanceled`讀取訊息並關閉視窗之後傳回錯誤。 如果使用者不太可能受益于訊息，應用程式可能會選擇隱藏會產生訊息的流程。 |
| `ConsentRequired`| 使用者同意已遺失或已被撤銷。 |使用`acquireToken`互動式參數呼叫，讓使用者可以同意。 |
| `UserPasswordExpired` | 使用者的密碼已過期。 | 使用`acquireToken`互動式參數呼叫，讓使用者可以重設其密碼 |
| `None` |  系統會提供進一步的詳細資料。 在互動式驗證流程期間，使用者互動可能會解決此狀況。 | 使用`acquireToken`互動式參數呼叫 |

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

## <a name="iosmacos"></a>[iOS/macOS](#tab/iosmacos)

IOS 和 macOS 錯誤的 MSAL 完整清單列在[MSALError 列舉](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128)中。

所有產生的 MSAL 錯誤都會與`MSALErrorDomain`網域一起傳回。

對於系統錯誤，MSAL 會從系統`NSError` API 傳回原始的。 例如，如果因為缺少網路連線而導致權杖取得失敗，則`NSURLErrorDomain` MSAL 會傳回含有網域和`NSURLErrorNotConnectedToInternet`程式碼的錯誤。

我們建議您在用戶端至少處理下列兩個 MSAL 錯誤：

- `MSALErrorInteractionRequired`：使用者必須執行互動式要求。 有許多狀況可能會導致此錯誤，例如過期的驗證會話，或需要額外的驗證需求。 呼叫 MSAL 互動式權杖取得 API 以進行復原。 

- `MSALErrorServerDeclinedScopes`：已拒絕部分或所有範圍。 決定是否只以授與的範圍繼續進行，或停止登入程式。

> [!NOTE]
> `MSALInternalError`列舉應該僅用於參考和偵錯工具。 請勿嘗試在執行時間自動處理這些錯誤。 如果您的應用程式遇到任何落在之下`MSALInternalError`的錯誤，您可能會想要顯示一般使用者面向的訊息，說明發生了什麼事。

例如， `MSALInternalErrorBrokerResponseNotReceived`表示使用者未完成驗證，並以手動方式傳回至應用程式。 在此情況下，您的應用程式應該會顯示一般錯誤訊息，說明驗證未完成，並建議他們再次嘗試驗證。

下列目標-C 範例程式碼示範處理一些常見錯誤狀況的最佳作法：

```objc
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                    // See more info here: https://aka.ms/intuneMAMSDK
                    
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        // See more info here: https://aka.ms/intuneMAMSDK
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

---

## <a name="conditional-access-and-claims-challenges"></a>條件式存取和宣告挑戰

以無訊息方式取得權杖時，您的應用程式可能會在您嘗試存取的 API 需要[條件式存取宣告挑戰](../azuread-dev/conditional-access-dev-guide.md)（例如 MFA 原則）時收到錯誤。

處理此錯誤的模式是使用 MSAL 以互動方式取得權杖。 以互動方式取得權杖時，系統會對使用者發出提示，讓他們有機會符合所需的條件式存取原則。

在某些情況下，當您呼叫需要條件式存取的 API 時，您可能會在 API 發出的錯誤中收到宣告挑戰。 例如，如果條件式存取原則是使用受控裝置（Intune），則錯誤會類似[AADSTS53000：您的裝置必須受到管理才能存取此資源](reference-aadsts-error-codes.md)或類似的內容。 在此情況下，您可以在取得權杖的呼叫中傳入宣告，讓系統提示使用者符合適當的原則。

### <a name="net"></a>.NET

從 MSAL.NET 呼叫需要條件式存取的 API 時，您的應用程式將必須處理宣告挑戰例外狀況。 此例外狀況會呈現為[宣告](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet)屬性非空白的 [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)。

若要處理索賠挑戰，您必須使用`.WithClaim()` `PublicClientApplicationBuilder`類別的方法。

### <a name="javascript"></a>JavaScript

使用 MSAL 以無訊息方式`acquireTokenSilent`取得權杖時（使用），您的應用程式可能會在您嘗試存取的 API 需要[條件式存取宣告挑戰](../azuread-dev/conditional-access-dev-guide.md)（例如 MFA 原則）時收到錯誤。

處理此錯誤的模式，是發出在 MSAL.js 中取得權杖的互動式呼叫 (例如 `acquireTokenPopup` 或 `acquireTokenRedirect`)，如下列範例所示：

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function (accessTokenResponse) {
    // call API
}).catch( function (error) {
    if (error instanceof InteractionRequiredAuthError) {
        // Extract claims from error message
        accessTokenRequest.claimsRequest = extractClaims(error.errorMessage);
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
    }
});
```

以互動方式取得權杖時，系統會對使用者發出提示，讓他們有機會符合所需的條件式存取原則。

當您呼叫需要條件式存取的 API 時，您可能會在 API 發出的錯誤中收到宣告挑戰。 在此情況下，您可以將錯誤中傳回的宣告傳遞給`claimsRequest` `AuthenticationParameters.ts`類別的欄位，以滿足適當的原則。 

如需詳細資訊，請參閱[要求額外的宣告](active-directory-optional-claims.md)。

### <a name="msal-for-ios-and-macos"></a>適用於 iOS 和 macOS 的 MSAL

適用于 iOS 和 macOS 的 MSAL 可讓您在互動式和無訊息的權杖取得案例中，要求特定的宣告。

若要要求自訂宣告`claimsRequest` ， `MSALSilentTokenParameters`請`MSALInteractiveTokenParameters`在或中指定。

如需詳細資訊，請參閱[使用適用于 iOS 和 macOS 的 MSAL 要求自訂宣告](request-custom-claims.md)。

## <a name="retrying-after-errors-and-exceptions"></a>在發生錯誤和例外狀況後重試

當您呼叫 MSAL 時，應該會執行自己的重試原則。 MSAL 會對 AAD 服務進行 HTTP 呼叫，偶爾會發生失敗，例如網路可能會中斷，或伺服器已超載。  

### <a name="http-error-codes-500-600"></a>HTTP 錯誤碼 500-600

針對 HTTP 錯誤碼為 500-600 的錯誤，MSAL.NET 會實作簡單的單次重試機制。

### <a name="http-429"></a>HTTP 429

當服務權杖伺服器（STS）因為要求太多而超載時，它會傳回 HTTP 錯誤429，並提示您多久才能在 [ `Retry-After`回應] 欄位中再試一次。

### <a name="net"></a>.NET

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)表面`System.Net.Http.Headers.HttpResponseHeaders`作為屬性`namedHeaders`。 您可以使用錯誤碼的其他資訊來改善應用程式的可靠性。 在描述的案例中，您可以使用`RetryAfterproperty` （屬於型`RetryConditionHeaderValue`別）和計算時機來重試。

以下是使用用戶端認證流程的 daemon 應用程式範例。 您可以將其調整為任何取得權杖的方法。

```csharp
do
{
    retry = false;
    TimeSpan? delay;
    try
    {
         result = await publicClientApplication.AcquireTokenForClient(scopes, account)
                                           .ExecuteAsync();
    }
    catch (MsalServiceException serviceException)
    {
         if (ex.ErrorCode == "temporarily_unavailable")
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
    …
    if (delay.HasValue)
    {
        Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
        retry = true;
    }
} while (retry);
```
