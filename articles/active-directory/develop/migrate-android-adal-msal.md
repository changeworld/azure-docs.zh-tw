---
title: ADAL 到 MSAL 的 Android 遷移指南 |蔚藍
description: 瞭解如何將 Azure 活動目錄身份驗證庫 （ADAL） Android 應用遷移到 Microsoft 身份驗證庫 （MSAL）。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084048"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>ADAL 到 MSAL 的 Android 遷移指南

本文重點介紹遷移使用 Azure 活動目錄身份驗證庫 （ADAL） 使用 Microsoft 身份驗證庫 （MSAL） 的應用所需的更改。

## <a name="difference-highlights"></a>差異亮點

ADAL 與 Azure 活動目錄 v1.0 終結點配合使用。 Microsoft 身份驗證庫 （MSAL） 與 Microsoft 標識平臺（以前稱為 Azure 活動目錄 v2.0 終結點）配合使用。 Microsoft 標識平臺不同于 Azure 活動目錄 v1.0，因為它：

支援：
  - 組織標識（Azure 活動目錄）
  - 非組織標識，如Outlook.com、Xbox Live 等
  - （僅限 B2C）與谷歌、Facebook、推特和亞馬遜聯合登錄

- 標準是否與：
  - OAuth v2.0
  - 打開 ID 連接 （OIDC）

MSAL 公共 API 引入了重要的更改，包括：

- 用於訪問權杖的新模型：
  - ADAL 通過 提供對權杖的訪問`AuthenticationContext`，該權杖表示伺服器。 MSAL 通過 提供對權杖的訪問`PublicClientApplication`，該權杖表示用戶端。 用戶端開發人員不需要為每個需要與之交互的頒發機構`PublicClientApplication`創建新實例。 只需要一`PublicClientApplication`個配置。
  - 支援使用作用域以及資源識別碼請求訪問權杖。
  - 支援增量同意。 當使用者訪問應用中越來越多的功能（包括應用註冊期間未包括的功能）時，開發人員可以請求作用域。
  - 當局不再在運行時得到驗證。 相反，開發人員在開發過程中聲明"已知許可權"的清單。
- 權杖 API 更改：
  - 在 ADAL`AcquireToken()`中，首先發出無聲請求。 否則，它將發出互動式請求。 此行為導致某些開發人員僅依賴`AcquireToken`，從而導致使用者有時意外提示其獲得憑據。 MSAL 要求開發人員在使用者收到 UI 提示時有意考慮。
    - `AcquireTokenSilent`始終導致一個靜默請求，要麼成功，要麼失敗。
    - `AcquireToken`始終生成通過 UI 提示使用者的請求。
- MSAL 支援從預設瀏覽器或嵌入式 Web 視圖登錄：
  - 預設情況下，使用設備上的預設瀏覽器。 這允許 MSAL 使用一個或多個已登錄帳戶的身份驗證狀態 （cookies）。 如果不存在身份驗證狀態，則通過 MSAL 在授權期間進行身份驗證會導致為將在同一瀏覽器中使用的其他 Web 應用程式創建身份驗證狀態 （cookies）。
- 新的異常模型：
  - 異常更清楚地定義發生的錯誤類型以及開發人員需要執行的操作來解決它。
- MSAL 支援 和`AcquireToken``AcquireTokenSilent`調用的參數物件。
- MSAL 支援聲明性配置，用於：
  - 用戶端 ID，重定向 URI。
  - 嵌入式瀏覽器與預設瀏覽器
  - 當局
  - HTTP 設置，如讀取和連接逾時

## <a name="your-app-registration-and-migration-to-msal"></a>你的應用註冊和遷移到MSAL

您無需更改現有應用註冊才能使用 MSAL。 如果要利用增量/漸進式同意，可能需要查看註冊以標識要增量請求的特定作用域。 有關範圍和增量同意的詳細資訊，如下。

在門戶中的應用註冊中，您將看到一個**API 許可權**選項卡。這提供了應用當前配置為請求訪問的 API 和許可權（作用域）的清單。 它還顯示與每個 API 許可權關聯的作用功能變數名稱稱的清單。

### <a name="user-consent"></a>使用者同意

使用 ADAL 和 AAD v1 終結點時，使用者首次使用時會同意他們擁有的資源。 使用 MSAL 和 Microsoft 標識平臺，可以增量請求同意。 增量同意對於使用者可能認為具有高特權的許可權很有用，或者如果沒有明確說明為什麼需要許可權，可能會質疑。 在 ADAL 中，這些許可權可能導致使用者放棄登錄到你的應用。

