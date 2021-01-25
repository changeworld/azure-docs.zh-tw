---
title: 教學課程：建立 Angular 應用程式，使用 Microsoft 身分識別平台進行驗證 | Azure
titleSuffix: Microsoft identity platform
description: 在本教學課程中，您會建置 Angular 單頁應用程式 (SPA)，使用 Microsoft 身分識別平台來登入使用者，並取得存取權杖來代表他們呼叫 Microsoft Graph API。
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 03/05/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: 105353598a2af60c407bacf02b4527b2de84e450
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756155"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application"></a>教學課程：讓使用者登入並從 Angular 單頁應用程式呼叫 Microsoft Graph API

在本教學課程中，您會建置一個 Angular 單頁應用程式 (SPA)，將使用者登入，並呼叫 Microsoft Graph API。

本教學課程內容：

> [!div class="checklist"]
> * 使用 `npm` 建立 Angular 專案
> * 在 Azure 入口網站中註冊應用程式
> * 新增程式碼以支援使用者登入和登出
> * 新增程式碼以呼叫 Microsoft Graph API
> * 測試應用程式

## <a name="prerequisites"></a>必要條件

* 用於執行本機網頁伺服器的 [Node.js](https://nodejs.org/en/download/)。
* 用於修改專案檔的 [Visual Studio Code](https://code.visualstudio.com/download) 或其他編輯器。

## <a name="how-the-sample-app-works"></a>此範例應用程式的運作方式

![此圖顯示本教學課程中的範例應用程式如何產生](./media/tutorial-v2-angular/diagram-auth-flow-spa-angular.svg)

此教學課程中建立的範例應用程式可讓 Angular SPA 查詢 Microsoft Graph API，或查詢可接受由 Microsoft 身分識別平台所簽發權杖的 Web API。 使用適用於 Angular 的 Microsoft 驗證程式庫 (MSAL)，這是核心 MSAL.js 程式庫的包裝函式。 MSAL Angular 啟用 Angular 6+ 應用程式，藉由使用 Azure Active Directory (Azure AD) 來驗證企業使用者，同時驗證具有 Microsoft 帳戶和社交身分識別 (例如 Facebook、Google 和 LinkedIn) 的使用者。 程式庫也可啟用應用程式，並取得 Microsoft 雲端服務或 Microsoft Graph 的存取權。

在此案例中，當使用者登入之後，系統會透過授權標頭要求一個存取權杖，並將其新增到 HTTP 要求。 MSAL 會處理權杖取得和續約事項。

### <a name="libraries"></a>程式庫

本教學課程會使用下列程式庫：

|程式庫|描述|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|適用於 Angular 包裝函式的 Microsoft Authentication Library|

您可以在 GitHub 上的 [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) 存放庫中找到適用於 MSAL.js 程式庫的原始程式碼。

## <a name="create-your-project"></a>建立專案

使用下列 npm 命令產生新的 Angular 應用程式：

```bash
npm install -g @angular/cli@8                    # Install the Angular CLI
ng new my-application --routing=true --style=css # Generate a new Angular app
cd my-application                                # Change to the app directory
npm install @angular/material@8 @angular/cdk@8   # Install the Angular Material component library (optional, for UI)
npm install msal @azure/msal-angular             # Install MSAL and MSAL Angular in your application
ng generate component page-name                  # To add a new page (such as a home or profile page)
```

## <a name="register-your-application"></a>註冊您的應用程式

依照指示，在 Azure 入口網站中[註冊單頁應用程式](./scenario-spa-app-registration.md)。

在您註冊的應用程式 [概觀] 頁面上，記下 [應用程式 (用戶端) 識別碼] 值以供稍後使用。

將您的 **重新導向 URI** 值註冊為 **http://localhost:4200/** 並啟用隱含授與設定。

## <a name="configure-the-application"></a>設定應用程式

1. 在 *src/app* 資料夾中，編輯 *app.module.ts* 並將 `MSALModule` 新增至 `imports` 以及 `isIE` 常數：

    ```javascript
    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;
    @NgModule({
      declarations: [
        AppComponent
      ],
      imports: [
        BrowserModule,
        AppRoutingModule,
        MsalModule.forRoot({
          auth: {
            clientId: 'Enter_the_Application_Id_here', // This is your client ID
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', // This is your tenant ID
            redirectUri: 'Enter_the_Redirect_Uri_Here'// This is your redirect URI
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
          },
        }, {
          popUp: !isIE,
          consentScopes: [
            'user.read',
            'openid',
            'profile',
          ],
          unprotectedResources: [],
          protectedResourceMap: [
            ['https://graph.microsoft.com/v1.0/me', ['user.read']]
          ],
          extraQueryParameters: {}
        })
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    ```

    取代下列值：

    |值名稱|關於|
    |---------|---------|
    |Enter_the_Application_Id_Here|在應用程式註冊的 [概觀] 頁面中，這是您的 **應用程式 (用戶端) 識別碼** 值。 |
    |Enter_the_Cloud_Instance_Id_Here|這是 Azure 雲端的執行個體。 針對主要或全域 Azure 雲端，請輸入 **https://login.microsoftonline.com** 。 針對國家端 (例如中國)，請參閱[國家雲端](./authentication-national-cloud.md)。|
    |Enter_the_Tenant_Info_Here| 設為下列其中一個選項：如果您的應用程式支援「此組織目錄中的帳戶」，請將此值取代為目錄 (租用戶) 識別碼或租用戶名稱 (例如 **contoso.microsoft.com**)。 如果您的應用程式支援「任何組織目錄中的帳戶」，請將此值取代為 [組織]。 如果您的應用程式支援「任何組織目錄中的帳戶及個人的 Microsoft 帳戶」，請將此值取代為 [通用]。 若要將支援範圍限制為「僅限個人 Microsoft 帳戶」，請將此值取代為 [取用者]。 |
    |Enter_the_Redirect_Uri_Here|以 **http://localhost:4200** 取代。|

    如需可用之可設定選項的詳細資訊，請參閱[將用戶端應用程式初始化](msal-js-initializing-client-applications.md) \(英文\)。

2. 在相同檔案的頂端，新增下列 import 陳述式：

    ```javascript
    import { MsalModule, MsalInterceptor } from '@azure/msal-angular';
    ```

3. 將下列 import 陳述式新增至 `src/app/app.component.ts` 的頂端：

    ```javascript
    import { MsalService, BroadcastService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';
    ```
## <a name="sign-in-a-user"></a>登入使用者

將下列程式碼新增至 `AppComponent` 以將使用者登入：

```javascript
export class AppComponent implements OnInit {
    constructor(private broadcastService: BroadcastService, private authService: MsalService) { }

    ngOnInit() { }

    login() {
        const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

        if (isIE) {
          this.authService.loginRedirect({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        } else {
          this.authService.loginPopup({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        }
    }
}
```

> [!TIP]
> 我們建議針對 Internet Explorer 使用者使用 `loginRedirect`。

## <a name="acquire-a-token"></a>取得權杖

### <a name="angular-interceptor"></a>Angular Interceptor

MSAL Angular 會提供 `Interceptor` 類別給已知受保護的資源，而該類別可為使用 Angular `http` 用戶端的傳出要求自動取得權杖。

首先，將 `Interceptor` 類別納入為您應用程式的提供者：

```javascript
import { MsalInterceptor, MsalModule } from "@azure/msal-angular";
import { HTTP_INTERCEPTORS, HttpClientModule } from "@angular/common/http";

@NgModule({
    // ...
    providers: [
        {
            provide: HTTP_INTERCEPTORS,
            useClass: MsalInterceptor,
            multi: true
        }
    ]
}
```

接下來，以 `protectedResourceMap`形式提供受保護資源與 `MsalModule.forRoot()` 的對應，並將這些範圍包含在 `consentScopes` 中。 您在 `protectedResourceMap` 集合中提供的 URL 會區分大小寫。

```javascript
@NgModule({
  // ...
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_here', // This is your client ID
        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
      },
      cache: {
        cacheLocation: 'localStorage',
        storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
      },
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      unprotectedResources: [],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ],
      extraQueryParameters: {}
    })
  ],
});
```

最後，利用 HTTP 要求來擷取使用者的設定檔：

```JavaScript
const graphMeEndpoint = "https://graph.microsoft.com/v1.0/me";

getProfile() {
  this.http.get(graphMeEndpoint).toPromise()
    .then(profile => {
      this.profile = profile;
    });
}
```

### <a name="acquiretokensilent-acquiretokenpopup-acquiretokenredirect"></a>acquireTokenSilent、acquireTokenPopup、acquireTokenRedirect
MSAL 使用三個方法來取得權杖：`acquireTokenRedirect`、`acquireTokenPopup` 和 `acquireTokenSilent`。 不過，建議使用 `MsalInterceptor` 類別代替 Angular 應用程式，如上一節所示。

#### <a name="get-a-user-token-silently"></a>以無訊息方式取得使用者權杖

`acquireTokenSilent` 方法會處理權杖取得和更新作業，不需要與使用者進行互動。 在第一次執行 `loginRedirect` 或 `loginPopup` 方法之後，`acquireTokenSilent` 就會經常用來取得權杖，以在稍後呼叫中使用權杖存取受保護的資源。 以無訊息方式進行要求或更新權杖的呼叫。

```javascript
const requestObj = {
    scopes: ["user.read"]
};

this.authService.acquireTokenSilent(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

在該程式碼中，`scopes` 包含要在 API 存取權杖中傳回的所要求範圍。

例如：

* 適用於 Microsoft Graph 的 `["user.read"]`
* 適用於自訂 Web API 的 `["<Application ID URL>/scope"]` (也就是，`api://<Application ID>/access_as_user`)

#### <a name="get-a-user-token-interactively"></a>以互動方式取得使用者權杖

有時您需要使用者與 Microsoft 身分識別平臺互動。 例如：

* 使用者可能需要重新輸入其認證，因為密碼已過期。
* 您的應用程式要求其他資源範圍的存取權，需要使用者同意。
* 需要雙因素驗證。

大部分應用程式的建議模式都是先呼叫 `acquireTokenSilent`，再捕捉例外狀況，然後呼叫 `acquireTokenPopup` (或 `acquireTokenRedirect`) 以啟動互動式要求。

呼叫 `acquireTokenPopup` 會導致出現快顯登入視窗。 或者， `acquireTokenRedirect` 將使用者重新導向至 Microsoft 身分識別平臺。 在該視窗中，使用者必須確認其認證、同意必要的資源，或完成雙因素驗證。

```javascript
  const requestObj = {
      scopes: ["user.read"]
  };

  this.authService.acquireTokenPopup(requestObj).then(function (tokenResponse) {
      // Callback code here
      console.log(tokenResponse.accessToken);
  }).catch(function (error) {
      console.log(error);
  });
```

> [!NOTE]
> 此快速入門搭配 Microsoft Internet Explorer 使用 `loginRedirect` 和 `acquireTokenRedirect` 方法，因為 Internet Explorer 有處理快顯視窗的相關[已知問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)。

## <a name="log-out"></a>登出

新增下列程式碼以將使用者登出：

```javascript
logout() {
  this.authService.logout();
}
```

## <a name="add-ui"></a>新增 UI
如需如何使用 Angular Material 元件庫新增 UI 的範例，請參閱[應用程式範例](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular)。

## <a name="test-your-code"></a>測試您的程式碼

1.  從應用程式資料夾在命令提示字元中執行命令，以啟動網頁伺服器來接聽此連接埠：

    ```bash
    npm install
    npm start
    ```
1. 在瀏覽器中輸入 **http://localhost:4200** 或 **http://localhost:{port}** ，其中 *port* 是網頁伺服器接聽所在的連接埠。


### <a name="provide-consent-for-application-access"></a>同意應用程式存取

第一次開始登入應用程式時，系統會提示您授與您設定檔的存取權，並且允許其將您登入：

![[要求的權限] 視窗](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

## <a name="add-scopes-and-delegated-permissions"></a>新增範圍與委派的權限

Microsoft Graph API 需要 user.read 範圍才能讀取使用者的設定檔。 根據預設，在註冊入口網站上註冊的每個應用程式中，都會自動新增此範圍。 Microsoft Graph 的其他 API 與您後端伺服器的自訂 API 一樣，需要其他範圍。 例如，Microsoft Graph API 需要 Calendars.Read 範圍才能列出使用者的行事曆。

為了在應用程式內容中存取使用者的行事曆，請將 Calendars.Read 委派權限新增至應用程式註冊資訊。 接著，將 Calendars.Read 範圍新增至 `acquireTokenSilent` 呼叫。

>[!NOTE]
>系統可能會在您增加範圍數目時，提示使用者同意其他事項。

如果後端 API 不需要範圍 (不建議)，您可以在呼叫中使用 clientId 作為範圍以取得權杖。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>後續步驟

在我們的多個部分文章系列中，深入了解如何在 Microsoft 身分識別平台上開發單頁應用程式 (SPA)。

> [!div class="nextstepaction"]
> [案例：單頁應用程式](scenario-spa-overview.md)
