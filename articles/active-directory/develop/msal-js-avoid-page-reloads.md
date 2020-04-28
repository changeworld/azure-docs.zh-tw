---
title: 避免頁面重載（MSAL .js） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何在使用適用于 JavaScript 的 Microsoft 驗證程式庫（MSAL）以無訊息方式取得和更新權杖時，避免頁面重載。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77084948"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>使用 MSAL 以無訊息方式取得和更新權杖時，避免頁面重載
適用于 JavaScript 的 Microsoft 驗證程式庫（MSAL）會`iframe`使用隱藏的元素，在背景中以無訊息方式取得和更新權杖。 Azure AD 會將權杖傳回至權杖要求中指定的已註冊 redirect_uri （根據預設，這是應用程式的根頁面）。 因為回應是302，所以會導致 HTML 對應至中載入的`redirect_uri` `iframe`。 應用程式通常`redirect_uri`是根頁面，而這會使其重載。

在其他情況下，如果流覽至應用程式的根頁面需要驗證，則可能會導致`iframe`嵌套元素`X-Frame-Options: deny`或錯誤。

由於 MSAL 無法關閉 Azure AD 發行的302，而且必須處理傳回的權杖，因此無法防止`redirect_uri`在中載入。 `iframe`

若要避免再次重載整個應用程式或因此問題而造成的其他錯誤，請遵循這些因應措施。

## <a name="specify-different-html-for-the-iframe"></a>為 iframe 指定不同的 HTML

將 config `redirect_uri`上的屬性設定為不需要驗證的簡單頁面。 您必須確保它符合 Azure 入口網站中註冊的`redirect_uri` 。 這不會影響使用者的登入體驗，因為當使用者開始登入程式，並在登入完成後重新導向至正確的位置時，MSAL 會儲存起始頁。

## <a name="initialization-in-your-main-app-file"></a>在您的主要應用程式檔中初始化

如果您的應用程式結構化，因此有一個中央 JAVAscript 檔案定義應用程式的初始化、路由和其他專案，您可以根據應用程式是否在中`iframe`載入，而有條件地載入應用程式模組。 例如：

在 AngularJS 中： app .js

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

在 [角度] 中： app. 模組 ts

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
深入瞭解如何使用 MSAL[建立單一頁面應用程式（SPA）](scenario-spa-overview.md) 。