> [!TIP]
> 我們建議您在需要向使用者提供有關應用需要許可權原因的其他上下文的情況下使用增量同意。

### <a name="admin-consent"></a>系統管理員同意

組織管理員可以代表其組織的所有成員同意應用程式所需的許可權。 某些組織只允許管理員同意應用程式。 管理員同意要求您在應用註冊中包括應用程式使用的所有 API 許可權和作用域。

> [!TIP]
> 即使您可以使用 MSAL 請求應用註冊中未包含的內容，我們建議您更新應用註冊以包括使用者可以授予許可權的所有資源和作用域。

## <a name="migrating-from-resource-ids-to-scopes"></a>從資源指示器遷移到作用域

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>首次使用時對擁有權限進行身份驗證和請求授權

如果您當前使用的是 ADAL，並且不需要使用增量同意，則開始使用 MSAL 的最簡單方法是使用新`acquireToken``AcquireTokenParameter`物件發出請求並設置資源識別碼 值。

> [!CAUTION]
> 無法同時設置作用域和資源識別碼。嘗試同時設置兩者將導致 。 `IllegalArgumentException`

 這將導致與您使用的 v1 行為相同。 應用註冊中請求的擁有權限在使用者首次交互期間請求。

### <a name="authenticate-and-request-permissions-only-as-needed"></a>僅根據需要進行身份驗證和請求許可權

要利用增量同意，請從應用註冊中列出應用使用的許可權（作用域），並根據：

- 在使用者首次與應用交互期間，要請求哪些作用域。
- 與應用的重要功能關聯的許可權，您還需要向使用者解釋這些許可權。

組織作用域後，請組織要為其請求權杖的資源 （API） 的每個清單。 以及您希望使用者同時授權的任何其他作用域。

用於向 MSAL 發出請求的參數物件支援：

- `Scope`：要請求授權並接收訪問權杖的範圍清單。
- `ExtraScopesToConsent`：要在請求其他資源的訪問權杖時為其請求授權的其他作用域清單。 此作用域清單允許您最小化請求使用者授權所需的次數。 這意味著更少的使用者授權或同意提示。

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>從身份驗證上下文遷移到公共用戶端應用程式

### <a name="constructing-publicclientapplication"></a>構建公共用戶端應用程式

使用 MSAL 時，將具現化 。 `PublicClientApplication` 此物件對應用標識建模，並用於向一個或多個頒發機構發出請求。 使用此物件，您將配置用戶端標識、重定向 URI、預設許可權、是否使用設備瀏覽器與嵌入式 Web 視圖、日誌級別等。

您可以使用 JSON 聲明性地配置此物件，JSON 作為檔提供，也可以作為 APK 中的資源存儲。

儘管此物件不是單個物件，但它在內部使用共用`Executors`的互動式請求和靜默請求。

### <a name="business-to-business"></a>企業對企業

在 ADAL 中，從 請求訪問權杖的每個組織都需要 單獨的`AuthenticationContext`實例。 在 MSAL 中，這不再是要求。 您可以指定要從中請求權杖作為靜默或互動式請求的一部分的許可權。

### <a name="migrate-from-authority-validation-to-known-authorities"></a>從許可權驗證遷移到已知頒發機構

MSAL 沒有用於啟用或禁用許可權驗證的標誌。 授權驗證是 ADAL 和 MSAL 早期版本中的一項功能，它可防止代碼向潛在惡意機構請求權杖。 MSAL 現在檢索 Microsoft 已知的許可權清單，並將該清單與您在配置中指定的頒發機構合併。

> [!TIP]
> 如果您是 Azure 業務消費者 （B2C） 使用者，這意味著您不再需要禁用許可權驗證。 相反，在 MSAL 配置中，將每個受支援的 Azure AD B2C 策略作為許可權。

如果您嘗試使用 Microsoft 不知道且未包含在配置中的頒發機構，您將獲得 。 `UnknownAuthorityException`

### <a name="logging"></a>記錄
現在，您可以聲明性地將日誌記錄配置為配置的一部分，如下所示：

 ```
 "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": true
  }
  ```

## <a name="migrate-from-userinfo-to-account"></a>從使用者資訊遷移到帳戶

在 ADAL`AuthenticationResult`中，`UserInfo`提供了用於檢索有關已身份驗證帳戶的資訊的物件。 術語"使用者"，即人工或軟體代理，其應用方式使得某些應用支援具有多個帳戶的單個使用者（無論是人工使用者還是軟體代理）難以傳達。

