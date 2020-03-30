---
title: 初始化 MSAL.js 用戶端應用 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何使用適用于 JavaScript 的 Microsoft 身份驗證庫 （MSAL.js） 初始化用戶端應用程式。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: a0a2c5fc971c3f1f3283d95c5617bdf1e88a6a58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084035"
---
# <a name="initialize-client-applications-using-msaljs"></a>使用 MSAL.js 初始化用戶端應用程式
本文介紹使用使用者代理應用程式的實例初始化 JavaScript 的 Microsoft 身份驗證庫 （MSAL.js）。 使用者代理應用程式是公共用戶端應用程式的一種形式，其中用戶端代碼在使用者代理（如 Web 瀏覽器）中執行。 這些用戶端不存儲機密，因為瀏覽器上下文是可公開訪問的。 要瞭解有關用戶端應用程式類型和應用程式佈建選項的詳細資訊，請閱讀[概述](msal-client-applications.md)。

## <a name="prerequisites"></a>Prerequisites
在初始化應用程式之前，首先需要[將其註冊到 Azure 門戶](scenario-spa-app-registration.md)，以便應用可以與 Microsoft 標識平臺集成。 註冊後，您可能需要以下資訊（可在 Azure 門戶中找到）：

- 用戶端 ID（表示應用程式的 GUID 的字串）
- 標識提供程式 URL（名為實例）和應用程式的登錄訪問群體。 這兩個參數統稱為許可權。
- 僅為您的組織（也稱為單租戶應用程式）編寫業務線應用程式，則租戶 ID。
- 對於 Web 應用，您還必須設置重定向 Uri，其中標識提供程式將返回到您的應用程式與安全權杖。

## <a name="initializing-applications"></a>初始化應用程式

您可以在普通的 JavaScript/Typescript 應用程式中按如下方式使用 MSAL.js。 通過`UserAgentApplication`具現化設定物件來初始化 MSAL 身份驗證上下文。 初始化 MSAL.js 所需的最小配置是應用程式的用戶端 ID，您應該從應用程式註冊門戶獲取該配置。

對於具有重定向流 （`loginRedirect`和`acquireTokenRedirect`） 的身份驗證方法，您需要顯式註冊回檔以通過`handleRedirectCallback()`方法進行成功或錯誤。 這是必要的，因為重定向流不會返回承諾，因為具有彈出式體驗的方法會返回承諾。

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    }
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

// (optional when using redirect methods) register redirect call back for Success or Error
myMSALObj.handleRedirectCallback(authCallback);
```

MSAL.js 旨在具有單個`UserAgentApplication`實例和配置，以表示單個身份驗證上下文。 不建議使用多個實例，因為它們會導致瀏覽器中的緩存條目和行為衝突。

## <a name="configuration-options"></a>設定選項

MSAL.js 具有如下所示的設定物件，該物件提供可用於創建 實例的可配置選項分組`UserAgentApplication`。

```javascript
type storage = "localStorage" | "sessionStorage";

// Protocol Support
export type AuthOptions = {
    clientId: string;
    authority?: string;
    validateAuthority?: boolean;
    redirectUri?: string | (() => string);
    postLogoutRedirectUri?: string | (() => string);
    navigateToLoginRequestUrl?: boolean;
};

// Cache Support
export type CacheOptions = {
    cacheLocation?: CacheLocation;
    storeAuthStateInCookie?: boolean;
};

// Library support
export type SystemOptions = {
    logger?: Logger;
    loadFrameTimeout?: number;
    tokenRenewalOffsetSeconds?: number;
    navigateFrameWait?: number;
};

// Developer App Environment Support
export type FrameworkOptions = {
    isAngular?: boolean;
    unprotectedResources?: Array<string>;
    protectedResourceMap?: Map<string, Array<string>>;
};

