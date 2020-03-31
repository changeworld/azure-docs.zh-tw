---
title: 避免頁面重新載入 （MSAL.js） |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何在使用 JavaScript 的 Microsoft 身份驗證庫 （MSAL.js） 以靜默方式獲取和續訂權杖時避免頁面重新載入。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/29/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 63944a5a9af34c2d4cf98eeb870a730df49654e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084948"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>使用 MSAL.js 以靜默方式獲取和續訂權杖時，避免頁面重新載入
適用于 JavaScript 的 Microsoft 身份驗證庫 （MSAL.js） 使用隱藏`iframe`元素在後臺悄悄地獲取和續訂權杖。 Azure AD 將權杖返回到權杖請求中指定的已註冊redirect_uri（預設情況下，這是應用的根頁）。 由於回應是 302，因此會導致與 中的`redirect_uri`載入對應的`iframe`HTML。 通常，應用程式的`redirect_uri`是根頁，這導致它重新載入。

在其他情況下，如果導航到應用的根頁需要身份驗證，則可能導致嵌套`iframe`元素或`X-Frame-Options: deny`錯誤。

由於 MSAL.js 無法關閉 Azure AD 頒發的 302，並且需要處理返回的權杖，因此`redirect_uri`無法阻止 在`iframe`中載入 。

為了避免整個應用再次重新載入或由此引起的其他錯誤，請按照這些解決方法操作。

## <a name="specify-different-html-for-the-iframe"></a>為 iframe 指定不同的 HTML

在配置`redirect_uri`上將屬性設置為一個簡單的頁面，不需要身份驗證。 您必須確保它與 Azure 門戶中註冊時`redirect_uri`匹配。 這不會影響使用者的登錄體驗，因為當使用者開始登錄過程並在登錄完成後重定向回確切位置時，MSAL 會保存起始頁。

## <a name="initialization-in-your-main-app-file"></a>在主應用檔中初始化

如果應用的結構使有一個中央 JAVAscript 檔定義應用的初始化、路由和其他內容，則可以根據應用是否載入`iframe`有條件地載入應用模組。 例如：

在 AngularJS 中：app.js

```javascript
// Check that the window is an iframe and not popup
if (window !== window.parent && !window.opener) {
angular.module('todoApp', ['ui.router', 'MsalAngular'])
    .config(['$httpProvider', 'msalAuthenticationServiceProvider','$locationProvider', function ($httpProvider, msalProvider,$locationProvider) {
        msalProvider.init(
            // msal configuration
        );

        $locationProvider.html5Mode(false).hashPrefix('');
    }]);
}
else {
    angular.module('todoApp', ['ui.router', 'MsalAngular'])
        .config(['$stateProvider', '$httpProvider', 'msalAuthenticationServiceProvider', '$locationProvider', function ($stateProvider, $httpProvider, msalProvider, $locationProvider) {
            $stateProvider.state("Home", {
                url: '/Home',
                controller: "homeCtrl",
                templateUrl: "/App/Views/Home.html",
            }).state("TodoList", {
                url: '/TodoList',
                controller: "todoListCtrl",
                templateUrl: "/App/Views/TodoList.html",
                requireLogin: true
            })

            $locationProvider.html5Mode(false).hashPrefix('');

            msalProvider.init(
                // msal configuration
            );
        }]);
}
```

在角度：應用.module.ts

```javascript
// Imports...
@NgModule({
  declarations: [
    AppComponent,
    MsalComponent,
    MainMenuComponent,
    AccountMenuComponent,
    OsNavComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    ServiceWorkerModule.register('ngsw-worker.js', { enabled: environment.production }),
    MsalModule.forRoot(environment.MsalConfig),
    SuiModule,
    PagesModule
  ],
  providers: [
    HttpServiceHelper,
    {provide: HTTP_INTERCEPTORS, useClass: MsalInterceptor, multi: true},
    AuthService
  ],
  entryComponents: [
    AppComponent,
    MsalComponent
  ]
})
export class AppModule {
  constructor() {
    console.log('APP Module Constructor!');
  }

  ngDoBootstrap(ref: ApplicationRef) {
    if (window !== window.parent && !window.opener)
    {
      console.log("Bootstrap: MSAL");
      ref.bootstrap(MsalComponent);
    }
    else
    {
    //this.router.resetConfig(RouterModule);
      console.log("Bootstrap: App");
      ref.bootstrap(AppComponent);
    }
  }
}
```

Msal 元件：

```javascript
import { Component} from '@angular/core';
import { MsalService } from '@azure/msal-angular';

// This component is used only to avoid Angular reload
// when doing acquireTokenSilent()

@Component({
  selector: 'app-root',
  template: '',
})
export class MsalComponent {
  constructor(private Msal: MsalService) {
  }
}
```

## <a name="next-steps"></a>後續步驟
瞭解有關使用 MSAL.js[構建單頁應用程式 （SPA）](scenario-spa-overview.md)的更多詳細資訊。