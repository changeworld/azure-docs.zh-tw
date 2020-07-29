---
title: 適用于 Android 的 MSAL 遷移指南的 ADAL |Azure
description: 瞭解如何將您的 Azure Active Directory Authentication Library （ADAL） Android 應用程式遷移至 Microsoft 驗證程式庫（MSAL）。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 09/6/2019
ms.author: marsma
ms.reviewer: shoatman
ms.custom: aaddev
ms.openlocfilehash: 21866bb7dab3d5a093ffc4655161b80853eadfc5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77084048"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>適用于 Android 的 MSAL 遷移指南的 ADAL

本文重點說明您需要進行的變更，以遷移使用 Azure Active Directory Authentication Library （ADAL）的應用程式來使用 Microsoft Authentication Library （MSAL）。

## <a name="difference-highlights"></a>差異重點

ADAL 適用于 Azure Active Directory v1.0 端點。 Microsoft 驗證程式庫（MSAL）適用于 Microsoft 身分識別平臺，先前稱為 Azure Active Directory v2.0 端點。 Microsoft 身分識別平臺與 Azure Active Directory v1.0 的不同之處在于：

支援：
  - 組織身分識別（Azure Active Directory）
  - 非組織身分識別，例如 Outlook.com、Xbox Live 等等
  - （僅限 B2C）使用 Google、Facebook、Twitter 和 Amazon 的同盟登入

- 標準與相容：
  - OAuth v2。0
  - OpenID Connect （OIDC）

MSAL 公用 API 引進重要的變更，包括：

- 用於存取權杖的新模型：
  - ADAL 可讓您透過代表伺服器的來存取權杖 `AuthenticationContext` 。 MSAL 可讓您透過代表用戶端的來存取權杖 `PublicClientApplication` 。 用戶端開發人員不需要為 `PublicClientApplication` 他們需要與之互動的每個授權單位建立新的實例。 只需要一個設定 `PublicClientApplication` 。
  - 除了資源識別碼之外，還支援使用範圍來要求存取權杖。
  - 支援累加式同意。 開發人員可以要求範圍，因為使用者會在應用程式中存取更多的功能，包括在應用程式註冊期間未包含的功能。
  - 授權單位不會再于執行時間進行驗證。 相反地，開發人員會在開發期間宣告「已知授權單位」清單。
- 權杖 API 變更：
  - 在 ADAL 中， `AcquireToken()` 會先發出無訊息要求。 失敗了，它會提出互動式要求。 這種行為會導致某些開發人員僅依賴 `AcquireToken` ，而導致使用者意外地收到認證的提示。 MSAL 需要開發人員知道使用者何時收到 UI 提示。
    - `AcquireTokenSilent`一律會產生成功或失敗的無訊息要求。
    - `AcquireToken`一律會產生透過 UI 提示使用者的要求。
- MSAL 支援從預設瀏覽器或內嵌的 web view 登入：
  - 根據預設，會使用裝置上的預設瀏覽器。 這可讓 MSAL 使用一或多個已登入帳戶可能已經存在的驗證狀態（cookie）。 如果沒有驗證狀態，透過 MSAL 在授權期間進行驗證，會導致在相同瀏覽器中使用其他 web 應用程式的優點，而建立驗證狀態（cookie）。
- 新的例外狀況模型：
  - 例外狀況更清楚地定義發生的錯誤類型，以及開發人員需要採取哪些動作來解決它。
- MSAL 支援和呼叫的參數物件 `AcquireToken` `AcquireTokenSilent` 。
- MSAL 支援的宣告式設定：
  - 用戶端識別碼，重新導向 URI。
  - 內嵌與預設瀏覽器
  - 許可權
  - HTTP 設定，例如讀取和連接逾時

## <a name="your-app-registration-and-migration-to-msal"></a>您的應用程式註冊和遷移至 MSAL

您不需要變更現有的應用程式註冊，就可以使用 MSAL。 如果您想要利用增量/漸進式同意，您可能需要檢查註冊，以識別您想要以累加方式要求的特定範圍。 如需範圍和累加式同意的詳細資訊，請遵循。

在入口網站的應用程式註冊中，您會看到 [ **API 許可權**] 索引標籤。這會提供您的應用程式目前設定用來要求存取權的 Api 和許可權（範圍）清單。 它也會顯示與每個 API 許可權相關聯的領域名稱清單。

### <a name="user-consent"></a>使用者同意

使用 ADAL 和 AAD v1 端點時，使用者同意其擁有的資源會在第一次使用時授與。 透過 MSAL 和 Microsoft 身分識別平臺，您可以透過累加方式要求同意。 增量同意適用于使用者可能會考慮高許可權的許可權，或者如果未提供許可權的清楚說明，則可能會有問題。 在 ADAL 中，這些許可權可能會導致使用者放棄登入您的應用程式。

