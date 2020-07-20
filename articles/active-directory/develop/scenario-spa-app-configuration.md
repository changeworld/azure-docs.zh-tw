---
title: 設定單一頁面應用程式-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立單一頁面應用程式（應用程式的程式碼設定）
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f159105046231ba5fb4e458cdd70d930a411a920
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80882330"
---
# <a name="single-page-application-code-configuration"></a>單一頁面應用程式：程式碼設定

瞭解如何為您的單一頁面應用程式（SPA）設定程式碼。

## <a name="msal-libraries-that-support-implicit-flow"></a>支援隱含流程的 MSAL 程式庫

Microsoft 身分識別平臺提供下列 Microsoft 驗證程式庫（MSAL）程式庫，可透過使用業界建議的安全性作法來支援隱含流程：

| MSAL 程式庫 | 描述 |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | 一般的 JavaScript 程式庫，可用於透過 JavaScript 或 SPA 架構（例如，角度、Vue.js 和 React.js）所建立的任何用戶端 web 應用程式。 |
| ![MSAL 角度](media/sample-v2-code/logo_angular.png) <br/> [MSAL 角度](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | 核心 MSAL.js 程式庫的包裝函式，可簡化透過角度架構建立的單一頁面應用程式中使用。 |

## <a name="application-code-configuration"></a>應用程式代碼設定

在 MSAL 程式庫中，應用程式註冊資訊會在程式庫初始化期間以設定的形式傳遞。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

如需可設定選項的詳細資訊，請參閱[使用 MSAL.js初始化應用程式](msal-js-initializing-client-applications.md)。

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
// App.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id'
            }
        })
    ]
})

export class AppModule { }
```

---

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [登入和登出](scenario-spa-sign-in.md)
