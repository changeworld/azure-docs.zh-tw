---
title: 取得權杖以呼叫 Web API （單一頁面應用程式）-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立單一頁面應用程式（取得權杖以呼叫 API）
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.openlocfilehash: eeba01a609a1a21ed564c0b9cb78a28a4ad5c95a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80882313"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>單一頁面應用程式：取得權杖以呼叫 API

使用 MSAL.js 來取得 Api 之權杖的模式，是先使用方法來嘗試無訊息權杖要求 `acquireTokenSilent` 。 呼叫這個方法時，程式庫會先檢查瀏覽器儲存體中的快取，以查看有效的權杖是否存在，並將其傳回。 當快取中沒有有效的權杖時，它會將無訊息 token 要求傳送至隱藏的 iframe Azure Active Directory （Azure AD）。 此方法也可讓程式庫更新權杖。 如需 Azure AD 中的單一登入會話和權杖存留期值的詳細資訊，請參閱[權杖存留期](active-directory-configurable-token-lifetimes.md)。

Azure AD 的無訊息 token 要求可能會因為 Azure AD 會話或密碼變更過期之類的原因而失敗。 在這種情況下，您可以叫用其中一個互動式方法（這會提示使用者）取得權杖：

* [快顯視窗](#acquire-a-token-with-a-pop-up-window)，方法是使用`acquireTokenPopup`
* 重新[導向](#acquire-a-token-with-a-redirect)，使用`acquireTokenRedirect`

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>在快顯或重新導向體驗之間做選擇

 您不能在應用程式中同時使用快顯和重新導向方法。 快顯或重新導向體驗之間的選擇取決於您的應用程式流程：

* 如果您不想讓使用者在驗證期間從主要應用程式頁面移開，建議使用快顯方法。 因為驗證重新導向會在快顯視窗中進行，所以會保留主應用程式的狀態。

* 如果使用者具有瀏覽器條件約束或停用快顯視窗的原則，您可以使用重新導向方法。 使用重新導向方法與 Internet Explorer 瀏覽器，因為[Internet explorer 上的快顯視窗有已知問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)。

您可以設定在建立存取權杖要求時，要讓存取權杖包含的 API 範圍。 請注意，存取權杖中可能不會授與所有要求的範圍。 這取決於使用者的同意。

## <a name="acquire-a-token-with-a-pop-up-window"></a>取得具有快顯視窗的權杖

# <a name="javascript"></a>[JavaScript](#tab/javascript)

下列程式碼結合了先前所述的模式與快顯體驗的方法：

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

# <a name="angular"></a>[Angular](#tab/angular)

MSAL 角度包裝函式會提供 HTTP 攔截器，這會自動以無訊息方式取得存取權杖，並將其附加至 Api 的 HTTP 要求。

您可以在 [設定] 選項中指定 Api 的範圍 `protectedResourceMap` 。 `MsalInterceptor`會在自動取得權杖時要求這些範圍。

```javascript
// app.module.ts
@NgModule({
  declarations: [
    // ...
  ],
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_Here',
      }
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ]
    })
  ],
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MsalInterceptor,
      multi: true
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

針對無訊息權杖取得成功和失敗，MSAL 角度會提供您可以訂閱的回呼。 也請務必記得取消訂閱。

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if (this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

或者，您可以使用核心 MSAL.js 程式庫中所述的取得權杖方法來明確取得權杖。

---

## <a name="acquire-a-token-with-a-redirect"></a>使用重新導向取得權杖

# <a name="javascript"></a>[JavaScript](#tab/javascript)

下列模式如先前所述，但使用重新導向方法來以互動方式取得權杖。 您必須註冊先前所述的重新導向回呼。

```javascript
function authCallback(error, response) {
    // Handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-optional-claims"></a>要求選擇性宣告

您可以針對下列目的使用選擇性宣告：

- 在應用程式的權杖中包含額外的宣告。
- 變更 Azure AD 在權杖中傳回之特定宣告的行為。
- 新增和存取應用程式的自訂宣告。

若要在中要求選擇性宣告 `IdToken` ，您可以將 stringified 宣告物件傳送至 `claimsRequest` 類別的欄位 `AuthenticationParameters.ts` 。

```javascript
"optionalClaims":
   {
      "idToken": [
            {
                  "name": "auth_time",
                  "essential": true
             }
      ],

var request = {
    scopes: ["user.read"],
    claimsRequest: JSON.stringify(claims)
};

myMSALObj.acquireTokenPopup(request);
```

若要深入瞭解，請參閱[選擇性宣告](active-directory-optional-claims.md)。

# <a name="angular"></a>[Angular](#tab/angular)

這段程式碼與先前所述的相同。

---

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 Web API](scenario-spa-call-api.md)
