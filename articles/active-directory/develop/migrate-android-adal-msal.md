---
title: 適用于 Android 的 ADAL MSAL 遷移指南 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何將 Azure Active Directory Authentication Library (ADAL) Android 應用程式遷移至 Microsoft 驗證程式庫 (MSAL) 。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 10/14/2020
ms.author: marsma
ms.reviewer: shoatman
ms.custom: aaddev
ms.openlocfilehash: bf9b3a154e19fab08c46f9838f555e223f10e8a0
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672282"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>適用于 Android 的 ADAL 至 MSAL 遷移指南

本文強調您需要進行的變更，以將使用 Azure Active Directory Authentication Library (ADAL) 的應用程式遷移至使用 Microsoft 驗證程式庫 (MSAL) 。

## <a name="difference-highlights"></a>差異重點

ADAL 適用于 Azure Active Directory v1.0 端點。 Microsoft 驗證程式庫 (MSAL) 適用于 Microsoft 身分識別平臺（先前稱為 Azure Active Directory v2.0 端點）。 Microsoft 身分識別平臺與 Azure Active Directory v1.0 的不同之處在于：

支援：
  - 組織身分識別 (Azure Active Directory) 
  - 非組織身分識別，例如 Outlook.com、Xbox Live 等
  -  (只 Azure AD B2C Google、Facebook、Twitter 和 Amazon) 同盟登入

- 符合下列標準：
  - OAuth 2.0 版
  - OpenID Connect (OIDC) 

MSAL 公用 API 引進重要的變更，包括：

- 用來存取權杖的新模型：
  - ADAL 可透過表示伺服器的來存取權杖 `AuthenticationContext` 。 MSAL 可讓您透過 `PublicClientApplication` （代表用戶端）來存取權杖。 用戶端開發人員不需要為 `PublicClientApplication` 需要與之互動的每個授權單位建立新的實例。 只需要一項設定 `PublicClientApplication` 。
  - 除了資源識別碼之外，還支援使用範圍來要求存取權杖。
  - 支援累加式同意。 開發人員可以要求範圍，因為使用者會在應用程式中存取更多和更多功能，包括應用程式註冊期間未包含的範圍。
  - 在執行時間不會再驗證授權單位。 相反地，開發人員會在開發期間宣告「已知的授權單位」清單。
- 權杖 API 變更：
  - 在 ADAL 中， `AcquireToken()` 首先發出無訊息要求。 若失敗，則會提出互動式要求。 這種行為會導致某些開發人員只依賴 `AcquireToken` ，這會導致使用者意外提示輸入認證。 MSAL 需要開發人員刻意知道使用者何時收到 UI 提示。
    - `AcquireTokenSilent` 一律會產生成功或失敗的無訊息要求。
    - `AcquireToken` 一律會產生透過 UI 提示使用者的要求。
- MSAL 支援從預設瀏覽器或內嵌的 web 視圖登入：
  - 預設會使用裝置上的預設瀏覽器。 這可讓 MSAL 使用已有一或多個已登入之帳戶的驗證狀態 (cookie) 。 如果沒有任何驗證狀態，在授權期間透過 MSAL 進行驗證會導致驗證狀態 (cookie，) 為將在相同瀏覽器中使用之其他 web 應用程式的優點建立。
- 新的例外狀況模型：
  - 例外狀況更清楚地定義發生的錯誤類型，以及開發人員解決此問題所需採取的動作。
- MSAL 支援和呼叫的參數物件 `AcquireToken` `AcquireTokenSilent` 。
- MSAL 支援的宣告式設定：
  - 用戶端識別碼，重新導向 URI。
  - 內嵌 vs 預設瀏覽器
  - 當局
  - HTTP 設定，例如讀取和連接逾時

## <a name="your-app-registration-and-migration-to-msal"></a>您的應用程式註冊和遷移至 MSAL

您不需要變更現有的應用程式註冊，就能使用 MSAL。 如果您想要利用累加/漸進式同意，您可能需要檢查註冊，以找出您想要以累加方式要求的特定範圍。 有關範圍和累加式同意的詳細資訊，請遵循。

