---
title: Azure AD B2C （MSAL 安卓） |蔚藍
titleSuffix: Microsoft identity platform
description: 在 Android 的 Microsoft 身份驗證庫 （MSAL） 中使用 Azure AD B2C 時，瞭解具體注意事項。安卓系統）
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
ms.openlocfilehash: 0998bb04b0dfc69db4696f2e390cfe259eba6718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696516"
---
# <a name="use-msal-for-android-with-b2c"></a>將 MSAL 用於帶 B2C 的安卓

Microsoft 驗證程式庫 (MSAL) 可讓應用程式開發人員使用 [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/) \(部分機器翻譯\)，以社交和本機身分識別驗證使用者。 Azure AD B2C 是身分識別管理服務。 使用它自訂和控制客戶在使用應用程式時註冊、登錄和管理其設定檔的方式。

## <a name="configure-known-authorities-and-redirect-uri"></a>配置已知許可權並重定向 URI

在 Android 的 MSAL 中，B2C 策略（使用者旅程）配置為單個頒發機構。

給定一個具有兩個策略的 B2C 應用程式：
- 註冊/登錄
    * 叫`B2C_1_SISOPolicy`
- 編輯個人資料
    * 叫`B2C_1_EditProfile`

應用程式的設定檔將聲明兩個`authorities`。 每個策略一個。 每個`type`機構的屬性為`B2C`。

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

`redirect_uri`必須在應用配置中註冊 ，還要在`AndroidManifest.xml`[授權代碼授予流](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-oauth-code)期間支援重定向。

## <a name="initialize-ipublicclientapplication"></a>初始化 IPublicClient 應用程式

`IPublicClientApplication`由工廠方法構造，以允許非同步解析應用程式佈建。

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

## <a name="interactively-acquire-a-token"></a>以對話模式獲取權杖

要與 MSAL 交互獲取權杖，生成實例`AcquireTokenParameters`並將其供應給`acquireToken`方法。 下面的權杖請求使用許可權`default`。

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

## <a name="silently-renew-a-token"></a>靜默續訂權杖

要使用 MSAL 以靜默方式獲取權杖`AcquireTokenSilentParameters`，可以生成實例並將其供應`acquireTokenSilentAsync`給方法。 與`acquireToken`方法不同，`authority`必須指定 才能以靜默方式獲取權杖。

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

## <a name="specify-a-policy"></a>指定策略

由於 B2C 中的策略表示為單獨的頒發機構，因此在構造`fromAuthority``acquireToken`或`acquireTokenSilent`參數時指定子句來實現調用預設以外的策略。  例如：

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>處理密碼更改策略

本地帳戶註冊或登錄使用者流顯示"**忘記密碼？** 連結。 按一下此連結並不會自動觸發密碼重設使用者流程。

相反地，系統會將錯誤碼 `AADB2C90118` 傳回您的應用程式。 你的應用應該通過運行重置密碼的特定使用者流來處理此錯誤代碼。

要捕獲密碼重設錯誤代碼，可以在 以下實現中使用`AuthenticationCallback`：

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

## <a name="use-iauthenticationresult"></a>使用 I 身份驗證結果

成功的權杖獲取會導致`IAuthenticationResult`物件。 它包含訪問權杖、使用者聲明和中繼資料。

### <a name="get-the-access-token-and-related-properties"></a>獲取訪問權杖和相關屬性

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

### <a name="get-the-authorized-account"></a>獲取授權帳戶

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

### <a name="idtoken-claims"></a>IdToken 聲明

IdToken 中返回的聲明由安全權杖服務 （STS） 填充，而不是由 MSAL 填充。 根據所使用的標識提供程式 （IdP），某些聲明可能不存在。 一些國內流離失所者目前不提供`preferred_username`索賠。 由於 MSAL 使用此聲明進行緩存，因此預留位置值`MISSING FROM THE TOKEN RESPONSE`在其位置使用。 有關 B2C IdToken 聲明的詳細資訊，請參閱[Azure 活動目錄 B2C 中的權杖概述](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#claims)。

## <a name="managing-accounts-and-policies"></a>管理客戶和策略

B2C 將每個策略視為單獨的頒發機構。 因此，從每個策略返回的訪問權杖、刷新權杖和 ID 權杖是不可互換的。 這意味著每個策略返回一個單獨的`IAccount`物件，其權杖不能用於調用其他策略。

每個策略為每個使用者的`IAccount`緩存添加一個。 如果使用者登錄到應用程式並調用兩個策略，則他們有兩`IAccount`個 s。 要從緩存中刪除此使用者，必須調用`removeAccount()`每個策略。

使用 續訂策略的權杖時`acquireTokenSilent`，提供`IAccount`從以前調用策略返回`AcquireTokenSilentParameters`到 的相同權杖。 提供由其他策略返回的帳戶將導致錯誤。

## <a name="next-steps"></a>後續步驟

在[什麼是 Azure 活動目錄 B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)上瞭解有關 Azure 活動目錄 B2C（Azure AD B2C） 的更多內容？
