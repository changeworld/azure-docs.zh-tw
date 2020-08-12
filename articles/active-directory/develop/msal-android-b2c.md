---
title: Azure AD B2C (MSAL Android) |Azure
titleSuffix: Microsoft identity platform
description: '瞭解使用 Azure AD B2C 搭配適用于 Android 的 Microsoft 驗證程式庫 (MSAL 時的特定考慮。Android) '
services: active-directory
author: brianmel
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 9/18/2019
ms.author: brianmel
ms.reviewer: rapong
ms.custom: aaddev
ms.openlocfilehash: 0ad5fab685757d2efd91cd1df0e48a5f1258d17e
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88119873"
---
# <a name="use-msal-for-android-with-b2c"></a>搭配 B2C 使用適用于 Android 的 MSAL

Microsoft 驗證程式庫 (MSAL) 可讓應用程式開發人員使用 [Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/index.yml) \(部分機器翻譯\)，以社交和本機身分識別驗證使用者。 Azure AD B2C 是身分識別管理服務。 用來自訂和控制客戶在使用您的應用程式時，如何註冊、登入及管理其設定檔。

## <a name="configure-known-authorities-and-redirect-uri"></a>設定已知的授權單位和重新導向 URI

在適用于 Android 的 MSAL 中， (使用者旅程) 的 B2C 原則會設定為個別的授權單位。

假設有一個具有兩個原則的 B2C 應用程式：
- 註冊/登入
    * 呼叫`B2C_1_SISOPolicy`
- 編輯個人資料
    * 呼叫`B2C_1_EditProfile`

應用程式的設定檔會宣告兩個 `authorities` 。 每個原則各一個。 `type`每個授權單位的屬性為 `B2C` 。

### `app/src/main/res/raw/msal_config.json`
```json
{
    "client_id": "<your_client_id_here>",
    "redirect_uri": "<your_redirect_uri_here>",
    "authorities": [{
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/",
            "default": true
        },
        {
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_EditProfile/"
        }
    ]
}
```

`redirect_uri`必須在應用程式設定中註冊，也可以在中， `AndroidManifest.xml` 以支援[授權碼授與流程](../../active-directory-b2c/authorization-code-flow.md)期間的重新導向。

## <a name="initialize-ipublicclientapplication"></a>初始化 IPublicClientApplication

`IPublicClientApplication`是由 factory 方法所建立，可允許以非同步方式剖析應用程式設定。

```java
PublicClientApplication.createMultipleAccountPublicClientApplication(
    context, // Your application Context
    R.raw.msal_config, // Id of app JSON config
    new IPublicClientApplication.ApplicationCreatedListener() {
        @Override
        public void onCreated(IMultipleAccountPublicClientApplication pca) {
            // Application has been initialized.
        }

        @Override
        public void onError(MsalException exception) {
            // Application could not be created.
            // Check Exception message for details.
        }
    }
);
```

## <a name="interactively-acquire-a-token"></a>以互動方式取得權杖

若要以互動方式使用 MSAL 取得權杖，請建立 `AcquireTokenParameters` 實例，並將它提供給 `acquireToken` 方法。 下面的權杖要求會使用 `default` 授權單位。

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }

        @Override
        public void onCancel() {
            // The user cancelled the flow
        }
    }).build();

pca.acquireToken(parameters);
```

## <a name="silently-renew-a-token"></a>以無訊息方式更新權杖

若要使用 MSAL 以無訊息方式取得權杖，請建立 `AcquireTokenSilentParameters` 實例，並將它提供給 `acquireTokenSilentAsync` 方法。 不同于 `acquireToken` 方法， `authority` 必須指定，以無訊息方式取得權杖。

```java
IMultilpeAccountPublicClientApplication pca = ...; // Initialization not shown
AcquireTokenSilentParameters parameters = new AcquireTokenSilentParameters.Builder()
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .forAccount(account)
    // Select a configured authority (policy), mandatory for silent auth requests
    .fromAuthority("https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/")
    .callback(new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccesful, inspect the exception
        }
    })
    .build();

