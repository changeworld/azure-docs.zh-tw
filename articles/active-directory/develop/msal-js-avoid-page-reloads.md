---
title: '避免頁面重載 ( # A0) |蔚藍'
titleSuffix: Microsoft identity platform
description: '瞭解如何在使用適用于 JavaScript 的 Microsoft 驗證程式庫以無訊息方式取得和更新權杖時避免頁面重載 ( # A0) 。'
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/29/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 0c96d161e55261af1bbe04eae6ead1d245158d02
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064822"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>使用 MSAL.js 以無訊息方式取得和更新權杖時，請避免頁面重載
適用于 JavaScript 的 Microsoft 驗證程式庫 ( # A0) 使用隱藏 `iframe` 的元素，在背景中以無訊息方式取得和更新權杖。 Azure AD 會將權杖傳回權杖要求中所指定的註冊 redirect_uri (預設是應用程式的根頁面) 。 因為回應是302，所以會產生對應至中所載入之的 HTML `redirect_uri` `iframe` 。 應用程式通常 `redirect_uri` 是根頁面，而這會導致其重載。

在其他情況下，如果流覽至應用程式的根頁面需要驗證，它可能會導致嵌套的 `iframe` 元素或 `X-Frame-Options: deny` 錯誤。

由於 MSAL.js 無法關閉 Azure AD 發出的302，因此必須處理傳回的權杖，否則無法防止在 `redirect_uri` 中載入 `iframe` 。

若要避免再次重載整個應用程式或造成其他錯誤，請遵循這些因應措施。

## <a name="specify-different-html-for-the-iframe"></a>為 iframe 指定不同的 HTML

將設定 `redirect_uri` 上的屬性設定為不需要驗證的簡單頁面。 您必須確定它符合 `redirect_uri` Azure 入口網站中註冊的。 當使用者開始登入程式時，這不會影響使用者的登入體驗，並在登入完成後重新導向回到確切的位置。

## <a name="initialization-in-your-main-app-file"></a>在您的主要應用程式檔中初始化

如果您的應用程式已結構化，因此有一個中央 JAVAscript 檔案定義應用程式的初始化、路由和其他專案，則您可以根據應用程式是否在中載入來有條件地載入應用程式模組 `iframe` 。 例如：

在 AngularJS 中： app.js

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

以角度： app.config

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

MsalComponent:

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
深入瞭解如何使用 MSAL.js [建立單一頁面應用程式 (SPA) ](scenario-spa-overview.md) 。