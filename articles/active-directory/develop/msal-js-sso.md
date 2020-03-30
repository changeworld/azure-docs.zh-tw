---
title: 單一登入 （MSAL.js） |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何使用適用于 JavaScript 的 Microsoft 身份驗證庫 （MSAL.js） 構建單一登入體驗。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262850"
---
# <a name="single-sign-on-with-msaljs"></a>使用 MSAL.js 進行單一登入

單一登入 （SSO） 使使用者能夠一次輸入其憑據以登錄並建立會話，該會話可以在多個應用程式之間重複使用，而無需再次進行身份驗證。 這為使用者提供了無縫體驗，並減少了重複的認證提示。

Azure AD 通過在使用者首次進行身份驗證時設置會話 Cookie 為應用程式提供 SSO 功能。 MSAL.js 庫允許應用程式通過多種方式利用這一點。

## <a name="sso-between-browser-tabs"></a>瀏覽器選項卡之間的 SSO

當您的應用程式在多個選項卡中打開，並且您首次在一個選項卡上登錄使用者時，使用者也會在另一個選項卡上登錄，而不會被提示。 MSAL.js 在瀏覽器`localStorage`中緩存使用者的 ID 權杖，並將使用者登錄到其他打開的選項卡上的應用程式。

預設情況下，MSAL.js 使用`sessionStorage`不允許在選項卡之間共用會話。 要在選項卡之間獲取 SSO，請確保`cacheLocation`在 MSAL.js 中設置為`localStorage`如下所示。

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

## <a name="sso-between-apps"></a>應用之間的 SSO

當使用者進行身份驗證時，在瀏覽器中的 Azure AD 域上設置會話 Cookie。 MSAL.js 依賴于此會話 Cookie 為不同應用程式之間的使用者提供 SSO。 MSAL.js 還會緩存使用者的 ID 權杖和訪問權杖，並緩存每個應用程式域的瀏覽器存儲。 因此，SSO 行為因不同情況而異：  

### <a name="applications-on-the-same-domain"></a>同一域上的應用程式

當應用程式託管在同一域中時，使用者可以登錄到應用一次，然後在沒有提示的情況下對其他應用進行身份驗證。 MSAL.js 利用為域上的使用者緩存的權杖來提供 SSO。

### <a name="applications-on-different-domain"></a>不同域上的應用程式

當應用程式託管在不同的域上時，緩存在域 A 上的權杖不能被域 B 中的 MSAL.js 訪問。

這意味著，當在域 A 上登錄的使用者導航到域 B 上的應用程式時，他們將被重定向或使用 Azure AD 頁面提示或提示。 由於 Azure AD 仍然具有使用者會話 Cookie，它將登錄使用者，並且他們不必重新輸入憑據。 如果使用者在與 Azure AD 會話中有多個使用者帳戶，系統將提示使用者選擇要登錄的相關帳戶。

### <a name="automatically-select-account-on-azure-ad"></a>在 Azure AD 上自動選擇帳戶

在某些情況下，應用程式可以訪問使用者的身份驗證上下文，並希望在登錄多個帳戶時避免 Azure AD 帳戶選擇提示。  這可以通過幾種不同的方式完成：

**使用會話 ID （SID）**

會話 ID 是可在 ID 權杖中配置的[可選聲明](active-directory-optional-claims.md)。 此聲明允許應用程式獨立于使用者的帳戶名稱或使用者名來標識使用者的 Azure AD 會話。 您可以將請求參數中的 SID 傳遞給`acquireTokenSilent`調用。 這將允許 Azure AD 繞過帳戶選擇。 SID 綁定到會話 Cookie，不會跨瀏覽器上下文。

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
> SID 只能用於 MSAL.js 中的`acquireTokenSilent`調用進行的靜默身份驗證請求。
您可以[在此處](active-directory-optional-claims.md)找到在應用程式清單中配置可選聲明的步驟。

**使用登錄提示**

如果未配置 SID 聲明或需要在互動式身份驗證調用中繞過帳戶選擇提示，則可以通過在請求參數`login_hint`中提供 和可選在 MSAL.js 交互方法 （`domain_hint``extraQueryParameters``loginPopup`和`loginRedirect` `acquireTokenPopup` `acquireTokenRedirect`） 中提供 一樣來執行此操作。 例如：

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

您可以通過讀取使用者 ID 權杖中返回的聲明來獲取login_hint和domain_hint的值。

* **登錄提示**應設置為 ID`preferred_username`權杖中的聲明。

* **domain_hint** domain_hint僅在使用 /common 許可權時才需要傳遞。 域提示由租戶 ID（tid） 確定。  如果`tid`ID 權杖中的聲明是`9188040d-6c67-4c5b-b112-36a304b66dad`消費者。 否則，它是組織。

有關登錄提示和域提示的值的詳細資訊，[請閱讀此處](v2-oauth2-implicit-grant-flow.md)。

> [!Note]
> 不能同時傳遞 SID 和login_hint。 這將導致錯誤回應。

## <a name="sso-without-msaljs-login"></a>SSO 沒有 MSAL.js 登錄

根據設計，MSAL.js 要求調用登錄方法，在獲取 API 權杖之前建立使用者上下文。 由於登錄方法是互動式的，因此使用者將看到提示。

在某些情況下，應用程式可以通過在另一個應用程式中啟動的身份驗證來訪問經過身份驗證的使用者的上下文或 ID 權杖，並希望利用 SSO 獲取權杖，而無需首先通過 MSAL.js 登錄。

例如：使用者被登錄到父 Web 應用程式，該應用程式承載另一個作為載入項或外掛程式運行的 JavaScript 應用程式。

此方案中的 SSO 體驗可以實現如下：

將`sid`可用（或`login_hint`可選`domain_hint`）作為請求參數傳遞給 MSAL.js`acquireTokenSilent`調用，如下所示：

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

## <a name="sso-in-adaljs-to-msaljs-update"></a>SSO 在 ADAL.js 到 MSAL.js 更新

MSAL.js 為 Azure AD 身份驗證方案帶來了 ADAL.js 的功能同位。 為了使從 ADAL.js 遷移到 MSAL.js 變得簡單，並避免提示使用者再次登錄，庫在 ADAL.js 緩存中讀取表示使用者會話的 ID 權杖，並在 MSAL.js 中無縫地在使用者中簽名。  

要利用從 ADAL.js 更新時的單一登入 （SSO） 行為，您需要確保庫用於`localStorage`緩存權杖。 在初始`cacheLocation`化`localStorage`時將 設置為 MSAL.js 和 ADAL.js 配置，如下所示：


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

配置後，MSAL.js 將能夠讀取 ADAL.js 中經過身份驗證的使用者的緩存狀態，並用它來在 MSAL.js 中提供 SSO。

## <a name="next-steps"></a>後續步驟

詳細瞭解 Azure AD 中的[單一登入會話和權杖存留期](active-directory-configurable-token-lifetimes.md)值。
