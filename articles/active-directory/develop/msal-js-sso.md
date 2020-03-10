---
title: 單一登入（MSAL .js） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何使用適用于 JavaScript 的 Microsoft 驗證程式庫（MSAL）來建立單一登入體驗。
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8080d4cf4c3f0091f7837b3fccead5474c42db55
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78377231"
---
# <a name="single-sign-on-with-msaljs"></a>使用 MSAL.js 進行單一登入

單一登入（SSO）可讓使用者在登入後輸入其認證一次，並建立可跨多個應用程式重複使用的會話，而不需要再次驗證。 這可為使用者提供順暢的體驗，並減少重複的認證提示。

Azure AD 會在使用者第一次驗證時設定會話 cookie，以提供應用程式的 SSO 功能。 MSAL 程式庫可讓應用程式以數種方式利用此功能。

## <a name="sso-between-browser-tabs"></a>瀏覽器索引標籤之間的 SSO

當您的應用程式在多個索引標籤中開啟，而且您第一次在一個索引標籤上登入使用者時，使用者也會在沒有提示的情況下登入其他索引標籤。 MSAL 會在瀏覽器中快取使用者的識別碼權杖 `localStorage`，並在其他開啟的索引標籤上，將使用者登入應用程式。

根據預設，MSAL 會使用不允許在索引標籤之間共用會話的 `sessionStorage`。 若要在索引標籤之間取得 SSO，請務必將 MSAL 中的 `cacheLocation` 設定為 `localStorage`，如下所示。

```javascript
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>應用程式之間的 SSO

當使用者進行驗證時，會在瀏覽器的 Azure AD 網域上設定會話 cookie。 MSAL 依賴此會話 cookie，為不同應用程式之間的使用者提供 SSO。 MSAL 也會在每個應用程式域的瀏覽器儲存體中，快取使用者的識別碼權杖和存取權杖。 因此，在不同的情況下，SSO 行為會有所不同：  

### <a name="applications-on-the-same-domain"></a>相同網域上的應用程式

當應用程式裝載在相同的網域時，使用者可以登入一次應用程式，然後在沒有提示的情況下，向其他應用程式進行驗證。 MSAL 會利用為網域上的使用者快取的權杖來提供 SSO。

### <a name="applications-on-different-domain"></a>不同網域上的應用程式

當應用程式裝載于不同的網域時，網域 B 中的 MSAL 無法存取在網域 A 上快取的權杖。

這表示當使用者登入網域 A 時，流覽至網域 B 上的應用程式時，系統會將他們重新導向或提示 Azure AD 頁面。 因為 Azure AD 仍然具有使用者會話 cookie，所以它會登入使用者，而不需要重新輸入認證。 如果使用者在與 Azure AD 的會話中有多個使用者帳戶，系統會提示使用者選擇要用來登入的相關帳戶。

### <a name="automatically-select-account-on-azure-ad"></a>在 Azure AD 上自動選取帳戶

在某些情況下，應用程式可以存取使用者的驗證內容，而且想要在登入多個帳戶時避免 Azure AD 帳戶選取提示。  這可以透過幾個不同的方式來完成：

**使用會話識別碼（SID）**

會話識別碼是[選擇性](active-directory-optional-claims.md)的宣告，可在識別碼權杖中設定。 此宣告可讓應用程式識別使用者的 Azure AD 會話，而不受使用者的帳戶名稱或使用者名。 您可以將要求參數中的 SID 傳遞給 `acquireTokenSilent` 呼叫。 這可讓 Azure AD 略過選取的帳戶。 SID 會系結至會話 cookie，而且不會跨瀏覽器內容。

```javascript
var request = {
    scopes: ["user.read"],
    sid: sid
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

> [!Note]
> SID 只能搭配 MSAL 中的 `acquireTokenSilent` 呼叫所提出的無訊息驗證要求使用。
您可以在[這裡](active-directory-optional-claims.md)找到在應用程式資訊清單中設定選擇性宣告的步驟。

**使用登入提示**

如果您未設定 SID 宣告，或需要略過互動式驗證呼叫中的帳戶選取提示，您可以在要求參數中提供 `login_hint`，並選擇性地在 MSAL 的互動式方法（`loginPopup`、`loginRedirect`、`acquireTokenPopup` 和 `acquireTokenRedirect`）中提供 `domain_hint` 作為 `extraQueryParameters`。 例如：

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

您可以藉由讀取使用者的識別碼權杖中傳回的宣告，來取得 login_hint 和 domain_hint 的值。

* **loginHint**應該設定為識別碼權杖中的 `preferred_username` 宣告。

* 只有在使用/common 授權單位時，才需要傳遞**domain_hint** 。 網域提示是由租使用者識別碼（tid）所決定。  如果識別碼權杖中的 `tid` 宣告為 `9188040d-6c67-4c5b-b112-36a304b66dad` 則為取用者。 否則，就是組織。

如需登入提示和網域提示值的詳細資訊，請參閱[這裡](v2-oauth2-implicit-grant-flow.md)。

> [!Note]
> 您不能同時傳遞 SID 和 login_hint。 這會導致錯誤回應。

## <a name="sso-without-msaljs-login"></a>不含 MSAL 的 SSO 登入

根據設計，MSAL 會要求在取得 Api 的權杖之前，呼叫登入方法來建立使用者內容。 由於登入方法是互動式的，因此使用者會看到提示字元。

在某些情況下，應用程式可以透過在另一個應用程式中起始的驗證來存取已驗證使用者的內容或識別碼權杖，而且想要利用 SSO 來取得權杖，而不需要先透過 MSAL 登入。

其中一個範例是：使用者已登入父系 web 應用程式，其裝載另一個以附加元件或外掛程式身分執行的 JavaScript 應用程式。

在此案例中，您可以透過下列方式來達成 SSO 體驗：

將 `sid` （或 `login_hint` 並選擇性地 `domain_hint`）當做要求參數傳遞至 MSAL `acquireTokenSilent` 呼叫，如下所示：

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>將 ADAL 中的 SSO MSAL 至 .js 更新

MSAL 會為 Azure AD 的驗證案例帶來與 ADAL 的功能同位。 為了讓 ADAL 能夠輕鬆進行 MSAL，並避免提示使用者重新登入，程式庫會在 ADAL 中讀取代表使用者會話的識別碼權杖，並在 MSAL 中順暢地登入使用者。  

若要在從 ADAL 更新時利用單一登入（SSO）行為，您必須確定程式庫使用 `localStorage` 來快取權杖。 在初始化時，將 `cacheLocation` 設定為在 MSAL 中 `localStorage`，如下所示：


```javascript
// In ADAL.js
window.config = {
   clientId: 'g075edef-0efa-453b-997b-de1337c29185',
   cacheLocation: 'localStorage'
};

var authContext = new AuthenticationContext(config);


// In latest MSAL.js version
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

設定完成後，MSAL 就能夠讀取 ADAL 中已驗證之使用者的快取狀態，並使用它在 MSAL 中提供 SSO。

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure AD 中的[單一登入會話和權杖存留期](active-directory-configurable-token-lifetimes.md)值。
