---
title: 配置單頁應用 - 微軟識別平臺 |蔚藍
description: 瞭解如何構建單頁應用程式（應用的代碼配置）
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160078"
---
# <a name="single-page-application-code-configuration"></a>單頁應用程式：代碼配置

瞭解如何為單頁應用程式 （SPA） 配置代碼。

## <a name="msal-libraries-that-support-implicit-flow"></a>支援隱式流的 MSAL 庫

Microsoft 標識平臺提供以下 Microsoft 身份驗證庫 （MSAL） 庫，以便使用行業推薦的安全實踐支援隱式流：  

| MSAL 程式庫 | 描述 |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | 普通 JavaScript 庫，用於通過 JavaScript 或 SPA 框架（如 Angular、Vue.js 和 React.js）構建的任何用戶端 Web 應用。 |
| ![MSAL 角](media/sample-v2-code/logo_angular.png) <br/> [MSAL 角](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | 核心 MSAL.js 庫的包裝，以簡化通過 Angular 框架構建的單頁應用的使用。 此庫處於預覽狀態，並且某些 Angular 版本和瀏覽器都有[已知問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular)。 |

## <a name="application-code-configuration"></a>應用程式代碼配置

在 MSAL 庫中，應用程式註冊資訊在庫初始化期間作為配置傳遞。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

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

有關可配置選項的詳細資訊，請參閱[使用 MSAL.js 初始化應用程式](msal-js-initializing-client-applications.md)。

# <a name="angular"></a>[Angular](#tab/angular)

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