// Configuration Object
export type Configuration = {
    auth: AuthOptions,
    cache?: CacheOptions,
    system?: SystemOptions,
    framework?: FrameworkOptions
};
```

以下是設定物件中當前支援的可配置選項組總數：

- **用戶端 ID**：必需。 應用程式的用戶端 ID，應從應用程式註冊門戶獲取此項。

- **許可權**：可選。 指示 MSAL 可以從中請求權杖的目錄的 URL。 預設值為：`https://login.microsoftonline.com/common`。
    * 在 Azure AD 中，它是實例&lt;&gt;/&lt;訪問群體&gt;HTTPs://表單&lt;，&gt;其中實例是標識提供程式域（例如`https://login.microsoftonline.com`），&lt;訪問群體&gt;是表示登錄訪問群體的識別碼。 這可以是以下值：
        * `https://login.microsoftonline.com/<tenant>`- 租戶是與租戶關聯的域，如contoso.onmicrosoft.com或表示僅用於登錄到特定組織使用者登錄的目錄`TenantID`屬性的 GUID。
        * `https://login.microsoftonline.com/common`- 用於使用工作帳戶和學校帳戶或 Microsoft 個人帳戶登錄使用者。
        * `https://login.microsoftonline.com/organizations/`- 用於使用工作和學校帳戶登錄使用者。
        * `https://login.microsoftonline.com/consumers/`- 用於僅使用個人 Microsoft 帳戶（即時）登錄使用者。
    * 在 Azure AD B2C 中，`https://<instance>/tfp/<tenant>/<policyName>/`它是表單 ，其中實例是 Azure AD B2C 域，即 [您的租戶名稱].b2clogin.com，租戶是 Azure AD B2C 租戶的名稱，即 [您的租戶名稱].onmicrosoft.com，策略名稱是要應用的 B2C 策略的名稱。


- **驗證授權**：可選。  驗證權杖的頒發者。 預設值為 `true`。 對於 B2C 應用程式，由於許可權值已知，並且每個策略可能不同，因此頒發許可權驗證將不起作用，必須設置為`false`。

- **重定向：** 可選。  應用程式的重新導向 URI，您的應用程式可在此傳送及接收驗證回應。 它必須與您在門戶中註冊的重定向 URI 之一完全符合。 預設為 `window.location.href`。

- **後記錄重新定向：** 可選。  登出後將使用者重定向到`postLogoutRedirectUri`。預設值為`redirectUri`。

- **導航到登錄請求URL：** 可選。 能夠在登錄後關閉預設導航以啟動頁面。 預設值為 true。 這僅用於重定向流。

- **緩存位置**： 可選。  將瀏覽器存儲設置為`localStorage``sessionStorage`或 。 預設值為 `sessionStorage`。

- **存儲AuthStateInCookie：** 可選。  此標誌在 MSAL.js v0.2.2 中引入，作為 Microsoft Internet 資源管理器和 Microsoft Edge 上的[身份驗證迴圈問題的](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones)修復程式。 使標誌`storeAuthStateInCookie`為 true 以利用此修復程式。 啟用此功能後，MSAL.js 將在瀏覽器 Cookie 中存儲驗證身份驗證流所需的身份驗證請求狀態。 預設情況下，此標誌設置為`false`。

- **記錄器**：可選。  具有回檔實例的記錄器物件，開發人員可以提供該物件，以便以自訂方式使用和發佈日誌。 有關傳遞記錄器物件的詳細資訊，請參閱[使用 msal.js 進行日誌記錄](msal-logging.md)。

- **負載幀超時**：可選。  Azure AD 的權杖續訂回應之前的不活動毫秒數應被視為超時。預設值為 6 秒。

- **權杖續訂偏移秒**：可選。 設置到期前續訂權杖所需的偏移視窗的毫秒數。 預設值為 300 毫秒。

- **導航幀等待**：可選。 設置隱藏 iframe 導航到其目標的等待時間的毫秒數。 預設值為 500 毫秒。

這些僅適用于從 MSAL 角包裝庫向下傳遞：
- **未受保護的資源**：可選。  未受保護的資源 URI 陣列。 MSAL 不會將權杖附加到具有這些 URI 的傳出請求。 預設為 `null`。

- **受保護的資源映射**：可選。  這是將資源映射到 MSAL 用於在 Web API 呼叫中自動附加訪問權杖的作用域。 為資源獲取單個訪問權杖。 因此，您可以按照如下方式映射特定資源路徑："{"、"user.read"}https://graph.microsoft.com/v1.0/me或資源的應用 URL 為："\"""user.read"，"https://graph.microsoft.com/郵件.send"*。 這是 CORS 調用所必需的。 預設為 `null`。
