---
title: '在驗證要求中傳遞自訂狀態 ( # A0) |蔚藍'
titleSuffix: Microsoft identity platform
description: '瞭解如何使用適用于 JavaScript 的 Microsoft 驗證程式庫，在驗證要求中傳遞自訂狀態參數值 ( # A0) 。'
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
ms.openlocfilehash: 5c1fad96ea6e3b75b3afdfd4a4d3baac43308541
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063666"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>使用 MSAL.js 在驗證要求中傳遞自訂狀態

如同 OAuth 2.0 所定義的 *狀態* 參數會包含在驗證要求中，而且也會在權杖回應中傳回，以防止跨網站偽造要求攻擊。 根據預設，適用于 JavaScript 的 Microsoft 驗證程式庫 ( # A0) 會在驗證要求中傳遞隨機產生的唯一 *狀態* 參數值。

在重新導向之前，state 參數也可以用來將應用程式狀態的資訊編碼。 您可以在應用程式中傳遞使用者的狀態，例如它們所在的頁面或視圖，作為此參數的輸入。 MSAL.js 程式庫可讓您將自訂狀態傳遞為物件中的狀態參數 `Request` ：

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
> 如果您想要略過快取的權杖並移至伺服器，請將布林值傳入 `forceRefresh` 用來提出登入/權杖要求的 AuthenticationParameters 物件。
> `forceRefresh` 因為對您應用程式的效能影響，所以預設不應使用。
> 依賴快取會為您的使用者提供更好的體驗。
> 略過快取應該只用于您知道目前快取的資料沒有最新資訊的情況。
> 例如，將角色新增至需要以更新角色取得新權杖之使用者的系統管理工具。

例如：

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

傳送要求時，會將傳入的狀態附加至 MSAL.js 的唯一 GUID 集。 傳迴響應時，MSAL.js 會檢查狀態是否相符，然後將物件中的自訂傳入狀態傳回 `Response` 為 `accountState` 。

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

若要深入瞭解，請參閱使用 MSAL.js [建立單一頁面應用程式 (SPA) ](scenario-spa-overview.md) 。