---
title: 在驗證要求中傳遞自訂狀態（MSAL.js） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何使用適用于 JavaScript 的 Microsoft 驗證程式庫（MSAL.js），在驗證要求中傳遞自訂狀態參數值。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 01/16/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 840c371e63aacf8ef410cbf84cc9f68137dd77df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85477578"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>使用 MSAL.js 在驗證要求中傳遞自訂狀態

*狀態*參數（如 OAuth 2.0 所定義）包含在驗證要求中，也會在權杖回應中傳回，以防止跨網站偽造要求攻擊。 根據預設，適用于 JavaScript 的 Microsoft 驗證程式庫（MSAL.js）會在驗證要求中傳遞隨機產生的唯一*狀態*參數值。

State 參數也可以在重新導向之前用來編碼應用程式狀態的資訊。 您可以在應用程式中傳遞使用者的狀態，例如其所在的頁面或視圖，做為此參數的輸入。 MSAL.js 程式庫可讓您在物件中傳遞自訂狀態作為狀態參數 `Request` ：

```javascript
// Request type
export type AuthenticationParameters = {
    scopes?: Array<string>;
    extraScopesToConsent?: Array<string>;
    prompt?: string;
    extraQueryParameters?: QPDict;
    claimsRequest?: string;
    authority?: string;
    state?: string;
    correlationId?: string;
    account?: Account;
    sid?: string;
    loginHint?: string;
    forceRefresh?: boolean;
};
```

> [!Note]
> 如果您想要略過快取的權杖並移至伺服器，請將布林值傳入 `forceRefresh` 用來提出登入/token 要求的 AuthenticationParameters 物件。
> `forceRefresh`預設不應使用，因為對應用程式效能的影響。
> 依賴快取可讓您的使用者獲得更好的體驗。
> 略過快取僅適用于您知道目前快取的資料沒有最新資訊的情況。
> 例如，系統管理員工具，會將角色新增至需要以更新角色取得新權杖的使用者。

例如：

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

傳入的狀態會附加至在傳送要求時 MSAL.js 所設定的唯一 GUID。 傳迴響應時，MSAL.js 會檢查狀態是否相符，然後將物件中的自訂傳入狀態傳回 `Response` 為 `accountState` 。

```javascript
export type AuthResponse = {
    uniqueId: string;
    tenantId: string;
    tokenType: string;
    idToken: IdToken;
    accessToken: string;
    scopes: Array<string>;
    expiresOn: Date;
    account: Account;
    accountState: string;
};
```

若要深入瞭解，請參閱使用 MSAL.js[建立單一頁面應用程式（SPA）](scenario-spa-overview.md) 。