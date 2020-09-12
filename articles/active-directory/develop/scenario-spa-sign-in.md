---
title: 單一頁面應用程式登入 & 登出-Microsoft 身分識別平臺 |蔚藍
description: '瞭解如何建立單一頁面應用程式 (登入) '
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
ms.openlocfilehash: c8ec151c813bfb0b9777e583a4ea5144e3b2079a
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297054"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>單一頁面應用程式：登入和登出

瞭解如何將登入新增至單一頁面應用程式的程式碼。

在您可以取得權杖以存取應用程式中的 Api 之前，您需要經過驗證的使用者內容。 您可以透過下列兩種方式，在 MSAL.js 中將使用者登入您的應用程式：

* [快顯視窗](#sign-in-with-a-pop-up-window)，方法是使用 `loginPopup` 方法
* 使用方法重新[導向](#sign-in-with-redirect) `loginRedirect`

您也可以選擇性地傳遞您需要使用者在登入時同意的 Api 範圍。

> [!NOTE]
> 如果您的應用程式已經可以存取已驗證的使用者內容或識別碼權杖，您可以略過登入步驟並直接取得權杖。 如需詳細資訊，請參閱 [沒有 MSAL.js 登](msal-js-sso.md#sso-without-msaljs-login)入的 SSO。

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>在快顯或重新導向體驗之間進行選擇

您無法在應用程式中同時使用快顯和重新導向方法。 彈出或重新導向體驗之間的選擇取決於您的應用程式流程：

* 如果您不想讓使用者在驗證期間離開主要的應用程式頁面，建議您採用快顯方法。 由於驗證重新導向是在快顯視窗中執行，因此會保留主應用程式的狀態。

* 如果使用者有瀏覽器條件約束或已停用快顯視窗的原則，您可以使用重新導向方法。 使用重新導向方法搭配 Internet Explorer 瀏覽器，因為 [Internet Explorer 上的快顯視窗有已知問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)。

## <a name="sign-in-with-a-pop-up-window"></a>使用快顯視窗登入


# <a name="javascript-msaljs-2x"></a>[JavaScript ( # A0 2.x) ](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let username = "";

const myMsal = new PublicClientApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        //login success

        // In case multiple accounts exist, you can select
        const currentAccounts = myMsal.getAllAccounts();
    
        if (currentAccounts === null) {
            // no accounts detected
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
        } else if (currentAccounts.length === 1) {
            username = currentAccounts[0].username;
        }
    
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="javascript-msaljs-1x"></a>[JavaScript ( # A0 1.x) ](#tab/javascript1)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        //login success
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="angular"></a>[Angular](#tab/angular)

MSAL 角度包裝函式可讓您藉由新增至路由定義來保護應用程式中的特定路由 `MsalGuard` 。 此防護將會叫用方法，以在存取該路由時登入。

```javascript
// In app-routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ProfileComponent } from './profile/profile.component';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';

const routes: Routes = [
  {
    path: 'profile',
    component: ProfileComponent,
    canActivate: [
      MsalGuard
    ]
  },
  {
    path: '',
    component: HomeComponent
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes, { useHash: false })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

若要取得快顯視窗體驗，請啟用 [設定] `popUp` 選項。 您也可以傳遞需要同意的範圍，如下所示：

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
            }
        }, {
            popUp: true,
            consentScopes: ["User.ReadWrite"]
        })
    ]
})
```
---

## <a name="sign-in-with-redirect"></a>使用重新導向登入

# <a name="javascript-msaljs-2x"></a>[JavaScript ( # A0 2.x) ](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let username = "";

const myMsal = new PublicClientApplication(config);

function handleResponse(response) {
    //handle redirect response

    // In case multiple accounts exist, you can select
    const currentAccounts = myMsal.getAllAccounts();

    if (currentAccounts === null) {
        // no accounts detected
    } else if (currentAccounts.length > 1) {
        // Add choose account code here
    } else if (currentAccounts.length === 1) {
        username = currentAccounts[0].username;
    }
}

myMsal.handleRedirectPromise(handleResponse);

myMsal.loginRedirect(loginRequest);
```

# <a name="javascript-msaljs-1x"></a>[JavaScript ( # A0 1.x) ](#tab/javascript1)

重新導向方法不會傳回承諾，因為從主應用程式移開。 若要處理並存取傳回的權杖，您必須先註冊成功和錯誤回呼，然後再呼叫重新導向方法。

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

myMsal.handleRedirectCallback(authCallback);

myMsal.loginRedirect(loginRequest);
```

# <a name="angular"></a>[Angular](#tab/angular)

此處的程式碼與使用快顯視窗登入一節中所述的步驟相同。 預設流程為重新導向。

---

## <a name="sign-out"></a>登出

MSAL 程式庫提供的 `logout` 方法會清除瀏覽器儲存體中的快取，並將登出要求傳送給 Azure Active Directory (Azure AD) 。 登出之後，程式庫預設會重新導向回到應用程式起始頁。

您可以設定在登出之後要將其重新導向至的 URI `postLogoutRedirectUri` 。 此 URI 也應該在您的應用程式註冊中註冊為登出 URI。

# <a name="javascript-msaljs-2x"></a>[JavaScript ( # A0 2.x) ](#tab/javascript2)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new PublicClientApplication(config);

// you can select which account application should sign out
const logoutRequest = {
    account: myMsal.getAccountByUsername(username)
}

myMsal.logout(logoutRequest);
```

# <a name="javascript-msaljs-1x"></a>[JavaScript ( # A0 1.x) ](#tab/javascript1)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new UserAgentApplication(config);

myMsal.logout();
```

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
//In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            }
        })
    ]
})

// In app.component.ts
this.authService.logout();
```

---

## <a name="next-steps"></a>接下來的步驟

> [!div class="nextstepaction"]
> [取得應用程式的權杖](scenario-spa-acquire-token.md)
