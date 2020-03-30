---
title: 在身份驗證請求 （MSAL.js） 中傳遞自訂狀態 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何使用 JavaScript 的 Microsoft 身份驗證庫 （MSAL.js） 在身份驗證請求中傳遞自訂狀態參數值。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/16/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1c05956f83ad3a6491627be8916fac2c8be2b7ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084945"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>使用 MSAL.js 在身份驗證請求中傳遞自訂狀態

OAuth 2.0 定義的*狀態*參數包含在身份驗證請求中，並且還會返回權杖回應，以防止跨網站請求偽造攻擊。 預設情況下，JavaScript 的 Microsoft 身份驗證庫 （MSAL.js） 在身份驗證請求中傳遞隨機生成的唯一*狀態*參數值。

狀態參數還可用於在重定向之前對應用的狀態資訊進行編碼。 您可以將使用者的狀態傳遞給應用中，例如他們打開的頁面或視圖，作為此參數的輸入。 MSAL.js 庫允許您在`Request`物件中傳遞自訂狀態作為狀態參數：

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
> 如果要跳過緩存的權杖並轉到伺服器，請將布林`forceRefresh`傳遞到用於發出登錄/權杖請求的身份驗證參數物件中。
> `forceRefresh`預設情況下不應使用，因為性能會影響應用程式。
> 依賴緩存將為使用者提供更好的體驗。
> 跳過緩存應僅在您知道當前緩存的資料沒有最新資訊的情況下使用。
> 例如，將角色添加到需要使用更新角色獲取新權杖的使用者的管理員工具。

例如：

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

發送請求時，傳入狀態將追加到 MSAL.js 設置的唯一 GUID。 返回回應時，MSAL.js 檢查狀態匹配，然後將`Response`在物件中傳遞的自訂返回為`accountState`。

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

要瞭解更多資訊，請閱讀有關使用 MSAL.js[構建單頁應用程式 （SPA）。](scenario-spa-overview.md)