考慮銀行帳戶。 您可以在多個金融機構擁有多個帳戶。 當您開立帳戶時，您（使用者）將頒發憑據，如 ATM 卡& PIN，用於訪問每個帳戶的餘額、帳單付款等。 這些憑證只能在頒發憑證的金融機構使用。

與金融機構的帳戶一樣，使用憑據訪問 Microsoft 標識平臺中的帳戶。 這些憑據要麼在 Microsoft 註冊，要麼由 Microsoft 頒發。 或由 Microsoft 代表組織。

如果 Microsoft 標識平臺與金融機構的不同之處在于，在此類比中，Microsoft 標識平臺提供了一個框架，允許使用者使用一個帳戶及其關聯的憑據來訪問屬於多個個人和組織。 這就像能夠使用一家銀行發行的卡，在另一家金融機構。 這之所以有效，是因為所有有關組織都在使用 Microsoft 標識平臺，該平臺允許跨多個組織使用一個帳戶。 以下是範例：

Sam 適用于Contoso.com但管理屬於Fabrikam.com的 Azure 虛擬機器。 Sam 要管理 Fabrikam 的虛擬機器，需要授權他訪問它們。 可以通過將 Sam 的帳戶添加到Fabrikam.com，並授予其帳戶允許他處理虛擬機器的角色來授予此存取權限。 這將使用 Azure 門戶完成。

將 Sam 的Contoso.com帳戶添加為Fabrikam.com的成員將導致在 Fabrikam.com 的 Sam Azure 活動目錄中創建新記錄。 Sam 在 Azure 活動目錄中的記錄稱為使用者物件。 在這種情況下，該使用者物件將指向 sam 的使用者物件Contoso.com。 Sam 的 Fabrikam 使用者物件是 Sam 的本地表示形式，將用於在Fabrikam.com上下文中存儲與 Sam 關聯的帳戶的資訊。 在Contoso.com，山姆的頭銜是高級DevOps顧問。 在法布裡卡姆，山姆的頭銜是承包商-虛擬機器。 在Contoso.com，Sam 對管理虛擬機器概不負責，也不授權管理虛擬機器。 在Fabrikam.com，這是他唯一的工作職能。 然而，Sam 仍然只有一組憑據需要跟蹤，即 Contoso.com頒發的憑據。

成功`acquireToken`調用後，您將看到對可在以後`IAccount``acquireTokenSilent`請求中使用的物件的引用。

### <a name="imultitenantaccount"></a>I 多租戶帳戶

如果您有一個應用，該應用訪問來自表示該帳戶的每個租戶的帳戶的聲明，則可以將物件強制轉換為`IAccount`。 `IMultiTenantAccount` 此介面提供由租戶`ITenantProfiles`ID 鍵控的映射，允許您訪問屬於您請求權杖的每個租戶中相對於當前帳戶的帳戶的聲明。

和 的根處`IAccount`的聲明`IMultiTenantAccount`始終包含來自家庭租戶的聲明。 如果尚未在家庭租戶中請求權杖，則此集合將為空。

## <a name="other-changes"></a>其他變更

### <a name="use-the-new-authenticationcallback"></a>使用新的身份驗證回檔

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

## <a name="migrate-to-the-new-exceptions"></a>遷移到新異常

在 ADAL 中，有一種類型的異常`AuthenticationException`，其中包括一種檢索`ADALError`枚舉值的方法。
在 MSAL 中，存在異常層次結構，每個級別都有自己的關聯特定錯誤代碼集。

MSAL 異常清單

|例外狀況  | 描述  |
|---------|---------|
| `MsalException`     | MSAL 引發的預設選中異常。  |
| `MsalClientException`     | 如果錯誤是用戶端，則引發。 |
| `MsalArgumentException`     | 如果一個或多個輸入參數無效，則引發。 |
| `MsalClientException`     | 如果錯誤是用戶端，則引發。 |
| `MsalServiceException`     | 如果錯誤是伺服器端，則引發。 |
| `MsalUserCancelException`     | 如果使用者取消身份驗證流，則引發。  |
| `MsalUiRequiredException`     | 如果無法以靜默方式刷新權杖，則引發。  |
| `MsalDeclinedScopeException`     | 如果伺服器拒絕一個或多個請求的作用域，則引發。  |
| `MsalIntuneAppProtectionPolicyRequiredException` | 如果資源啟用了 MAMCA 保護原則，則引發。 |

### <a name="adalerror-to-msalexception-errorcode"></a>ADAL 錯誤到 Msal 異常錯誤代碼

### <a name="adal-logging-to-msal-logging"></a>ADAL 日誌記錄到 MSAL 日誌記錄

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
