---
title: 設定單頁應用 - 微軟識別平臺 |蔚藍
description: 瞭解如何建構單頁應用程式(應用程式的代碼設定)
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882330"
---
# <a name="single-page-application-code-configuration"></a>單頁應用程式:代碼設定

瞭解如何為單頁應用程式 (SPA) 設定代碼。

## <a name="msal-libraries-that-support-implicit-flow"></a>支援隱式流的 MSAL 函式庫

Microsoft 識別平臺提供以下 Microsoft 身份驗證庫 (MSAL) 函式庫,以便使用產業推薦的安全實務支援隱式流:

| MSAL 程式庫 | 描述 |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | 普通 JavaScript 庫,用於透過 JavaScript 或 SPA 框架(如 Angular、Vue.js 和 React.js)構建的任何用戶端 Web 應用。 |
| ![MSAL 角](media/sample-v2-code/logo_angular.png) <br/> [MSAL 角](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | 核心 MSAL.js 庫的包裝,以簡化通過 Angular 框架構建的單頁應用的使用。 |

## <a name="application-code-configuration"></a>應用程式代碼設定

在 MSAL 庫中,應用程式註冊資訊在庫初始化期間作為配置傳遞。

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

關於可設定選項的詳細資訊,請參閱[使用 MSAL.js 初始化應用程式](msal-js-initializing-client-applications.md)。

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