在入口網站的應用程式註冊中，您會看到 [ **API 許可權** ] 索引標籤。這會提供應用程式目前設定為要求存取權) 的 Api 和許可權清單 (範圍。 它也會顯示與每個 API 許可權相關聯的範圍名稱清單。

### <a name="user-consent"></a>使用者同意

使用 ADAL 和 Azure AD v1 端點時，使用者在第一次使用時，會授與他們所擁有之資源的同意。 透過 MSAL 和 Microsoft 身分識別平臺，您可以以累加方式要求同意。 累加式同意適用于使用者可能會考慮高許可權的許可權，如果未提供許可權的清楚說明，則可能會有問題。 在 ADAL 中，這些許可權可能會導致使用者放棄登入您的應用程式。

> [!TIP]
> 建議您在需要為您的使用者提供其他內容，以瞭解您的應用程式需要許可權的情況下，使用累加式同意。

### <a name="admin-consent"></a>系統管理員同意

組織系統管理員可以同意您的應用程式代表其組織的所有成員所需的許可權。 某些組織只允許系統管理員同意應用程式。 系統管理員同意需要您在應用程式註冊中包含應用程式所使用的所有 API 許可權和範圍。

> [!TIP]
> 雖然您可以使用 MSAL 來要求範圍，但不包含在您的應用程式註冊中，但建議您更新應用程式註冊，以包含使用者可授與許可權的所有資源和範圍。

## <a name="migrating-from-resource-ids-to-scopes"></a>從資源識別碼遷移至範圍

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>第一次使用時，驗證並要求擁有權限的授權

如果您目前使用 ADAL，而不需要使用累加式同意，開始使用 MSAL 最簡單的方式就是 `acquireToken` 使用新的物件提出要求 `AcquireTokenParameter` ，並設定資源識別碼值。

> [!CAUTION]
> 不可能同時設定範圍和資源識別碼。嘗試設定兩者都會產生 `IllegalArgumentException` 。

這會導致您所使用的 v1 行為相同。 在您的應用程式註冊中要求的擁有權限都是在使用者第一次互動時要求的。

### <a name="authenticate-and-request-permissions-only-as-needed"></a>只在必要時驗證和要求許可權

若要利用累加式同意，請從您的應用程式註冊 (範圍) 的許可權清單，並根據下列專案將其組織成兩個清單：

- 當使用者在登入期間第一次與您的應用程式互動時，您想要要求的範圍。
- 與您的應用程式重要功能相關聯的許可權，您也必須向使用者說明這些許可權。

組織範圍之後，請依照您想要要求權杖的資源 (API) 來組織每個清單。 以及任何其他您希望使用者同時授權的範圍。

用來向 MSAL 提出要求的 parameters 物件支援：

- `Scope`：您想要要求授權並接收存取權杖的範圍清單。
- `ExtraScopesToConsent`：當您要求其他資源的存取權杖時，您想要要求授權的其他範圍清單。 這份範圍清單可讓您將要求使用者授權所需的次數降到最低。 這表示較少的使用者授權或同意提示。

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>從 AuthenticationCoNtext 遷移至 PublicClientApplications

### <a name="constructing-publicclientapplication"></a>建立 PublicClientApplication

當您使用 MSAL 時，會具現化 `PublicClientApplication` 。 此物件會建立應用程式身分識別的模型，並用來對一或多個授權單位提出要求。 使用這個物件時，您將會設定用戶端身分識別、重新導向 URI、預設授權單位、是否要使用裝置瀏覽器與內嵌的 web view、記錄層級等等。

您可以使用 JSON，以宣告方式設定此物件，您可以在 APK 中以資源的形式提供作為檔案或存放區。

雖然這個物件不是單一的，但它會在內部 `Executors` 針對互動式和無訊息要求使用 shared。

### <a name="business-to-business"></a>企業對企業

在 ADAL 中，您要求存取權杖的每個組織都需要個別的實例 `AuthenticationContext` 。 在 MSAL 中，這不再是必要條件。 您可以指定您想要在無訊息或互動式要求中要求權杖的授權單位。

### <a name="migrate-from-authority-validation-to-known-authorities"></a>從授權單位驗證遷移至已知授權單位

MSAL 沒有啟用或停用授權驗證的旗標。 授權驗證是 ADAL 中的一項功能，在舊版的 MSAL 中，可防止您的程式碼要求可能有惡意授權的權杖。 MSAL 現在會抓取 Microsoft 已知的授權清單，並將該清單與您在設定中指定的授權單位合併。

> [!TIP]
> 如果您是 Azure 企業對消費者 (B2C) 使用者，這表示您不再需要停用授權驗證。 相反地，請將每個支援的 Azure AD B2C 原則納入 MSAL 設定中的授權單位。

如果您嘗試使用 Microsoft 不知道的授權單位，而且不包含在您的設定中，您將會收到 `UnknownAuthorityException` 。

### <a name="logging"></a>記錄
您現在可以在設定過程中，以宣告方式設定記錄，如下所示：

```json
"logging": {
  "pii_enabled": false,
  "log_level": "WARNING",
  "logcat_enabled": true
}
```

## <a name="migrate-from-userinfo-to-account"></a>從使用者帳戶遷移至帳戶

在 ADAL 中，會 `AuthenticationResult` 提供 `UserInfo` 物件，用來取得已驗證之帳戶的相關資訊。 「使用者」一詞是指「使用者」，也就是使用「人」或「軟體代理程式」所套用的方式，使得某些應用程式支援單一使用者 (的人或軟體代理程式) 是否有多個帳戶。

請考慮銀行帳戶。 您在多個金融機構可能會有一個以上的帳戶。 當您開啟帳戶時，您 (使用者) 會發出用來存取每個帳戶餘額、帳單付款等的認證，例如 ATM 卡 & PIN。 這些認證只能在發出它們的金融機構使用。

就像金融機構的帳戶一樣，Microsoft 身分識別平臺中的帳戶是使用認證來存取。 這些認證可能會向 Microsoft 註冊或發行。 或由 Microsoft 代表組織。

Microsoft 身分識別平臺與金融機構不同之處在于，Microsoft 身分識別平臺所提供的架構，可讓使用者使用一個帳戶與其相關聯的認證，來存取屬於多位個人和組織的資源。 這就像是能夠使用一個銀行所發行的卡片，還有另一個金融機構。 這是因為所有有問題的組織都使用 Microsoft 身分識別平臺，可讓您在多個組織中使用一個帳戶。 以下是範例：

Sam 適用于 Contoso.com，但會管理屬於 Fabrikam.com 的 Azure 虛擬機器。 為了讓 Sam 管理 Fabrikam 的虛擬機器，他必須獲得授權才能存取。 您可以藉由將 Sam 的帳戶新增至 Fabrikam.com，並授與帳戶可讓他使用虛擬機器的角色，來授與此存取權。 這會透過 Azure 入口網站完成。

將 Sam 的 Contoso.com 帳戶新增為 Fabrikam.com 的成員，會導致在 Fabrikam 的 Azure Active Directory 中建立新記錄。 Sam 在 Azure Active Directory 中的記錄稱為使用者物件。 在此情況下，該使用者物件會指向 Sam 在 Contoso.com 中的使用者物件。 Sam 的 Fabrikam user 物件是 Sam 的本機標記法，它會用來在 Fabrikam.com 的內容中儲存與 Sam 相關聯的帳戶資訊。 在 Contoso.com 中，Sam 的職稱是資深 DevOps 顧問。 在 Fabrikam 中，Sam 的標題是 Contractor-Virtual 機。 在 Contoso.com 中，Sam 不負責管理虛擬機器，也不會獲得授權。 在 Fabrikam.com 中，這是他唯一的工作功能。 但 Sam 仍只會有一組認證來追蹤，這些是 Contoso.com 所發行的認證。

一旦成功 `acquireToken` 呼叫之後，您就會看到 `IAccount` 可在後續要求中使用之物件的參考 `acquireTokenSilent` 。

### <a name="imultitenantaccount"></a>IMultiTenantAccount

如果您的應用程式會從代表帳戶的每個租使用者存取有關帳戶的宣告，您可以 `IAccount` 將物件轉換為 `IMultiTenantAccount` 。 此介面提供 `ITenantProfiles` 依租使用者識別碼的對應，可讓您在每個要求權杖的租使用者（相對於目前的帳戶）中存取屬於帳戶的宣告。

位於根目錄的宣告 `IAccount` `IMultiTenantAccount` 一律會包含來自 home 租使用者的宣告。 如果您尚未對 home 租使用者中的權杖提出要求，此集合將會是空的。

## <a name="other-changes"></a>其他變更

### <a name="use-the-new-authenticationcallback"></a>使用新的 >authenticationcallback

```java
// Existing ADAL Interface
public interface AuthenticationCallback<T> {

    /**
     * This will have the token info.
     *
     * @param result returns <T>
     */
    void onSuccess(T result);

    /**
     * Sends error information. This can be user related error or server error.
     * Cancellation error is AuthenticationCancelError.
     *
     * @param exc return {@link Exception}
     */
    void onError(Exception exc);
}
```

```java
// New Interface for Interactive AcquireToken
public interface AuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException}
     */
    void onError(final MsalException exception);

    /**
     * Will be called if user cancels the flow.
     */
    void onCancel();
}

// New Interface for Silent AcquireToken
public interface SilentAuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException} or
     *                  {@link MsalUiRequiredException}.
     */
    void onError(final MsalException exception);
}
```

## <a name="migrate-to-the-new-exceptions"></a>遷移至新的例外狀況

在 ADAL 中，有一種例外狀況， `AuthenticationException` 其中包含用來取得 `ADALError` 列舉值的方法。
在 MSAL 中，有例外狀況的階層，每個都有一組相關的特定錯誤碼。

| 例外狀況                                        | 描述                                                         |
|--------------------------------------------------|---------------------------------------------------------------------|
| `MsalArgumentException`                          | 如果有一或多個輸入引數無效，則擲回。                 |
| `MsalClientException`                            | 如果錯誤為用戶端，則擲回。                                 |
| `MsalDeclinedScopeException`                     | 如果伺服器拒絕一或多個要求的範圍，則會擲回。 |
| `MsalException`                                  | MSAL 擲回的預設核取例外狀況。                           |
| `MsalIntuneAppProtectionPolicyRequiredException` | 如果資源已啟用 MAMCA 保護原則則擲回。         |
| `MsalServiceException`                           | 如果錯誤為伺服器端，則擲回。                                 |
| `MsalUiRequiredException`                        | 如果無法以無訊息方式重新整理權杖，則會擲回。                    |
| `MsalUserCancelException`                        | 如果使用者取消驗證流程，則擲回。                |

### <a name="adalerror-to-msalexception-translation"></a>ADALError 至 MsalException 轉譯

| 如果您要在 ADAL 中攔截這些錯誤 .。。  | ...攔截這些 MSAL 例外狀況：                                                         |
|--------------------------------------------------|---------------------------------------------------------------------|
| *沒有對等的 ADALError* | `MsalArgumentException`                          |
| <ul><li>`ADALError.ANDROIDKEYSTORE_FAILED`<li>`ADALError.AUTH_FAILED_USER_MISMATCH`<li>`ADALError.DECRYPTION_FAILED`<li>`ADALError.DEVELOPER_AUTHORITY_CAN_NOT_BE_VALIDED`<li>`ADALError.EVELOPER_AUTHORITY_IS_NOT_VALID_INSTANCE`<li>`ADALError.DEVELOPER_AUTHORITY_IS_NOT_VALID_URL`<li>`ADALError.DEVICE_CONNECTION_IS_NOT_AVAILABLE`<li>`ADALError.DEVICE_NO_SUCH_ALGORITHM`<li>`ADALError.ENCODING_IS_NOT_SUPPORTED`<li>`ADALError.ENCRYPTION_ERROR`<li>`ADALError.IO_EXCEPTION`<li>`ADALError.JSON_PARSE_ERROR`<li>`ADALError.NO_NETWORK_CONNECTION_POWER_OPTIMIZATION`<li>`ADALError.SOCKET_TIMEOUT_EXCEPTION`</ul> | `MsalClientException`                            |
| *沒有對等的 ADALError* | `MsalDeclinedScopeException`                     |
| <ul><li>`ADALError.APP_PACKAGE_NAME_NOT_FOUND`<li>`ADALError.BROKER_APP_VERIFICATION_FAILED`<li>`ADALError.PACKAGE_NAME_NOT_FOUND`</ul> | `MsalException`                                  |
| *沒有對等的 ADALError* | `MsalIntuneAppProtectionPolicyRequiredException` |
| <ul><li>`ADALError.SERVER_ERROR`<li>`ADALError.SERVER_INVALID_REQUEST`</ul> | `MsalServiceException`                           |
| <ul><li>`ADALError.AUTH_REFRESH_FAILED_PROMPT_NOT_ALLOWED` | `MsalUiRequiredException`</ul>                        |
| *沒有對等的 ADALError* | `MsalUserCancelException`                        |

### <a name="adal-logging-to-msal-logging"></a>ADAL 記錄至 MSAL 記錄

```java
// Legacy Interface
    StringBuilder logs = new StringBuilder();
    Logger.getInstance().setExternalLogger(new ILogger() {
            @Override
            public void Log(String tag, String message, String additionalMessage, LogLevel logLevel, ADALError errorCode) {
                logs.append(message).append('\n');
            }
        });
```

```java
// New interface
  StringBuilder logs = new StringBuilder();
  Logger.getInstance().setExternalLogger(new ILoggerCallback() {
      @Override
      public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII) {
          logs.append(message).append('\n');
      }
  });

// New Log Levels:
public enum LogLevel
{
    /**
     * Error level logging.
     */
    ERROR,
    /**
     * Warning level logging.
     */
    WARNING,
    /**
     * Info level logging.
     */
    INFO,
    /**
     * Verbose level logging.
     */
    VERBOSE
}
```
