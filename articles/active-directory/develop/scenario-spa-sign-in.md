---
title: 單頁應用登錄&登出 - 微軟身份平臺 |蔚藍
description: 瞭解如何構建單頁應用程式（登錄）
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: eb75aa53051e7e3c424ffe131cda61324fe86b1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159959"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>單頁應用程式：登錄和登出

瞭解如何將登錄添加到單頁應用程式的代碼。

在獲取權杖訪問應用程式中的 API 之前，需要經過身份驗證的使用者上下文。 您可以通過兩種方式在 MSAL.js 中登錄應用程式：

* 快顯視窗[，使用](#sign-in-with-a-pop-up-window)`loginPopup`方法
* 使用 方法[重定向](#sign-in-with-redirect)， `loginRedirect`

您還可以選擇傳遞在登錄時需要使用者同意的 API 的範圍。

> [!NOTE]
> 如果應用程式已有權訪問經過身份驗證的使用者上下文或 ID 權杖，則可以跳過登錄步驟並直接獲取權杖。 有關詳細資訊，請參閱[沒有 MSAL.js 登錄的 SSO。](msal-js-sso.md#sso-without-msaljs-login)

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>在快顯視窗或重定向體驗之間進行選擇

不能同時使用應用程式中的快顯視窗和重定向方法。 快顯視窗或重定向體驗之間的選擇取決於您的應用程式流：

* 如果不希望使用者在身份驗證期間離開主應用程式頁，我們建議使用彈出式方法。 由於身份驗證重定向發生在快顯視窗中，因此將保留主應用程式的狀態。

* 如果使用者具有禁用快顯視窗的瀏覽器約束或策略，則可以使用重定向方法。 使用重定向方法與 Internet 資源管理器瀏覽器，因為有[已知的問題與快顯視窗在 Internet 資源管理器](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)。

## <a name="sign-in-with-a-pop-up-window"></a>使用快顯視窗登錄

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

```javascript
const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginPopup(loginRequest).then(function (loginResponse) {
    //login success
    let idToken = loginResponse.idToken;
}).catch(function (error) {
    //login failure
    console.log(error);
});
```

# <a name="angular"></a>[Angular](#tab/angular)

MSAL 角包裝器允許您通過添加到`MsalGuard`路由定義來保護應用程式中的特定路由。 此保護將在訪問該路由時調用方法登錄。

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

有關快顯視窗體驗，啟用`popUp`配置選項。 您還可以傳遞需要同意的範圍，如下所示：

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["https://graph.microsoft.com/User.ReadWrite"]
            })]
         })
```
---

## <a name="sign-in-with-redirect"></a>使用重定向登錄

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

重定向方法不會返回承諾，因為從主應用移開。 要處理和訪問返回的權杖，您需要在調用重定向方法之前註冊成功和錯誤回檔。

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginRedirect(loginRequest);
```

# <a name="angular"></a>[Angular](#tab/angular)

此處的代碼與前面有關使用快顯視窗登錄的部分中描述的相同。 預設流是重定向的。

> [!NOTE]
> ID 權杖不包含同意的範圍，僅表示經過身份驗證的使用者。 同意的作用域在訪問權杖中返回，您將在下一步中獲取該權杖。

---

## <a name="sign-out"></a>登出

MSAL 庫提供了一`logout`種清除瀏覽器存儲中的緩存並將登出要求傳送到 Azure 活動目錄 （Azure AD） 的方法。 登出後，預設情況下，庫重定向回應用程式起始頁。

您可以通過設置`postLogoutRedirectUri`配置在登出後重定向到的 URI。 此 URI 也應在應用程式註冊中註冊為登出 URI。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

```javascript
const config = {

    auth: {
        clientID: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const userAgentApplication = new UserAgentApplication(config);
userAgentApplication.logout();

```

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            })]
         })

// In app.component.ts
this.authService.logout();
```

---

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [取得應用程式的權杖](scenario-spa-acquire-token.md)