pca.acquireTokenSilentAsync(parameters);
```

## <a name="specify-a-policy"></a>指定原則

因為 B2C 中的原則是以個別的授權單位來表示，所以叫用預設值以外的原則是藉由 `fromAuthority` 在建立或參數時指定子句來完成 `acquireToken` `acquireTokenSilent` 。  例如：

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>處理密碼變更原則

本機帳戶註冊或登入使用者流程顯示「**忘記密碼？**」 連結免費建立一個帳戶。 按一下此連結並不會自動觸發密碼重設使用者流程。

相反地，系統會將錯誤碼 `AADB2C90118` 傳回您的應用程式。 您的應用程式應該藉由執行可重設密碼的特定使用者流程來處理此錯誤碼。

若要攔截密碼重設錯誤代碼，可以在您的內使用下列執行 `AuthenticationCallback` ：

```java
new AuthenticationCallback() {

    @Override
    public void onSuccess(IAuthenticationResult authenticationResult) {
        // ..
    }

    @Override
    public void onError(MsalException exception) {
        final String B2C_PASSWORD_CHANGE = "AADB2C90118";

        if (exception.getMessage().contains(B2C_PASSWORD_CHANGE)) {
            // invoke password reset flow
        }
    }

    @Override
    public void onCancel() {
        // ..
    }
}
```

## <a name="use-iauthenticationresult"></a>使用 IAuthenticationResult

成功取得權杖的結果會產生 `IAuthenticationResult` 物件。 其中包含存取權杖、使用者宣告和中繼資料。

### <a name="get-the-access-token-and-related-properties"></a>取得存取權杖和相關屬性

```java
// Get the raw bearer token
String accessToken = authenticationResult.getAccessToken();

// Get the scopes included in the access token
String[] accessTokenScopes = authenticationResult.getScope();

// Gets the access token's expiry
Date expiry = authenticationResult.getExpiresOn();

// Get the tenant for which this access token was issued
String tenantId = authenticationResult.getTenantId();
```

### <a name="get-the-authorized-account"></a>取得授權的帳戶

```java
// Get the account from the result
IAccount account = authenticationResult.getAccount();

// Get the id of this account - note for B2C, the policy name is a part of the id
String id = account.getId();

// Get the IdToken Claims
//
// For more information about B2C token claims, see reference documentation
// https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens
Map<String, ?> claims = account.getClaims();

// Get the 'preferred_username' claim through a convenience function
String username = account.getUsername();

// Get the tenant id (tid) claim through a convenience function
String tenantId = account.getTenantId();
```

### <a name="idtoken-claims"></a>IdToken 宣告

安全性權杖服務會將 IdToken 中傳回的宣告填入 (STS) ，而不是由 MSAL。 視識別提供者 (使用的 IdP) 而定，某些宣告可能不存在。 有些 Idp 目前不提供宣告 `preferred_username` 。 由於 MSAL 會使用此宣告來進行快取，因此 `MISSING FROM THE TOKEN RESPONSE` 會在其位置使用預留位置值。 如需有關 B2C IdToken 宣告的詳細資訊，請參閱[Azure Active Directory B2C 中的權杖總覽](../../active-directory-b2c/tokens-overview.md#claims)。

## <a name="managing-accounts-and-policies"></a>管理帳戶和原則

B2C 會將每個原則視為個別的授權單位。 因此，從每個原則傳回的存取權杖、重新整理權杖和識別碼權杖無法互換。 這表示每個原則都會傳回另一個 `IAccount` 物件，其權杖無法用來叫用其他原則。

每個原則會將新增 `IAccount` 至每個使用者的快取。 如果使用者登入應用程式，並叫用兩個原則，則會有兩個 `IAccount` 。 若要從快取中移除此使用者，您必須 `removeAccount()` 針對每個原則呼叫。

當您使用來更新原則的權杖時 `acquireTokenSilent` ，請提供 `IAccount` 從先前的原則調用傳回的相同 `AcquireTokenSilentParameters` 。 提供另一個原則所傳回的帳戶會導致錯誤。

## <a name="next-steps"></a>後續步驟

若要深入瞭解 Azure Active Directory B2C (Azure AD B2C) [Azure Active Directory B2C 是什麼？](../../active-directory-b2c/overview.md)