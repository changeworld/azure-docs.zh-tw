---
title: 互動式請求提示行為 （MSAL.js） |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何使用 JavaScript 的 Microsoft 身份驗證庫 （MSAL.js） 自訂互動式調用中的提示行為。
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
ms.openlocfilehash: 778e89655019a49a30904fbe8d8e6aedf1833e9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695971"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>MSAL.js 互動式請求中的提示行為

當使用者建立了具有多個使用者帳戶的活動 Azure AD 會話時，Azure AD 登錄頁將預設提示使用者在繼續登錄之前選擇帳戶。 如果只有一個經過 Azure AD 的身份驗證會話，則使用者將不會看到帳戶選擇體驗。

MSAL.js`loginRedirect`庫（以 v0.2.4 開頭）不會在互動式請求 （、`loginPopup``acquireTokenRedirect`和`acquireTokenPopup`） 期間發送提示參數，因此不強制任何提示行為。 對於使用 方法的`acquireTokenSilent`靜默權杖請求，MSAL.js 將提示參數`none`集傳遞給 。

根據應用程式方案，您可以通過在傳遞給方法的請求參數中設置提示參數來控制互動式請求的提示行為。 例如，如果要調用帳戶選擇體驗：

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


使用 Azure AD 進行身份驗證時，可以傳遞以下提示值：

**登錄：** 此值將強制使用者在身份驗證請求上輸入憑據。

**select_account：** 此值將為使用者提供帳戶選擇體驗，其中列出會話中的所有帳戶。

**同意：** 此值將調用 OAuth 同意對話方塊，該對話允許使用者向應用授予許可權。

**無：** 此值將確保使用者看不到任何互動式提示。 建議不要將此值傳遞給 MSAL.js 中的互動式方法，因為它可能有意外行為。 相反，使用`acquireTokenSilent`方法實現靜默調用。

## <a name="next-steps"></a>後續步驟

詳細瞭解 MSAL.js 庫使用的`prompt`[OAuth 2.0 隱式授予](v2-oauth2-implicit-grant-flow.md)協定中的參數。
