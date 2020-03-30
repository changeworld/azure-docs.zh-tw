---
title: 錯誤和異常 （MSAL）
titleSuffix: Microsoft identity platform
description: 瞭解如何處理 MSAL 應用程式中的錯誤和異常、條件訪問和聲明挑戰。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/22/2019
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: f78c64fc0ba25dc3310b24e873dbae266ea2f281
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050327"
---
# <a name="handle-msal-exceptions-and-errors"></a>處理 MSAL 異常和錯誤

本文概述了不同類型的錯誤以及處理常見登錄錯誤的建議。

## <a name="msal-error-handling-basics"></a>MSAL 錯誤處理基礎知識

Microsoft 身份驗證庫 （MSAL） 中的異常適用于應用開發人員進行故障排除，而不是用於向最終使用者顯示。 例外狀況訊息不會當地語系化。

在處理例外狀況和錯誤時，您可以使用例外狀況類型本身和錯誤碼來區別例外狀況。  如需錯誤碼清單，請參閱[驗證與授權錯誤碼](reference-aadsts-error-codes.md)。

在登錄體驗期間，可能會遇到有關同意、條件訪問（MFA、裝置管理、基於位置的限制）、權杖頒發和兌換以及使用者屬性的錯誤。

有關應用錯誤處理的更多詳細資訊，請參閱以下與所使用的語言相匹配的部分。

## <a name="net"></a>[.NET](#tab/dotnet)

處理 .NET 異常時，可以使用異常類型本身和`ErrorCode`成員來區分異常。 `ErrorCode`值是[MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet)類型的常量。

您還可以查看[MsalClientexception、MsalServiceexception](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet)和[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)[MsalUI"例外"欄位](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet)。

如果引發[MsalServiceException，](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)請嘗試[身份驗證和授權錯誤代碼](reference-aadsts-error-codes.md)以查看代碼是否列出。

### <a name="common-net-exceptions"></a>常見的 .NET 異常

以下是可能引發的常見異常和一些可能的緩解措施：  

| 例外狀況 | 錯誤碼 | 降低|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001：使用者或管理員未同意將應用程式與 ID"{appId}"稱為"[appName]"使用。 請傳送此使用者和資源的互動式授權要求。| 您必須先取得使用者同意。 如果您不使用 .NET Core（沒有任何 Web UI），請調用（僅限一次）。 `AcquireTokeninteractive` 如果您正在使用 .NET 內核或不想執行 ，`AcquireTokenInteractive`使用者可以導航到 URL 以表示同意： `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read`。 致電`AcquireTokenInteractive`：`app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079：使用者需要使用多重要素驗證 （MFA）。| 沒有緩解。 如果為租戶配置了 MFA，Azure 活動目錄 （AAD） 決定強制執行它，則需要回退到互動式流（如`AcquireTokenInteractive`或`AcquireTokenByDeviceCode`）。|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010：在 */公共*或 */消費者*終結點上不支援授予類型。 請使用 */organizations* 或租用戶專屬端點。 您使用的是 */common*。| 如 Azure AD 的訊息所說明，授權單位必須有一個租用戶或 */organizations*。|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002：請求正文必須包含以下參數： `client_secret or client_assertion`.| 如果應用程式未在 Azure AD 中註冊為公共用戶端應用程式，則可以引發此異常。 在 Azure 門戶中，編輯應用程式的清單並將其設置為`allowPublicClient``true`。 |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`：無法識別登錄使用者| 庫無法查詢當前 Windows 登錄使用者，或者此使用者未 AD 或 AAD 加入（不支援工作位置加入的使用者）。 緩解 1：在 UWP 上，檢查應用程式是否具有以下功能：企業身份驗證、私人網路絡（用戶端和伺服器）、使用者帳戶資訊。 緩解 2：實現您自己的邏輯來獲取使用者名（例如），john@contoso.com並使用採用使用者名的`AcquireTokenByIntegratedWindowsAuth`表單。|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| 此方法需依賴由 Active Directory (AD) 公開的通訊協定。 如果使用者建立於不具 AD 支援 (「受控」使用者) 的 Azure Active Directory 中，此方法將會失敗。 使用者若建立於 AD 中，並且受 AAD 支援(「同盟」使用者)，則可受益於這種非互動式的驗證方法。 緩解：使用互動式身份驗證。|

