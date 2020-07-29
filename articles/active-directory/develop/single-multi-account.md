---
title: 單一和多個帳戶公用用戶端應用程式 |Azure
description: 概述單一和多個帳戶的公用用戶端應用程式。
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 89a383aabf3487a0938604bc28ddb06c0541d13e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80881327"
---
# <a name="single-and-multiple-account-public-client-apps"></a>單一和多個帳戶公用用戶端應用程式

本文將協助您瞭解單一和多個帳戶公用用戶端應用程式中使用的類型，並將焦點放在單一帳戶的公用用戶端應用程式。 

Azure Active Directory Authentication Library （ADAL）會將伺服器模型。  Microsoft 驗證程式庫（MSAL）會改為模型您的用戶端應用程式。  大部分的 Android 應用程式都被視為公開用戶端。 公用用戶端是無法安全地保存秘密的應用程式。  

MSAL 會將的 API 介面特製化， `PublicClientApplication` 以簡化及澄清應用程式的開發經驗，讓您一次只能使用一個帳戶。 `PublicClientApplication`是由和所子類別化 `SingleAccountPublicClientApplication` `MultipleAccountPublicClientApplication` 。  下圖顯示這些類別之間的關聯性。

![SingleAccountPublicClientApplication UML 類別圖](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>單一帳戶公用用戶端應用程式

`SingleAccountPublicClientApplication`類別可讓您建立以 MSAL 為基礎的應用程式，只允許一次登入單一帳戶。 `SingleAccountPublicClientApplication` 與 `PublicClientApplication` 在下列各方面不同：

- MSAL 會追蹤目前已登入的帳戶。
  - 如果您的應用程式使用 broker （Azure 入口網站應用程式註冊期間的預設值），並且安裝在存在訊息代理程式的裝置上，MSAL 會確認帳戶是否仍可在裝置上使用。
- `signIn`可讓您明確且與要求的範圍分開登入帳戶。
- `acquireTokenSilent`不需要帳戶參數。  如果您提供帳戶，而且您提供的帳戶不符合 MSAL 所追蹤的目前帳戶， `MsalClientException` 就會擲回。
- `acquireToken`不允許使用者切換帳戶。 如果使用者嘗試切換至不同的帳戶，則會擲回例外狀況。
- `getCurrentAccount`傳回提供下列各項的結果物件：
  - 布林值，指出帳戶是否已變更。 例如，帳戶可能會因從裝置移除而變更。
  - 先前的帳戶。 如果您需要在從裝置移除帳戶或新帳戶登入時執行任何本機資料清除，這會很有用。
  - CurrentAccount。
- `signOut`從裝置移除任何與您的用戶端相關聯的權杖。  

當裝置上安裝了 Android 驗證代理程式（例如 Microsoft Authenticator 或 Intune 公司入口網站，且您的應用程式已設定為使用 broker 時，將 `signOut` 不會從裝置移除該帳戶。

## <a name="single-account-scenario"></a>單一帳戶案例

下列虛擬程式碼說明如何使用 `SingleAccountPublicClientApplication` 。

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

## <a name="multiple-account-public-client-application"></a>多個帳戶公用用戶端應用程式

`MultipleAccountPublicClientApplication`類別是用來建立以 MSAL 為基礎的應用程式，允許同時登入多個帳戶。 它可讓您取得、新增和移除帳戶，如下所示：

### <a name="add-an-account"></a>新增帳戶

藉由呼叫一或多次，在您的應用程式中使用一或多個帳戶 `acquireToken` 。  

### <a name="get-accounts"></a>取得帳戶

- 呼叫 `getAccount` 以取得特定帳戶。
- 呼叫 `getAccounts` 以取得應用程式目前已知的帳戶清單。

您的應用程式將無法列舉 broker 應用程式已知之裝置上的所有 Microsoft 身分識別平臺帳戶。 它只能列舉您的應用程式已使用的帳戶。  這些功能不會傳回已從裝置移除的帳戶。

### <a name="remove-an-account"></a>移除帳戶

藉由使用帳戶識別碼呼叫來移除帳戶 `removeAccount` 。

如果您的應用程式已設定為使用 broker，且裝置上已安裝 broker，則當您呼叫時，不會將該帳戶從 broker 中移除 `removeAccount` 。  只會移除與您的用戶端相關聯的權杖。

## <a name="multiple-account-scenario"></a>多重帳戶案例

下列虛擬程式碼顯示如何建立多個帳戶應用程式、列出裝置上的帳戶，以及取得權杖。

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
