---
title: 初始化 MSAL 的用戶端應用程式 |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何使用適用于 JavaScript 的 Microsoft 驗證程式庫（MSAL）初始化用戶端應用程式。
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
ms.openlocfilehash: fbd700c787a844fa7538ed198f76ed5c06af2c28
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81010149"
---
# <a name="initialize-client-applications-using-msaljs"></a>使用 MSAL 初始化用戶端應用程式
本文說明如何將 Microsoft Authentication Library for JavaScript （MSAL）與使用者代理程式應用程式的實例進行初始化。 使用者代理程式應用程式是一種公用用戶端應用程式，其中用戶端程式代碼會在使用者代理程式（例如網頁瀏覽器）中執行。 這些用戶端不會儲存秘密，因為瀏覽器內容可公開存取。 若要深入瞭解用戶端應用程式類型和應用程式設定選項，請參閱[總覽](msal-client-applications.md)。

## <a name="prerequisites"></a>Prerequisites
在初始化應用程式之前，您必須先向[Azure 入口網站註冊](scenario-spa-app-registration.md)，讓您的應用程式可以與 Microsoft 身分識別平臺整合。 註冊之後，您可能需要下列資訊（可在 Azure 入口網站中找到）：

- 用戶端識別碼（代表應用程式 GUID 的字串）
- 應用程式的身分識別提供者 URL （名為實例）和登入物件。 這兩個參數統稱為授權單位。
- 租使用者識別碼，如果您要撰寫僅供貴組織使用的企業營運應用程式（也稱為單一租使用者應用程式）。
- 對於 web 應用程式，您也必須設定 redirectUri，其中身分識別提供者會使用安全性權杖傳回您的應用程式。

## <a name="initializing-applications"></a>初始化應用程式

您可以在一般 JavaScript/Typescript 應用程式中使用 MSAL，如下所示。 藉由具現化`UserAgentApplication`設定物件，初始化 MSAL 驗證內容。 初始化 MSAL 所需的最小設定是您應用程式的 clientID，您應該從應用程式註冊入口網站取得該設定。

針對具有重新導向流程（`loginRedirect`和`acquireTokenRedirect`）的驗證方法，在 MSAL 或更早版本中，您必須透過`handleRedirectCallback()`方法明確註冊成功或錯誤的回呼。 這是必要的，因為重新導向流程不會傳回承諾，因為方法具有快顯體驗。 這在 MSAL 版本1.3.0 中是選擇性的。

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

MSAL 設計成具有單一實例和設定， `UserAgentApplication`以代表單一驗證內容。 不建議使用多個實例，因為它們會在瀏覽器中造成衝突的快取專案和行為。

## <a name="configuration-options"></a>設定選項

MSAL 具有如下所示的設定物件，可提供可供建立實例的`UserAgentApplication`可設定選項群組。

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

以下是目前在 config 物件中支援的可設定選項總數：

- **clientID**：必要。 您的應用程式的 clientID 應該會從應用程式註冊入口網站取得。

- **授權**單位：選擇性。 URL，指出 MSAL 可向要求權杖的目錄。 預設值為：`https://login.microsoftonline.com/common`。
    * 在&lt;Azure AD 中，其格式為 HTTPs://實例&gt;/&lt;物件&gt;，其中&lt;instance&gt;是身分識別提供者網域（例如`https://login.microsoftonline.com`），而&lt;物件&gt;則是代表登入物件的識別碼。 這可以是下列值：
        * `https://login.microsoftonline.com/<tenant>`-tenant 是與租使用者相關聯的網域（例如 contoso.onmicrosoft.com），或代表僅用`TenantID`來登入特定組織使用者之目錄屬性的 GUID。
        * `https://login.microsoftonline.com/common`-用來以公司和學校帳戶或 Microsoft 個人帳戶登入使用者。
        * `https://login.microsoftonline.com/organizations/`-用來以公司和學校帳戶登入使用者。
        * `https://login.microsoftonline.com/consumers/`-用來登入僅具有個人 Microsoft 帳戶（live）的使用者。
    * 在 Azure AD B2C 中，其格式`https://<instance>/tfp/<tenant>/<policyName>/`為，其中 instance 是 Azure AD B2C 網域，也就是 {您的租使用者名稱}. .b2clogin.com .com，租使用者是 Azure AD B2C 租使用者的名稱，也就是 {您的 tenant-name}. onmicrosoft .Com，policyName 是要套用的 B2C 原則名稱。


- **validateAuthority**：選擇性。  驗證權杖的簽發者。 預設值為 `true`。 針對 B2C 應用程式，由於授權值是已知的，而且每個原則可能有所不同，因此授權單位驗證將無法使用，且必須`false`設定為。

- **redirectUri**：選擇性。  應用程式的重新導向 URI，您的應用程式可在此傳送及接收驗證回應。 其必須完全符合您在入口網站中註冊的其中一個重新導向 Uri。 預設為 `window.location.href`。

- **postLogoutRedirectUri**：選擇性。  在登出之後將`postLogoutRedirectUri`使用者重新導向至。預設為`redirectUri`。

- **navigateToLoginRequestUrl**：選擇性。 在登入之後關閉預設導覽至起始頁的功能。 預設值為 true。 這僅用於重新導向流程。

- **cacheLocation**：選擇性。  將瀏覽器儲存設定`localStorage`為`sessionStorage`或。 預設值為 `sessionStorage`。

- **storeAuthStateInCookie**：選擇性。  此旗標是在 MSAL 中引進，以修正 Microsoft Internet Explorer 和 Microsoft Edge 上的[驗證迴圈問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones)。 啟用旗`storeAuthStateInCookie`標為 true 可利用此修正程式。 當此功能啟用時，MSAL 會將驗證要求狀態儲存在瀏覽器 cookie 中，以驗證授權流程。 根據預設，此旗標會`false`設定為。

- **記錄器**：選擇性。  具有回呼實例的記錄器物件，可由開發人員提供，以自訂的方式取用和發行記錄。 如需傳遞記錄器物件的詳細資訊，請參閱[使用 msal 進行記錄](msal-logging.md)。

- **loadFrameTimeout**：選擇性。  在 Azure AD 的權杖更新回應應該被視為超時之前的非使用狀態毫秒數。預設值為6秒。

- **tokenRenewalOffsetSeconds**：選擇性。 毫秒數，設定在到期前更新權杖所需的位移時間範圍。 預設值為300毫秒。

- **navigateFrameWait**：選擇性。 在隱藏的 iframe 導覽至其目的地之前，設定等候時間的毫秒數。 預設值為500毫秒。

這些僅適用于從 MSAL 角包裝函式程式庫傳遞：
- **unprotectedResources**：選擇性。  未受保護資源的 Uri 陣列。 MSAL 不會將權杖附加至具有這些 URI 的連出要求。 預設為 `null`。

- **protectedResourceMap**：選擇性。  這是將資源對應到 MSAL 用來在 Web API 呼叫中自動附加存取權杖的範圍。 會取得資源的單一存取權杖。 因此，您可以將特定的資源路徑對應如下： {https://graph.microsoft.com/v1.0/me""，["user. read"]}，或資源的應用程式 URL，如下所示https://graph.microsoft.com/： {""，["user. read"，"mail. send"]}。 這是 CORS 呼叫的必要參數。 預設為 `null`。