### `MsalUiRequiredException`

調用`AcquireTokenSilent()`時從MSAL.NET返回的常見狀態碼之一是`MsalError.InvalidGrantError`。 此狀態碼意味著應用程式應再次調用身份驗證庫，但在交互模式下（公共用戶端應用程式獲取權杖交互或獲取權杖ByDeviceCodeFlow，並在 Web 應用程式中執行質詢）。 這是因為在頒發身份驗證權杖之前需要額外的使用者交互。

大多數時候`AcquireTokenSilent`失敗時，這是因為權杖緩存沒有與您的請求匹配的權杖。 訪問權杖將在 1 小時內過期，`AcquireTokenSilent`並嘗試基於刷新權杖獲取新權杖（在 OAuth2 術語中，這是"刷新權杖"流）。 此流也可能由於各種原因失敗，例如，如果租戶管理員配置更嚴格的登錄策略。 

交互的目的是讓使用者執行操作。 其中一些條件很容易被使用者解決（例如，只需按一下一下即可接受使用條款），有些條件無法使用當前配置解決（例如，有問題的電腦需要連接到特定的公司網路）。 有些説明使用者設置多重要素驗證，或在其設備上安裝 Microsoft 身份驗證器。

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException`分類枚舉

MSAL 公開一`Classification`個欄位，您可以讀取該欄位以提供更好的使用者體驗，例如告訴使用者其密碼已過期或他們需要提供使用某些資源的同意。 支援的值是枚舉的一`UiRequiredExceptionClassification`部分：

| 分類    | 意義           | 建議處理 |
|-------------------|-------------------|----------------------|
| 基本行動 | 條件可以通過使用者交互在互動式身份驗證流期間解決。 | 以對話模式調用獲取權杖（）。 |
| 附加操作 | 可以通過在互動式身份驗證流之外與系統進行額外的補救交互來解決條件。 | 以對話模式調用收購權杖（） 以顯示解釋補救措施的消息。 如果使用者不太可能完成補救措施，則調用應用程式可以選擇隱藏需要additional_action流。 |
| 僅消息      | 此時無法解決條件。 啟動互動式身份驗證流將顯示解釋該條件的消息。 | 以對話模式調用獲取權杖（） 以顯示解釋該條件的消息。 在使用者讀取消息並關閉視窗後，獲取權杖以對話模式（） 將返回使用者取消錯誤。 如果使用者不太可能從消息中受益，則調用應用程式可以選擇隱藏導致message_only的流。|
| 需要同意  | 使用者同意丟失或已被吊銷。 | 以對話模式致電獲取權杖（），以便使用者表示同意。 |
| 使用者密碼過期 | 使用者的密碼已過期。 | 以對話模式調用獲取權杖（），以便使用者可以重置其密碼。 |
| 提示永不失敗| 使用參數提示\從不調用互動式身份驗證，這迫使 MSAL 依賴于瀏覽器 Cookie，並且不顯示瀏覽器。 這已失敗。 | 無提示（）以對話模式調用獲取權杖（） |
| 獲取權杖靜音失敗 | MSAL SDK 沒有足夠的資訊從緩存中獲取權杖。 這可能是因為緩存中沒有權杖或找不到帳戶。 錯誤訊息具有更多詳細資訊。  | 以對話模式調用獲取權杖（）。 |
| None    | 不提供進一步的細節。 在互動式身份驗證流期間，可以通過使用者交互解決條件。 | 以對話模式調用獲取權杖（）。 |

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

## <a name="javascript"></a>[JAVAscript](#tab/javascript)

MSAL.js 提供錯誤物件，這些物件對不同類型的常見錯誤進行抽象和分類。 它還提供介面來訪問錯誤的特定詳細資訊，如錯誤訊息以適當地處理它們。

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
- `AuthError.message`： 與`errorMessage`相同。
- `AuthError.stack`：堆疊追蹤引發錯誤。

### <a name="error-types"></a>錯誤類型

可用的錯誤類型如下：

- `AuthError`：MSAL.js 庫的基本錯誤類，也用於意外錯誤。

- `ClientAuthError`：錯誤類，表示用戶端身份驗證出現問題。 來自程式庫的錯誤大多為 ClientAuthError。 這些錯誤是由於在登錄時調用登錄方法、使用者取消登錄等。

- `ClientConfigurationError`：錯誤類，在`ClientAuthError`給定使用者配置參數格式錯誤或缺失時發出請求之前擴展引發。

- `ServerError`：錯誤類，表示身份驗證服務器發送的錯誤字串。 這些可能是要求格式或參數無效之類的錯誤，或導致伺服器無法對使用者進行驗證或授權的任何其他錯誤。

- `InteractionRequiredAuthError`：錯誤類，擴展`ServerError`表示伺服器錯誤，這需要互動式調用。 如果使用者需要與伺服器交互`acquireTokenSilent`以提供憑據或同意身份驗證/授權，則引發此錯誤。 錯誤代碼包括`"interaction_required"``"login_required"`和`"consent_required"`。

對於使用重定向方法 （`loginRedirect`的 ）身份驗證流中`acquireTokenRedirect`的錯誤處理，您需要註冊回檔，在使用`handleRedirectCallback()`方法重定向後調用回檔，如下所示：

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

### <a name="errors-that-require-interaction"></a>需要交互的錯誤

當您嘗試使用非互動式方法獲取權杖（如`acquireTokenSilent`）時，將返回錯誤，但 MSAL 無法靜默地執行此操作。

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

在 Python 的 MSAL 中，大多數錯誤作為 API 呼叫的傳回值進行傳遞。 該錯誤表示為包含來自 Microsoft 標識平臺的 JSON 回應的字典。

* 成功的回應包含金鑰`"access_token"`。 回應的格式由 OAuth2 協定定義。 有關詳細資訊，請參閱[5.1 成功回應](https://tools.ietf.org/html/rfc6749#section-5.1)
* 錯誤回應包含`"error"`，通常`"error_description"`包含 。 回應的格式由 OAuth2 協定定義。 有關詳細資訊，請參閱[5.2 錯誤回應](https://tools.ietf.org/html/rfc6749#section-5.2)

返回錯誤時，金鑰包含人`"error_description"`可讀消息;當返回錯誤時，該鍵包含一條人類可讀的消息。這又通常包含一個 Microsoft 標識平臺錯誤代碼。 有關各種錯誤代碼的詳細資訊，請參閱[身份驗證和授權錯誤代碼](https://docs.microsoft.com/azure/active-directory/develop/reference-aadsts-error-codes)。

在 Python 的 MSAL 中，異常很少見，因為大多數錯誤都是通過返回錯誤值來處理的。 僅當`ValueError`嘗試如何使用庫出現問題時（例如 API 參數格式不正確時），才會引發異常。

## <a name="java"></a>[JAVA](#tab/java)

在 JAVA 的 MSAL 中，有三種類型的`MsalClientException`異常`MsalServiceException`：`MsalInteractionRequiredException`和 。所有繼承自`MsalException`。

- `MsalClientException`當發生庫或設備本地的錯誤時引發。
- `MsalServiceException`當安全權杖服務 （STS） 返回錯誤回應或其他網路錯誤時引發。
- `MsalInteractionRequiredException`當身份驗證成功需要 UI 交互時引發。

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException`公開向 STS 請求中返回的 HTTP 標頭。 通過`MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>Msal 交互必需例外

調用`AcquireTokenSilently()`時從 MSAL 返回的 JAVA 通用狀態`InvalidGrantError`代碼之一是 。 這意味著在頒發身份驗證權杖之前需要額外的使用者交互。 應用程式應再次調用身份驗證庫，但通過發送`AuthorizationCodeParameters`或`DeviceCodeParameters`用於公共用戶端應用程式在互動式模式下調用身份驗證庫。

大多數時候`AcquireTokenSilently`失敗時，這是因為權杖緩存沒有與您的請求匹配的權杖。 訪問權杖將在一小時後過期，並將`AcquireTokenSilently`嘗試基於刷新權杖獲取新權杖。 在 OAuth2 術語中，這是刷新權杖流。 此流也可能由於各種原因失敗，例如租戶管理員配置更嚴格的登錄策略時。

使用者很容易解決導致此錯誤的某些條件。 例如，他們可能需要接受使用條款。 或者，由於電腦需要連接到特定的公司網路，因此無法通過當前配置滿足請求。

MSAL 公開一`reason`個欄位，您可以使用該欄位提供更好的使用者體驗。 例如，該`reason`欄位可能會引導您告訴使用者其密碼已過期或他們需要提供使用某些資源的同意。 支援的值是枚舉的一`InteractionRequiredExceptionReason`部分：

| 原因 | 意義 | 推薦的處理 |
|---------|-----------|-----------------------------|
| `BasicAction` | 可在互動式身份驗證流期間通過使用者交互解決條件 | 使用`acquireToken`互動式參數調用 |
| `AdditionalAction` | 可以通過在互動式身份驗證流之外與系統進行額外的補救交互來解決條件。 | 使用`acquireToken`互動式參數調用以顯示說明要執行的補救措施的消息。 如果使用者不太可能完成補救措施，則調用應用可以選擇隱藏需要執行其他操作的流。 |
| `MessageOnly` | 此時無法解決條件。 啟動互動式身份驗證流以顯示解釋該條件的消息。 | 使用`acquireToken`互動式參數調用以顯示解釋條件的消息。 `acquireToken`將在使用者讀取`UserCanceled`消息並關閉視窗後返回錯誤。 如果使用者不太可能從郵件中獲益，應用可以選擇隱藏導致消息的流。 |
| `ConsentRequired`| 使用者同意丟失或已被吊銷。 |使用`acquireToken`互動式參數調用，以便使用者可以表示同意。 |
| `UserPasswordExpired` | 使用者的密碼已過期。 | 使用`acquireToken`互動式參數調用，以便使用者可以重置其密碼 |
| `None` |  詳情如下。 在互動式身份驗證流期間，可以通過使用者交互來解決此情況。 | 使用`acquireToken`互動式參數調用 |

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

iOS 和 macOS 錯誤的 MSAL 的完整清單列在[MSALError enum 中](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128)。

所有 MSAL 生成的錯誤都`MSALErrorDomain`隨域返回。

對於系統錯誤，MSAL 從系統`NSError`API 返回原始。 例如，如果權杖獲取因網路連接不足而失敗，MSAL 會返回`NSURLErrorDomain`域和`NSURLErrorNotConnectedToInternet`代碼的錯誤。

我們建議您在用戶端上至少處理以下兩個 MSAL 錯誤：

- `MSALErrorInteractionRequired`：使用者必須執行互動式請求。 有許多條件可能導致此錯誤，例如身份驗證會話過期或需要其他身份驗證要求。 調用 MSAL 互動式權杖獲取 API 進行恢復。 

- `MSALErrorServerDeclinedScopes`：部分或全部範圍被拒絕。 決定是僅繼續授予的範圍，還是停止登錄過程。

> [!NOTE]
> 枚`MSALInternalError`舉應僅用於引用和調試。 不要嘗試在運行時自動處理這些錯誤。 如果應用遇到屬於 的任何錯誤`MSALInternalError`，您可能需要顯示面向的泛型使用者消息，解釋發生了什麼。

例如，`MSALInternalErrorBrokerResponseNotReceived`表示使用者未完成身份驗證並手動返回到應用。 在這種情況下，你的應用應顯示一條通用錯誤訊息，解釋身份驗證未完成，並建議它們嘗試再次進行身份驗證。

以下 Objective-C 示例代碼演示了處理某些常見錯誤條件的最佳做法：

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

靜默獲取權杖時，當您嘗試訪問的 API 需要[條件訪問聲明（](../azuread-dev/conditional-access-dev-guide.md)如 MFA 策略）時，應用程式可能會收到錯誤。

處理此錯誤的模式是使用 MSAL 以對話模式獲取權杖。 以互動方式取得權杖時，系統會對使用者發出提示，讓他們有機會符合所需的條件式存取原則。

在某些情況下，當您呼叫需要條件式存取的 API 時，您可能會在 API 發出的錯誤中收到宣告挑戰。 例如，如果條件訪問策略要具有託管設備 （Intune），則錯誤將類似于[AADSTS53000：需要管理您的設備才能訪問此資源](reference-aadsts-error-codes.md)或類似內容。 在此情況下，您可以在取得權杖的呼叫中傳入宣告，讓系統提示使用者符合適當的原則。

### <a name="net"></a>.NET

從 MSAL.NET 呼叫需要條件式存取的 API 時，您的應用程式將必須處理宣告挑戰例外狀況。 此例外狀況會呈現為[宣告](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet)屬性非空白的 [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)。

要處理索賠質詢，您需要使用`.WithClaim()``PublicClientApplicationBuilder`類的方法。

### <a name="javascript"></a>JavaScript

使用 MSAL.js 以`acquireTokenSilent`靜默方式獲取權杖時，當您嘗試訪問的 API 需要[條件訪問聲明（](../azuread-dev/conditional-access-dev-guide.md)如 MFA 策略）時，應用程式可能會收到錯誤。

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

當您呼叫需要條件式存取的 API 時，您可能會在 API 發出的錯誤中收到宣告挑戰。 在這種情況下，您可以將錯誤中返回的聲明傳遞給`claimsRequest``AuthenticationParameters.ts`類的欄位以滿足相應的策略。 

有關詳細資訊[，請參閱請求其他索賠](active-directory-optional-claims.md)。

### <a name="msal-for-ios-and-macos"></a>適用於 iOS 和 macOS 的 MSAL

適用于 iOS 和 macOS 的 MSAL 允許您在互動式和靜默權杖採集方案中請求特定聲明。

要請求自訂聲明，請在`claimsRequest``MSALSilentTokenParameters`或`MSALInteractiveTokenParameters`中指定 。

有關詳細資訊[，請參閱使用 MSAL 請求自訂聲明以獲取 iOS 和 macOS。](request-custom-claims.md)

## <a name="retrying-after-errors-and-exceptions"></a>在發生錯誤和例外狀況後重試

在調用 MSAL 時，應實現您自己的重試策略。 MSAL 對 AAD 服務進行 HTTP 調用，並且偶爾會發生故障，例如網路可能會出現故障或伺服器超載。  

### <a name="http-error-codes-500-600"></a>HTTP 錯誤碼 500-600

針對 HTTP 錯誤碼為 500-600 的錯誤，MSAL.NET 會實作簡單的單次重試機制。

### <a name="http-429"></a>HTTP 429

當服務權杖伺服器 （STS） 滿載太多請求時，它將返回 HTTP 錯誤 429，提示在`Retry-After`回應欄位中重試多長時間。

### <a name="net"></a>.NET

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) `System.Net.Http.Headers.HttpResponseHeaders`異常作為屬性`namedHeaders`出現。 您可以使用錯誤代碼中的其他資訊來提高應用程式的可靠性。 在描述的情況下，可以使用`RetryAfterproperty`（類型`RetryConditionHeaderValue`） 並計算重試時間。

下面是使用用戶端憑據流的守護進程應用程式的示例。 您可以根據獲取權杖的任何方法來調整它。

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
