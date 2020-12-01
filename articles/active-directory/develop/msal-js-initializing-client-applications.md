---
title: MSAL.js 用戶端應用程式初始化 |蔚藍
titleSuffix: Microsoft identity platform
description: '瞭解如何使用適用于 JavaScript 的 Microsoft 驗證程式庫 ( # A0) 來初始化用戶端應用程式。'
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/17/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 6b5093c5a1a45aed3493fabd7a362b0579998171
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96343581"
---
# <a name="initialize-client-applications-using-msaljs"></a>使用 MSAL.js 初始化用戶端應用程式

本文說明如何使用使用者代理程式應用程式的實例，將適用于 JavaScript ( # A0) 的 Microsoft 驗證程式庫初始化。

使用者代理程式應用程式是一種公開用戶端應用程式，其中用戶端程式代碼會在使用者代理程式（例如網頁瀏覽器）中執行。 這類用戶端不會儲存秘密，因為瀏覽器內容可供公開存取。

若要深入瞭解用戶端應用程式類型和應用程式設定選項，請參閱 [MSAL 中的公用和機密用戶端應用](msal-client-applications.md)程式。

## <a name="prerequisites"></a>必要條件

在初始化應用程式之前，您必須先向 [Azure 入口網站註冊它](scenario-spa-app-registration.md)，並在您的應用程式與 Microsoft 身分識別平臺之間建立信任關係。

註冊您的應用程式之後，您將需要可在 Azure 入口網站中找到的部分或所有下列值。

| 值 | 必要 | 說明 |
|:----- | :------: | :---------- |
| 應用程式 (用戶端) 識別碼 | 必要 | 可在 Microsoft 身分識別平臺中唯一識別您應用程式的 GUID。 |
| 授權單位 | 選擇性 | 身分識別提供者 URL (*實例*) 和您應用程式的登 *入物件* 。 當使用者串連時，實例和登入物件會構成 *授權* 單位。 |
| 目錄 (租用戶) 識別碼 | 選擇性 | 如果您要建立僅供組織使用的企業營運應用程式（通常稱為 *單一租使用者應用程式*），請指定此項。 |
| 重新導向 URI | 選擇性 | 如果您要建立 web 應用程式，則 `redirectUri` 會指定身分識別提供者 (Microsoft 身分識別平臺) 應傳回其所發出的安全性權杖。 |

## <a name="initialize-msaljs-2x-apps"></a>初始化 MSAL.js 2.x 應用程式

使用[設定物件具][msal-js-configuration]現化[PublicClientApplication][msal-js-publicclientapplication] ，以初始化 MSAL authentication 內容。 [必要的最小設定] 屬性是 `clientID` 您應用程式的，在 Azure 入口網站的應用程式註冊的 [**總覽**] 頁面上顯示為 **應用程式 (用戶端) 識別碼**。

以下是範例設定物件和具現化 `PublicClientApplication` ：

```javascript
const msalConfig = {
    auth: {
        clientId: "11111111-1111-1111-111111111111",
        authority: "https://login.microsoftonline.com/common",
        knownAuthorities: [],
        redirectUri: "https://localhost:3001",
        postLogoutRedirectUri: "https://localhost:3001/logout",
        navigateToLoginRequestUrl: true
    },
    cache: {
        cacheLocation: "sessionStorage",
        storeAuthStateInCookie: false
    },
    system: {
        loggerOptions: {
            loggerCallback: (level: LogLevel, message: string, containsPii: boolean): void => {
                if (containsPii) {
                    return;
                }
                switch (level) {
                    case LogLevel.Error:
                        console.error(message);
                        return;
                    case LogLevel.Info:
                        console.info(message);
                        return;
                    case LogLevel.Verbose:
                        console.debug(message);
                        return;
                    case LogLevel.Warning:
                        console.warn(message);
                        return;
                }
            },
            piiLoggingEnabled: false
        },
        windowHashTimeout: 60000,
        iframeHashTimeout: 6000,
        loadFrameTimeout: 0
    }
};

// Create an instance of PublicClientApplication
const msalInstance = new PublicClientApplication(msalConfig);

// Handle the redirect flows
msalInstance.handleRedirectPromise().then((tokenResponse) => {
    // Handle redirect response
}).catch((error) => {
    // Handle redirect error
});
```

### `handleRedirectPromise`

當您的應用程式使用重新導向流程時，叫用 [handleRedirectPromise][msal-js-handleredirectpromise] 。 使用重新導向流程時， `handleRedirectPromise` 應該在每個頁面載入時執行。

承諾有三個可能的結果：

- `.then` 已叫用且 `tokenResponse` 為 truthy：應用程式從成功的重新導向作業傳回。
- `.then` 會叫用，且 `tokenResponse` 假 (`null`) ：應用程式不會從重新導向作業傳回。
- `.catch` 叫用：應用程式從重新導向作業傳回，而且發生錯誤。

## <a name="initialize-msaljs-1x-apps"></a>初始化 MSAL.js 1.x 應用程式

使用設定物件具現化 [UserAgentApplication][msal-js-useragentapplication] ，以初始化 MSAL 1.x 驗證內容。 [必要的最小設定] 屬性是 `clientID` 您應用程式的，在 Azure 入口網站的應用程式註冊的 [**總覽**] 頁面上顯示為 **應用程式 (用戶端) 識別碼**。

針對在 MSAL.js 1.2. x 或更早版本中具有重新導向流程 ([loginRedirect][msal-js-loginredirect] 和 [>acquiretokenredirect][msal-js-acquiretokenredirect]) 的驗證方法，您必須透過方法明確地註冊成功或錯誤的回呼 `handleRedirectCallback()` 。 MSAL.js 1.2. x 和更早版本中需要明確註冊回呼，因為重新導向流程不會傳回像是具有快顯體驗的方法的承諾。 在 MSAL.js 版本 1.3. x 和更新版本中，註冊回呼是 *選擇性* 的。

```javascript
// Configuration object constructed
const msalConfig = {
    auth: {
        clientId: "11111111-1111-1111-111111111111"
    }
}

// Create UserAgentApplication instance
const msalInstance = new UserAgentApplication(msalConfig);

function authCallback(error, response) {
    // Handle redirect response
}

// Register a redirect callback for Success or Error (when using redirect methods)
// **REQUIRED** in MSAL.js 1.2.x and earlier
// **OPTIONAL** in MSAL.js 1.3.x and later
msalInstance.handleRedirectCallback(authCallback);
```

## <a name="single-instance-and-configuration"></a>單一實例和設定

MSAL.js 1.x 和2.x 都設計成分別具有或的單一實例和設定， `UserAgentApplication` `PublicClientApplication` 以代表單一驗證內容。

不建議或的多個實例， `UserAgentApplication` `PublicClientApplication` 因為它們會導致瀏覽器中的快取專案和行為發生衝突。

## <a name="next-steps"></a>後續步驟

此 MSAL.js 在 GitHub 上的2.x 程式碼範例示範如何使用[設定物件來][msal-js-configuration]具現化[PublicClientApplication][msal-js-publicclientapplication] ：

[Azure-範例/ms-身分識別-javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2)

<!-- LINKS - External -->
[msal-browser]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/
[msal-core]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/
[msal-js-acquiretokenredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/classes/_useragentapplication_.useragentapplication.html#acquiretokenredirect
[msal-js-configuration]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/modules/_configuration_.html
[msal-js-handleredirectpromise]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/classes/_src_app_publicclientapplication_.publicclientapplication.html#handleredirectpromise
[msal-js-loginredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/classes/_useragentapplication_.useragentapplication.html#loginredirect
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/classes/_src_app_publicclientapplication_.publicclientapplication.html
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/modules/_useragentapplication_.html
