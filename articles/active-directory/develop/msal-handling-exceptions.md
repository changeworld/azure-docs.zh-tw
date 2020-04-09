---
title: 錯誤與例外 (MSAL)
titleSuffix: Microsoft identity platform
description: 瞭解如何處理 MSAL 應用程式中的錯誤和異常、條件訪問和聲明挑戰。
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884013"
---
# <a name="handle-msal-exceptions-and-errors"></a>處理 MSAL 例外和錯誤

本文概述了不同類型的錯誤以及處理常見登錄錯誤的建議。

## <a name="msal-error-handling-basics"></a>MSAL 錯誤處理基礎知識

Microsoft 身份驗證庫 (MSAL) 中的異常適用於應用開發人員進行故障排除,而不是用於向最終用戶顯示。 例外狀況訊息不會當地語系化。

在處理例外狀況和錯誤時，您可以使用例外狀況類型本身和錯誤碼來區別例外狀況。  如需錯誤碼清單，請參閱[驗證與授權錯誤碼](reference-aadsts-error-codes.md)。

在登錄體驗期間,可能會遇到有關同意、條件訪問(MFA、設備管理、基於位置的限制)、令牌頒發和兌換以及使用者屬性的錯誤。

有關應用錯誤處理的更多詳細資訊,請參閱以下與所使用的語言相匹配的部分。

## <a name="net"></a>[.NET](#tab/dotnet)

處理 .NET 異常時,可以使用異常類型`ErrorCode`本身和 成員來區分異常。 `ErrorCode`值是[MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet)類型的常量。