> [!TIP]
> 我們建議在您需要為使用者提供其他內容，以瞭解您的應用程式需要許可權的情況下，使用累加式同意。

### <a name="admin-consent"></a>系統管理員同意

組織系統管理員可以同意您的應用程式代表其組織的所有成員所需的許可權。 有些組織只允許系統管理員同意應用程式。 系統管理員同意要求您必須在應用程式註冊中包含您應用程式所使用的所有 API 許可權和範圍。

> [!TIP]
> 雖然您可以使用 MSAL 來要求不包含在應用程式註冊中的範圍，但建議您更新應用程式註冊，以包含使用者可授與許可權的所有資源和範圍。

## <a name="migrating-from-resource-ids-to-scopes"></a>從資源識別碼遷移至範圍

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>第一次使用時，驗證和要求擁有權限的授權

如果您目前使用 ADAL，而不需要使用累加式同意，開始使用 MSAL 最簡單的方式就是 `acquireToken` 使用新的物件來提出 `AcquireTokenParameter` 要求，並設定資源識別碼值。

> [!CAUTION]
> 您不能同時設定範圍和資源識別碼。嘗試同時設定這兩個會導致 `IllegalArgumentException` 。

 這會產生您所使用的相同 v1 行為。 在您的應用程式註冊中要求的擁有權限，會在其第一次互動期間向使用者要求。

### <a name="authenticate-and-request-permissions-only-as-needed"></a>只在必要時驗證並要求許可權

若要利用增量同意，請建立您的應用程式從應用程式註冊使用的許可權清單（範圍），並根據下列專案將它們組織成兩個清單：

- 您想要在使用者第一次與您的應用程式互動期間，于登入期間要求的範圍。
- 與您的應用程式重要功能相關聯的許可權，您也必須向使用者說明。

組織範圍之後，請依您想要為其要求權杖的資源（API）來組織每個清單。 以及任何其他您想要讓使用者同時授權的範圍。

用來向 MSAL 提出要求的 parameters 物件支援：

- `Scope`：您想要為其要求授權並接收存取權杖的範圍清單。
- `ExtraScopesToConsent`：當您要求其他資源的存取權杖時，您想要要求授權的其他範圍清單。 這份範圍清單可讓您將要求使用者授權所需的次數減到最少。 這表示較少的使用者授權或同意提示。

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>從 AuthenticationCoNtext 遷移至 PublicClientApplications

### <a name="constructing-publicclientapplication"></a>建立 PublicClientApplication

當您使用 MSAL 時，會具現化 `PublicClientApplication` 。 此物件會建立應用程式身分識別的模型，並用來對一個或多個授權單位提出要求。 您將使用此物件來設定用戶端身分識別、重新導向 URI、預設授權單位、是否使用裝置瀏覽器與內嵌 web 視圖、記錄層級等等。

您可以使用 JSON 來以宣告方式設定此物件，這會以檔案或存放區的形式提供給 APK 中的資源。

雖然此物件不是 singleton，但在內部，它會 `Executors` 針對互動式和無訊息要求使用 shared。

### <a name="business-to-business"></a>企業對企業

在 ADAL 中，您從要求存取權杖的每個組織都需要個別的實例 `AuthenticationContext` 。 在 MSAL 中，這不再是必要條件。 您可以指定要在無訊息或互動式要求中要求權杖的授權單位。

### <a name="migrate-from-authority-validation-to-known-authorities"></a>從授權驗證遷移至已知的授權單位

MSAL 沒有用來啟用或停用授權單位驗證的旗標。 授權單位驗證是 ADAL 中的一項功能，在早期版本的 MSAL 中，可防止您的程式碼向可能的惡意授權單位要求權杖。 MSAL 現在會抓取 Microsoft 已知的授權清單，並將該清單與您在設定中指定的授權單位合併。

> [!TIP]
> 如果您是 Azure 企業對消費者（B2C）使用者，這表示您不再需要停用授權驗證。 相反地，請將每個支援的 Azure AD B2C 原則納入為 MSAL 設定中的授權單位。

如果您嘗試使用 Microsoft 不知道且未包含在設定中的授權單位，您會收到 `UnknownAuthorityException` 。

### <a name="logging"></a>記錄
您現在可以用宣告方式將記錄設定為設定的一部分，如下所示：

 ```
 "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": true
  }
  ```

## <a name="migrate-from-userinfo-to-account"></a>從使用者資訊遷移至帳戶

在 ADAL 中，會 `AuthenticationResult` 提供 `UserInfo` 物件，用來抓取已驗證帳戶的相關資訊。 「使用者」一詞（代表人類或軟體代理程式）是以難以溝通的方式來套用，因為有些應用程式支援具有多個帳戶的單一使用者（不論是人類或軟體代理程式）。

