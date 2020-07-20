---
title: 互動式要求提示行為（MSAL.js） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何使用適用于 JavaScript 的 Microsoft 驗證程式庫（MSAL.js），在互動式呼叫中自訂提示行為。
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 85f5269c3c2616070b826ecc38735c31617db537
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85477544"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>MSAL.js 互動式要求中的提示行為

當使用者使用多個使用者帳戶建立 active Azure AD 會話時，Azure AD 登入] 頁面預設會提示使用者選取帳戶，然後再繼續登入。 如果只有一個已驗證的會話與 Azure AD，則使用者不會看到帳戶選取體驗。

MSAL.js 程式庫（從 v 0.2.4 開始）不會在互動式要求（ `loginRedirect` 、和）期間傳送提示 `loginPopup` 參數 `acquireTokenRedirect` `acquireTokenPopup` ，因此不會強制執行任何提示行為。 針對使用方法的無訊息 token 要求 `acquireTokenSilent` ，MSAL.js 會將提示參數設定為 `none` 。

根據您的應用程式案例，您可以在傳遞給方法的要求參數中設定 prompt 參數，以控制互動式要求的提示行為。 例如，如果您想要叫用帳戶選取體驗：

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


使用 Azure AD 進行驗證時，可以傳遞下列提示值：

**登入：** 此值會強制使用者輸入驗證要求的認證。

**select_account：** 這個值會提供使用者一個帳戶選取體驗，列出會話中的所有帳戶。

**同意：** 此值將會叫用 OAuth 同意對話方塊，讓使用者授與許可權給應用程式。

**無：** 此值可確保使用者看不到任何互動式提示。 建議您不要將此值傳遞至 MSAL.js 中的互動式方法，因為它可能會有未預期的行為。 相反地，請使用 `acquireTokenSilent` 方法來達到無訊息呼叫。

## <a name="next-steps"></a>後續步驟

深入瞭解 MSAL.js 連結 `prompt` 庫使用的[OAuth 2.0 隱含授](v2-oauth2-implicit-grant-flow.md)與通訊協定中的參數。