您還可以檢視[MsalClientexception、MsalServiceexception](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet)和[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)[MsalUI"例外'欄位](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet)。

如果引發[MsalServiceException,](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)請試著[驗證和授權錯誤代碼](reference-aadsts-error-codes.md)以檢視程式碼是否列出。

### <a name="common-net-exceptions"></a>常見的 .NET 異常

以下是可能引發的常見異常和一些可能的緩解措施:  

| 例外狀況 | 錯誤碼 | 降低|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001:使用者或管理員未同意將應用程式與 ID"{appId}"稱為"[appName]"使用。 請傳送此使用者和資源的互動式授權要求。| 您必須先取得使用者同意。 如果您不使用 .NET Core(沒有任何 Web UI),請呼叫(僅限`AcquireTokeninteractive`一次)。 如果您正在使用 .NET 內核或不想`AcquireTokenInteractive`執行, 使用者可以瀏覽到網`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read`址 來同意: 。 致電`AcquireTokenInteractive`:`app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079:使用者需要使用多重身份驗證 (MFA)。| 沒有緩解。 如果為租戶配置了 MFA,Azure 活動目錄 (AAD) 決定強制執行它,則需要回退到互動`AcquireTokenInteractive`式`AcquireTokenByDeviceCode`流(如或 )。|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010:在 */公共*或 */使用者*終結點上不支援授予類型。 請使用 */organizations* 或租用戶專屬端點。 您使用的是 */common*。| 如 Azure AD 的訊息所說明，授權單位必須有一個租用戶或 */organizations*。|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002:請求正文必須包含以下參數: `client_secret or client_assertion`.| 如果應用程式未在 Azure AD 中註冊為公共用戶端應用程式,則可以引發此異常。 在 Azure 門戶中,編輯應用程式的清單並將其`allowPublicClient``true`設定為 。 |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`:無法識別登錄使用者| 庫無法查詢當前 Windows 登錄使用者,或者此使用者未 AD 或 AAD 加入(不支援工作位置加入的使用者)。 緩解 1:在 UWP 上,檢查應用程式是否具有以下功能:企業身份驗證、專用網路(用戶端和伺服器)、使用者帳戶資訊。 緩解 2:實現您自己的邏輯來獲取使用者名(例如),john@contoso.com並使用採用使用者`AcquireTokenByIntegratedWindowsAuth`名的 窗體。|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| 此方法需依賴由 Active Directory (AD) 公開的通訊協定。 如果使用者建立於不具 AD 支援 (「受控」使用者) 的 Azure Active Directory 中，此方法將會失敗。 使用者若建立於 AD 中，並且受 AAD 支援(「同盟」使用者)，則可受益於這種非互動式的驗證方法。 緩解:使用互動式身份驗證。|

### `MsalUiRequiredException`

呼叫`AcquireTokenSilent()`時從MSAL.NET傳回的常見狀態代碼之`MsalError.InvalidGrantError`一是 。 此狀態代碼意味著應用程式應再次調用身份驗證庫,但在交互模式下(公共用戶端應用程式獲取權杖互動或獲取權杖ByDeviceCodeFlow,並在Web應用程式中執行質詢)。 這是因為在頒發身份驗證權杖之前需要額外的使用者互動。

大多數時候`AcquireTokenSilent`失敗時,這是因為令牌緩存沒有與您的請求匹配的令牌。 造訪權碼將在 1 小時內`AcquireTokenSilent`過期, 並嘗試基於刷新權杖獲取新權杖(在 OAuth2 術語中,這是「刷新權杖」 。 此流也可能由於各種原因失敗,例如,如果租戶管理員配置更嚴格的登錄策略。 

互動的目的是讓使用者執行操作。 其中一些條件很容易被用戶解決(例如,只需單擊一下即可接受使用條款),有些條件無法使用當前配置解決(例如,有問題的計算機需要連接到特定的公司網路)。 有些幫助用戶設置多重身份驗證,或在其設備上安裝 Microsoft 身份驗證器。

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException`分類枚舉

MSAL 公開`Classification`一 個字段,您可以讀取該欄位以提供更好的使用者體驗,例如告訴使用者其密碼已過期或他們需要提供使用某些資源的同意。 支援的值是枚舉的一`UiRequiredExceptionClassification`部分:

| 分類    | 意義           | 建議處理 |
|-------------------|-------------------|----------------------|
| 基本行動 | 條件可以通過使用者交互在互動式身份驗證流期間解決。 | 以交互方式調用獲取權杖()。 |
| 附加作業 | 可以通過在互動式身份驗證流之外與系統進行額外的補救交互來解決條件。 | 以交互方式調用收購權杖() 以顯示解釋補救措施的消息。 如果使用者不太可能完成補救措施,則調用應用程式可以選擇隱藏需要additional_action流。 |
| 只訊息      | 此時無法解決條件。 啟動互動式身份驗證流將顯示解釋該條件的消息。 | 以交互方式調用獲取權杖() 以顯示解釋該條件的消息。 在使用者讀取消息並關閉視窗后,獲取權杖以互動方式() 將返回使用者取消錯誤。 如果使用者不太可能從消息中受益,則調用應用程式可以選擇隱藏導致message_only的流。|
| 需要同意  | 使用者同意丟失或已被吊銷。 | 以交互方式致電獲取權杖(),以便使用者表示同意。 |
| 使用者密碼過期 | 用戶的密碼已過期。 | 以交互方式調用獲取權杖(),以便用戶可以重置其密碼。 |
| 提示永不失敗| 使用參數提示\從不調用互動式身份驗證,這迫使 MSAL 依賴於瀏覽器 Cookie,並且不顯示瀏覽器。 這已失敗。 | 沒有提示()以互動方式呼叫取得權杖() |
| 取得權杖靜音失敗 | MSAL SDK 沒有足夠的資訊從快取中獲取權杖。 這可能是因為緩存中沒有權杖或找不到帳戶。 錯誤消息具有更多詳細資訊。  | 以交互方式調用獲取權杖()。 |
| None    | 不提供進一步的細節。 在互動式身份驗證流期間,可以通過使用者交互解決條件。 | 以交互方式調用獲取權杖()。 |

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

MSAL.js 提供錯誤對象,這些物件對不同類型的常見錯誤進行抽象和分類。 它還提供介面來訪問錯誤的特定詳細資訊,如錯誤消息以適當地處理它們。

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
- `AuthError.message`:`errorMessage`與相同。
- `AuthError.stack`:堆疊跟蹤引發錯誤。

### <a name="error-types"></a>錯誤類型

可用的錯誤類型如下：

- `AuthError`:MSAL.js 庫的基本錯誤類,也用於意外錯誤。

- `ClientAuthError`:錯誤類,表示客戶端身份驗證出現問題。 來自程式庫的錯誤大多為 ClientAuthError。 這些錯誤是由於在登錄時調用登錄方法、使用者取消登錄等。

- `ClientConfigurationError`:錯誤類,在`ClientAuthError`給定使用者配置參數格式錯誤或缺失時發出請求之前擴展引發。

- `ServerError`:錯誤類,表示身份驗證伺服器發送的錯誤字串。 這些可能是要求格式或參數無效之類的錯誤，或導致伺服器無法對使用者進行驗證或授權的任何其他錯誤。

- `InteractionRequiredAuthError`:錯誤類,擴展`ServerError`表示伺服器錯誤,這需要互動式調用。 如果使用者需要與伺服器交互`acquireTokenSilent`以提供憑據或同意身份驗證/授權,則引發此錯誤。 錯誤代碼包括`"interaction_required"``"login_required"`與`"consent_required"`。

對於使用重定向方法(`loginRedirect`的 )身份驗證`acquireTokenRedirect`串流中的錯誤處理,您需要註冊回檔`handleRedirectCallback()`,在使用方法重定向後呼叫回調,如下所示:

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

當您嘗試使用非互動式方法獲取權杖(如`acquireTokenSilent`)時,將返回錯誤,但 MSAL 無法靜默地執行此操作。

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

在 Python 的 MSAL 中,大多數錯誤作為 API 呼叫的傳回值進行傳遞。 該錯誤表示為包含來自 Microsoft 標識平臺的 JSON 回應的字典。

* 成功的回應包含金鑰`"access_token"`。 回應的格式由 OAuth2 協定定義。 有關詳細資訊,請參閱[5.1 成功回應](https://tools.ietf.org/html/rfc6749#section-5.1)
* 錯誤回應包含`"error"`,`"error_description"`通常包含 。 回應的格式由 OAuth2 協定定義。 有關詳細資訊,請參閱[5.2 錯誤回應](https://tools.ietf.org/html/rfc6749#section-5.2)

返回錯誤時,密鑰包含人`"error_description"`可讀消息;當返回錯誤時,該鍵包含一條人類可讀的消息。這又通常包含一個 Microsoft 標識平台錯誤代碼。 有關各種錯誤代碼的詳細資訊,請參閱[身份認證與授權錯誤代碼](https://docs.microsoft.com/azure/active-directory/develop/reference-aadsts-error-codes)。

在 Python 的 MSAL 中,異常很少見,因為大多數錯誤都是通過返回錯誤值來處理的。 僅當`ValueError`嘗試如何使用庫出現問題時(例如 API 參數格式不正確時),才會引發異常。

## <a name="java"></a>[Java](#tab/java)

在 JAVA 的 MSAL 中,有`MsalClientException`三`MsalServiceException`種`MsalInteractionRequiredException`類型的例外 : 和 。所有繼承自`MsalException`。

- `MsalClientException`當發生庫或設備本地的錯誤時引發。
- `MsalServiceException`當安全令牌服務 (STS) 傳回錯誤回應或其他網路錯誤時引發。
- `MsalInteractionRequiredException`當身份驗證成功需要 UI 互動時引發。

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException`公開向 STS 請求中返回的 HTTP 標頭。 通過`MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>Msal 互動必需例外

呼叫`AcquireTokenSilently()`時從 MSAL 傳回的`InvalidGrantError`Java 通用狀態 碼之一是 。 這意味著在頒發身份驗證權杖之前需要額外的使用者互動。 應用程式應再次調用身份驗證庫,但通過發送`AuthorizationCodeParameters``DeviceCodeParameters`或用於公共用戶端應用程式在互動式模式下調用身份驗證庫。

大多數時候`AcquireTokenSilently`失敗時,這是因為令牌緩存沒有與您的請求匹配的令牌。 訪問權杖將在一小時後過期,並將`AcquireTokenSilently`嘗試基於刷新權杖獲取新權杖。 在 OAuth2 術語中,這是刷新令牌流。 此流也可能由於各種原因失敗,例如租戶管理員配置更嚴格的登錄策略時。

使用者很容易解決導致此錯誤的某些條件。 例如,他們可能需要接受使用條款。 或者,由於計算機需要連接到特定的公司網路,因此無法通過當前配置滿足請求。

MSAL 公開`reason`一 個字段,您可以使用該欄位提供更好的用戶體驗。 例如,該`reason`欄位可能會引導您告訴使用者其密碼已過期或他們需要提供使用某些資源的同意。 支援的值是枚舉的一`InteractionRequiredExceptionReason`部分:

| 原因 | 意義 | 推薦的處理 |
|---------|-----------|-----------------------------|
| `BasicAction` | 可在互動式身分驗證串流期間透過使用者互動解決條件 | 使用`acquireToken`互動式參數呼叫 |
| `AdditionalAction` | 可以通過在互動式身份驗證流之外與系統進行額外的補救交互來解決條件。 | 使用`acquireToken`互動式參數調用以顯示說明要執行的補救措施的消息。 如果使用者不太可能完成補救措施,則調用應用可以選擇隱藏需要執行其他操作的流。 |
| `MessageOnly` | 此時無法解決條件。 啟動互動式身份驗證流以顯示解釋該條件的消息。 | 使用`acquireToken`互動式參數調用以顯示解釋條件的消息。 `acquireToken`將在使用者讀取`UserCanceled`消息並關閉視窗後返回錯誤。 如果使用者不太可能從郵件中獲益,應用可以選擇隱藏導致消息的流。 |
| `ConsentRequired`| 使用者同意丟失或已被吊銷。 |使用`acquireToken`互動式參數調用,以便使用者可以表示同意。 |
| `UserPasswordExpired` | 用戶的密碼已過期。 | 使用`acquireToken`互動式參數呼叫,以便使用者可以重置其密碼 |
| `None` |  詳情如下。 在互動式身份驗證流期間,可以通過使用者交互來解決此情況。 | 使用`acquireToken`互動式參數呼叫 |

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

iOS 與 macOS 錯誤的 MSAL 的完整清單列在[MSALError enum 中](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128)。

所有 MSAL 生成的`MSALErrorDomain`錯誤都 隨域返回。

對於系統錯誤,MSAL 從`NSError`系統 API 返回原始。 例如,如果令牌獲取因網路連接不足而失敗,MSAL 會`NSURLErrorDomain`返回網`NSURLErrorNotConnectedToInternet`域和代碼的錯誤。

我們建議您在用戶端上至少處理以下兩個 MSAL 錯誤:

- `MSALErrorInteractionRequired`:用戶必須執行互動式請求。 有許多條件可能導致此錯誤,例如身份驗證會話過期或需要其他身份驗證要求。 調用 MSAL 互動式權杖獲取 API 進行恢復。 

- `MSALErrorServerDeclinedScopes`:部分或全部範圍被拒絕。 決定是僅繼續授予的範圍,還是停止登錄過程。

> [!NOTE]
> 枚`MSALInternalError`舉應僅用於引用和調試。 不要嘗試在運行時自動處理這些錯誤。 如果應用遇到屬於的任何錯誤`MSALInternalError`,您可能需要顯示面向的泛型使用者消息,解釋發生了什麼。

例如,`MSALInternalErrorBrokerResponseNotReceived`表示使用者未完成身份驗證並手動返回到應用。 在這種情況下,你的應用應顯示一條通用錯誤消息,解釋身份驗證未完成,並建議它們嘗試再次進行身份驗證。

以下 Objective-C 範例碼展示了處理某些常見錯誤條件的最佳做法:

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

靜默獲取權杖時,當您嘗試存取的 API 需要[條件存取聲明(](../azuread-dev/conditional-access-dev-guide.md)如 MFA 策略)時,應用程式可能會收到錯誤。

處理此錯誤的模式是使用 MSAL 以互動方式獲取權杖。 以互動方式取得權杖時，系統會對使用者發出提示，讓他們有機會符合所需的條件式存取原則。

在某些情況下，當您呼叫需要條件式存取的 API 時，您可能會在 API 發出的錯誤中收到宣告挑戰。 例如,如果條件存取策略要具有託管設備 (Intune),則錯誤將類似於[AADSTS53000:需要管理您的設備才能存取此資源](reference-aadsts-error-codes.md)或類似內容。 在此情況下，您可以在取得權杖的呼叫中傳入宣告，讓系統提示使用者符合適當的原則。

### <a name="net"></a>.NET

從 MSAL.NET 呼叫需要條件式存取的 API 時，您的應用程式將必須處理宣告挑戰例外狀況。 此例外狀況會呈現為[宣告](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet)屬性非空白的 [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)。

要處理索賠質詢,您需要使用`.WithClaim()``PublicClientApplicationBuilder`類的方法。

### <a name="javascript"></a>JavaScript

使用 MSAL.js`acquireTokenSilent`以 靜默方式獲取權杖時,當您嘗試存取的 API 需要[條件存取聲明(](../azuread-dev/conditional-access-dev-guide.md)如 MFA 策略)時,應用程式可能會收到錯誤。

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

當您呼叫需要條件式存取的 API 時，您可能會在 API 發出的錯誤中收到宣告挑戰。 在這種情況下,您可以將錯誤中返回的聲明傳遞給`claimsRequest``AuthenticationParameters.ts`類的欄位以滿足相應的策略。 

有關詳細資訊[,請參閱請求其他索賠](active-directory-optional-claims.md)。

### <a name="msal-for-ios-and-macos"></a>適用於 iOS 和 macOS 的 MSAL

適用於 iOS 和 macOS 的 MSAL 允許您在互動式和靜默權杖採集方案中請求特定聲明。

要要求自訂聲明,請在`claimsRequest``MSALSilentTokenParameters`或`MSALInteractiveTokenParameters`指定 。

有關詳細資訊[,請參閱使用 MSAL 請求自定義聲明以獲取 iOS 和 macOS。](request-custom-claims.md)

## <a name="retrying-after-errors-and-exceptions"></a>在發生錯誤和例外狀況後重試

在呼叫 MSAL 時,應實現您自己的重試策略。 MSAL 對 AAD 服務進行 HTTP 呼叫,並且偶爾會發生故障,例如網路可能會出現故障或伺服器過載。  

### <a name="http-error-codes-500-600"></a>HTTP 錯誤碼 500-600

針對 HTTP 錯誤碼為 500-600 的錯誤，MSAL.NET 會實作簡單的單次重試機制。

### <a name="http-429"></a>HTTP 429

當服務令牌伺服器 (STS) 滿載太多請求時,它將返回 HTTP 錯誤 429,提示在`Retry-After`回應欄位中重試多長時間。

### <a name="net"></a>.NET

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)`System.Net.Http.Headers.HttpResponseHeaders`異常作為`namedHeaders`屬性 出現。 您可以使用錯誤代碼中的其他資訊來提高應用程式的可靠性。 在描述的情況下,可以使用`RetryAfterproperty`(`RetryConditionHeaderValue`類型 ) 並計算重試時間。

下面是使用客戶端憑據流的守護程序應用程式的範例。 您可以根據獲取權杖的任何方法來調整它。

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
