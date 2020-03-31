---
title: 單個或多個帳戶公共用戶端應用 |蔚藍
description: 單個或多個帳戶公共用戶端應用的概述。
services: active-directory
documentationcenter: ''
author: shoatman
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: f2ce993b8fbf2a1b04ea4ad9d992ba278dbc964e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701411"
---
# <a name="single-and-multiple-account-public-client-apps"></a>單個或多個帳戶公共用戶端應用

本文將説明您瞭解單個或多個帳戶公共用戶端應用中使用的類型，重點是單一帳戶公共用戶端應用。 

Azure 活動目錄身份驗證庫 （ADAL） 對伺服器建模。  Microsoft 身份驗證庫 （MSAL） 改為對用戶端應用程式建模。  大多數 Android 應用都被視為公共用戶端。 公共用戶端是不能安全地保守機密的應用。  

MSAL 專門介紹的`PublicClientApplication`API 表面，以簡化和闡明一次僅允許一個帳戶使用的應用的開發體驗。 `PublicClientApplication`子類為`SingleAccountPublicClientApplication`和`MultipleAccountPublicClientApplication`。  下圖顯示了這些類之間的關係。

![單一帳戶公共用戶端應用程式 UML 類圖](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>單一帳戶公共用戶端應用程式

該`SingleAccountPublicClientApplication`類允許您創建基於 MSAL 的應用，該應用只允許一次登錄單個帳戶。 `SingleAccountPublicClientApplication` 與 `PublicClientApplication` 在下列各方面不同：

- MSAL 跟蹤當前登錄帳戶。
  - 如果應用使用代理（Azure 門戶應用註冊期間的預設值），並且安裝在存在代理的設備上，MSAL 將驗證該帳戶在設備上是否仍然可用。
- `signIn`允許您顯式和獨立于請求作用域登錄帳戶。
- `acquireTokenSilent`不需要帳戶參數。  如果您確實提供了一個帳戶，並且您提供的帳戶與 MSAL 跟蹤的當前帳戶不匹配，則引發一`MsalClientException`個帳戶。
- `acquireToken`不允許使用者切換帳戶。 如果使用者嘗試切換到其他帳戶，將引發異常。
- `getCurrentAccount`返回提供以下內容的結果物件：
  - 指示帳戶是否已更改的布林。 例如，由於從設備中刪除帳戶，可能會更改帳戶。
  - 以前的帳戶。 如果您在從設備中刪除帳戶或新帳戶登錄時需要執行任何本地資料清理，則此功能非常有用。
  - 當前帳戶。
- `signOut`從設備中刪除與用戶端關聯的任何權杖。  

當 Android 身份驗證代理（如 Microsoft 身份驗證器或 Intune 公司門戶）安裝在設備上且你的應用配置為使用代理時，`signOut`不會從設備中刪除帳戶。

## <a name="single-account-scenario"></a>單一帳戶方案

以下偽代碼說明了使用`SingleAccountPublicClientApplication`。

```java
// Construct Single Account Public Client Application
ISingleAccountPublicClientApplication app = PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will get a UI prompt before getting the token.
app.signIn(getActivity(), scopes, new AuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
    }
);

// Load Account Specific Data
getDataForAccount(account);

// Get Current Account
ICurrentAccountResult currentAccountResult = app.getCurrentAccount();
if (currentAccountResult.didAccountChange())
{
    // Account Changed Clear existing account data
    clearDataForAccount(currentAccountResult.getPriorAccount());
    mAccount = currentAccountResult.getCurrentAccount();
    if (account != null)
    {
        //load data for new account
        getDataForAccount(account);
    }
}

// Sign out
if (app.signOut())
{
    clearDataForAccount(mAccount);
    mAccount = null;
}
```

## <a name="multiple-account-public-client-application"></a>多個帳戶公共用戶端應用程式

該`MultipleAccountPublicClientApplication`類用於創建基於 MSAL 的應用，允許同時登錄多個帳戶。 它允許您獲取、添加和刪除帳戶，如下所示：

### <a name="add-an-account"></a>新增帳戶

通過調用`acquireToken`一次或多次，在應用程式中使用一個或多個帳戶。  

### <a name="get-accounts"></a>獲取帳戶

- 致電`getAccount`獲取特定帳戶。
- 調用`getAccounts`以獲取應用當前已知的帳戶清單。

你的應用將無法枚舉代理應用已知的設備上的所有 Microsoft 標識平臺帳戶。 它只能枚舉應用已使用的帳戶。  這些功能不會返回從設備中刪除的帳戶。

### <a name="remove-an-account"></a>移除帳戶

使用帳戶識別碼調用`removeAccount`刪除帳戶。

如果應用配置為使用代理，並且設備上安裝了代理，則調用 時不會從代理中刪除該帳戶`removeAccount`。  僅刪除與用戶端關聯的權杖。

## <a name="multiple-account-scenario"></a>多個帳戶方案

以下偽代碼演示如何創建多個帳戶應用、在設備上列出帳戶以及獲取權杖。

```java
// Construct Multiple Account Public Client Application
IMultipleAccountPublicClientApplication app = PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will be required to interact with a UI to obtain a token
app.acquireToken(getActivity(), scopes, new AuthenticationCallback()
 {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
 });

...

// Get the default authority
String authority = app.getConfiguration().getDefaultAuthority().getAuthorityURL().toString();

// Get a list of accounts on the device
List<IAccount> accounts = app.getAccounts();

// Pick an account to obtain a token from without prompting the user to sign in
IAccount selectedAccount = accounts.get(0);

// Get a token without prompting the user
app.acquireTokenSilentAsync(scopes, selectedAccount, authority, new SilentAuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }
});
```