請考慮使用銀行帳戶。 您在多個金融機構中可能有一個以上的帳戶。 當您開啟帳戶時，您（使用者）會發出認證，例如，用來存取每個帳戶的餘額、帳單款項等等的 ATM 卡 & PIN。 這些認證只能在發行他們的金融機構使用。

就像金融機構的帳戶一樣，Microsoft 身分識別平臺中的帳戶會使用認證來存取。 這些認證可以向 Microsoft 註冊或發行。 或由 Microsoft 代表組織。

相較于金融機構，Microsoft 身分識別平臺的不同之處在于，Microsoft 身分識別平臺會提供一個架構，讓使用者可以使用一個帳戶和其相關聯的認證，來存取屬於多個人和組織的資源。 這就像是能夠使用一個銀行所發行的卡片，還有另一個金融機構。 這是因為所有有問題的組織都使用 Microsoft 身分識別平臺，讓多個組織都能使用一個帳戶。 以下是範例：

Sam 適用于 Contoso.com，但會管理屬於 Fabrikam.com 的 Azure 虛擬機器。 若要讓 Sam 管理 Fabrikam 的虛擬機器，他必須有權存取它們。 將 Sam 的帳戶新增至 Fabrikam.com，並授與他的帳戶角色，讓他能夠使用虛擬機器，即可授與此存取權。 這會透過 Azure 入口網站來完成。

將 Sam 的 Contoso.com 帳戶新增為 Fabrikam.com 的成員，會導致在 Fabrikam .com 的 Azure Active Directory 中建立新的記錄，以供 Sam 使用。 Sam 在 Azure Active Directory 中的記錄稱為「使用者物件」。 在此情況下，該使用者物件會在 Contoso.com 中指向 Sam 的使用者物件。 Sam 的 Fabrikam user 物件是 Sam 的本機標記法，會用來儲存與 Sam 相關聯之帳戶的資訊，在 Fabrikam.com 的內容中。 在 Contoso.com 中，Sam 的職稱是資深 DevOps 顧問。 在 Fabrikam 中，Sam 的標題是「承包商-虛擬機器」。 在 Contoso.com 中，Sam 不會負責管理虛擬機器，也不會獲得授權。 在 Fabrikam.com 中，這是他唯一的工作功能。 但是 Sam 仍然只有一組認證可以追蹤，這是 Contoso.com 所發行的認證。

一旦成功 `acquireToken` 呼叫之後，您就會看到 `IAccount` 可在後續要求中使用之物件的參考 `acquireTokenSilent` 。

### <a name="imultitenantaccount"></a>IMultiTenantAccount

如果您的應用程式會從代表帳戶的每個租使用者存取帳戶的宣告，您可以將 `IAccount` 物件轉換為 `IMultiTenantAccount` 。 此介面提供以租使用者識別碼做為索引鍵的對應 `ITenantProfiles` ，可讓您從相對於目前的帳戶，存取您已向其要求權杖之每個租使用者中的帳戶所屬的宣告。

位於根目錄的宣告 `IAccount` ， `IMultiTenantAccount` 一律包含來自主要租使用者的宣告。 如果您尚未對 home 租使用者內的權杖提出要求，此集合會是空的。

## <a name="other-changes"></a>其他變更

### <a name="use-the-new-authenticationcallback"></a>使用新的 Authenticationcallback 傳給

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

在 ADAL 中，有一種類型的例外狀況， `AuthenticationException` 其中包含用來抓取 `ADALError` 列舉值的方法。
在 MSAL 中，有一個例外狀況階層，而且每個都有一組相關聯的特定錯誤碼。

MSAL 例外狀況的清單

|例外狀況  | Description  |
|---------|---------|
| `MsalException`     | MSAL 擲回的預設核取例外狀況。  |
| `MsalClientException`     | 如果錯誤是用戶端，則擲回。 |
| `MsalArgumentException`     | 如果一或多個輸入引數無效，則擲回。 |
| `MsalClientException`     | 如果錯誤是用戶端，則擲回。 |
| `MsalServiceException`     | 如果錯誤是伺服器端則擲回。 |
| `MsalUserCancelException`     | 如果使用者取消驗證流程則擲回。  |
| `MsalUiRequiredException`     | 如果無法以無訊息模式重新整理權杖，則擲回。  |
| `MsalDeclinedScopeException`     | 如果伺服器拒絕一或多個要求的範圍，則會擲回。  |
| `MsalIntuneAppProtectionPolicyRequiredException` | 如果資源已啟用 MAMCA 保護原則，則會擲回。 |

### <a name="adalerror-to-msalexception-errorcode"></a>ADALError 至 MsalException ErrorCode

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
