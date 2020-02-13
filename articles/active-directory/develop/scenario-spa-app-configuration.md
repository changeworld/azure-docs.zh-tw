---
title: 設定單一頁面應用程式-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立單一頁面應用程式（應用程式的程式碼設定）
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f1e0bf44515aab18019b19b4f0a6f84183e5aac3
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160078"
---
# <a name="single-page-application-code-configuration"></a>單一頁面應用程式：程式碼設定

瞭解如何為您的單一頁面應用程式（SPA）設定程式碼。

## <a name="msal-libraries-that-support-implicit-flow"></a>支援隱含流程的 MSAL 程式庫

Microsoft 身分識別平臺提供下列 Microsoft 驗證程式庫（MSAL）程式庫，可透過使用業界建議的安全性作法來支援隱含流程：  

| MSAL 程式庫 | 描述 |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | 一般的 JavaScript 程式庫，可用於透過 JavaScript 或 SPA 架構（例如，角度、Vue 及回應 .js）建立的任何用戶端 web 應用程式。 |
| ![MSAL 角度](media/sample-v2-code/logo_angular.png) <br/> [MSAL 角度](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | 核心 MSAL 程式庫的包裝函式，可簡化透過角度架構建立的單一頁面應用程式中使用。 此程式庫目前為預覽狀態，並具有某些角度版本與瀏覽器的[已知問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular)。 |

## <a name="application-code-configuration"></a>應用程式代碼設定

在 MSAL 程式庫中，應用程式註冊資訊會在程式庫初始化期間以設定的形式傳遞。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

如需可設定選項的詳細資訊，請參閱[使用 MSAL 初始化應用程式](msal-js-initializing-client-applications.md)。

# <a name="angulartabangular"></a>[Angular](#tab/angular)

```javascript
//In app.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id'
            })]
         })

  export class AppModule { }
```

---

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [登入和登出](scenario-spa-sign-in.md)
