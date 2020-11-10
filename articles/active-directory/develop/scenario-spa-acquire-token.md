---
title: 取得權杖以呼叫 web API (單一頁面應用程式) -Microsoft 身分識別平臺 |蔚藍
description: '瞭解如何建立單一頁面應用程式 (取得權杖以呼叫 API) '
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
ms.openlocfilehash: 3d91e69d7d9eac9f8cae1a1a122a13afd19ef631
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443869"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>單一頁面應用程式：取得權杖以呼叫 API

使用 MSAL.js 為 Api 取得權杖的模式，是先使用方法嘗試無訊息的權杖要求 `acquireTokenSilent` 。 呼叫這個方法時，程式庫會先檢查瀏覽器儲存體中的快取，查看是否存在有效的權杖並將其傳回。 當快取中沒有有效的權杖時，它會從隱藏的 iframe 傳送無訊息權杖要求給 Azure Active Directory (Azure AD) 。 此方法也可讓程式庫更新權杖。 如需 Azure AD 中單一登入會話和權杖存留期值的詳細資訊，請參閱 [權杖存留期](active-directory-configurable-token-lifetimes.md)。

Azure AD 的無訊息權杖要求可能會因為 Azure AD 會話過期或密碼變更等原因而失敗。 在這種情況下，您可以叫用其中一個互動式方法 (這會提示使用者) 取得權杖：

* [快顯視窗](#acquire-a-token-with-a-pop-up-window)，方法是使用 `acquireTokenPopup`
* 重新[導向](#acquire-a-token-with-a-redirect)，使用`acquireTokenRedirect`

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>在快顯或重新導向體驗之間進行選擇

 您無法在應用程式中同時使用快顯和重新導向方法。 彈出或重新導向體驗之間的選擇取決於您的應用程式流程：

* 如果您不想讓使用者在驗證期間離開主要的應用程式頁面，我們建議使用快顯方法。 由於驗證重新導向是在快顯視窗中執行，因此會保留主應用程式的狀態。

* 如果使用者具有瀏覽器條件約束或停用快顯視窗的原則，您可以使用重新導向方法。 使用重新導向方法搭配 Internet Explorer 瀏覽器，因為 [Internet Explorer 上的快顯視窗有已知問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)。

您可以設定您想要讓存取權杖在建立存取權杖要求時包含的 API 範圍。 請注意，存取權杖中可能不會授與所有要求的範圍。 這取決於使用者的同意。

## <a name="acquire-a-token-with-a-pop-up-window"></a>使用快顯視窗取得權杖

# <a name="javascript"></a>[JavaScript](#tab/javascript)

下列程式碼結合了先前描述的模式與快顯體驗的方法：

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

MSAL 角度包裝函式提供 HTTP 攔截器，它會自動以無訊息方式取得存取權杖，並將其附加至 Api 的 HTTP 要求。

您可以在 configuration 選項中指定 Api 的範圍 `protectedResourceMap` 。 `MsalInterceptor` 會在自動取得權杖時要求這些範圍。

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

或者，您可以使用如 core MSAL.js 程式庫中所述的取得權杖方法，明確地取得權杖。

---

## <a name="acquire-a-token-with-a-redirect"></a>使用重新導向取得權杖

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下是稍早所述的模式，但以互動方式取得權杖的重新導向方法會顯示。 如先前所述，您將需要註冊重新導向回呼。

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

您可以使用選擇性宣告進行下列用途：

- 將其他宣告包含在應用程式的權杖中。
- 變更 Azure AD 在權杖中傳回之特定宣告的行為。
- 新增和存取應用程式的自訂宣告。

若要在中要求選擇性的宣告 `IdToken` ，您可以傳送 stringified 宣告物件至 `claimsRequest` 類別的欄位 `AuthenticationParameters.ts` 。

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

若要深入瞭解，請參閱 [選用宣告](active-directory-optional-claims.md)。

# <a name="angular"></a>[Angular](#tab/angular)

此程式碼與先前所述的相同。

---

## <a name="next-steps"></a>後續步驟

請移至本案例的下一篇文章，以 [呼叫 WEB API](scenario-spa-call-api.md)。