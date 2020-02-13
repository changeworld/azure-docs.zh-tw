---
title: 單一頁面應用程式登入 & 登出-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立單一頁面應用程式（登入）
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
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159959"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>單一頁面應用程式：登入和登出

瞭解如何將登入新增至單一頁面應用程式的程式碼。

在您可以取得權杖以存取應用程式中的 Api 之前，您需要已驗證的使用者內容。 您可以透過兩種方式，在 MSAL 中將使用者登入您的應用程式：

* [快顯視窗](#sign-in-with-a-pop-up-window)，使用 `loginPopup` 方法
* 使用 `loginRedirect` 方法來重新[導向](#sign-in-with-redirect)

您也可以選擇性地傳遞您在登入時需要使用者同意的 Api 範圍。

> [!NOTE]
> 如果您的應用程式已可存取已驗證的使用者內容或識別碼權杖，您可以略過登入步驟並直接取得權杖。 如需詳細資訊，請參閱[不含 MSAL 的 SSO 登](msal-js-sso.md#sso-without-msaljs-login)入。

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>在快顯或重新導向體驗之間進行選擇

您不能在應用程式中同時使用快顯和重新導向方法。 快顯或重新導向體驗之間的選擇取決於您的應用程式流程：

* 如果您不想讓使用者在驗證期間從主要應用程式頁面移開，建議快顯方法。 因為驗證重新導向會在快顯視窗中進行，所以會保留主應用程式的狀態。

* 如果使用者具有瀏覽器條件約束或停用快顯視窗的原則，您可以使用重新導向方法。 使用重新導向方法與 Internet Explorer 瀏覽器，因為[Internet explorer 上的快顯視窗有已知問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)。

## <a name="sign-in-with-a-pop-up-window"></a>使用快顯視窗登入

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

# <a name="angulartabangular"></a>[Angular](#tab/angular)

MSAL 角度包裝函式可讓您藉由將 `MsalGuard` 新增至路由定義來保護應用程式中的特定路由。 此防護會叫用方法，以在存取該路由時登入。

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

如需快顯視窗體驗，請啟用 [`popUp` 設定] 選項。 您也可以傳遞需要同意的範圍，如下所示：

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

## <a name="sign-in-with-redirect"></a>使用重新導向登入

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

重新導向方法不會傳回承諾，因為會離開主要應用程式。 若要處理和存取傳回的權杖，您必須先註冊成功和錯誤回呼，再呼叫重新導向方法。

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

# <a name="angulartabangular"></a>[Angular](#tab/angular)

此處的程式碼與先前有關使用快顯視窗登入一節中所述的相同。 預設流程為 [重新導向]。

> [!NOTE]
> 識別碼權杖不包含同意的範圍，而且只代表已驗證的使用者。 同意的範圍會在存取權杖中傳回，您將在下一個步驟中取得。

---

## <a name="sign-out"></a>登出

MSAL 程式庫提供 `logout` 方法，可清除瀏覽器儲存體中的快取，並將登出要求傳送至 Azure Active Directory （Azure AD）。 登出之後，程式庫預設會重新導向回到應用程式起始頁。

您可以設定 `postLogoutRedirectUri`，以在登出後將其重新導向的 URI 設定為。 此 URI 也應該在您的應用程式註冊中註冊為登出 URI。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

# <a name="angulartabangular"></a>[Angular](#tab/angular)

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
