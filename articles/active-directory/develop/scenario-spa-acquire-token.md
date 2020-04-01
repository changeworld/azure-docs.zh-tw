---
title: 取得權杖來呼叫 Web API(單頁應用) - 微軟身份平臺 |蔚藍
description: 瞭解如何建構單頁應用程式(取得權杖以呼叫 API)
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.openlocfilehash: 393c3a06a2366a7d6947faf8bbfe038d6c5982fc
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419652"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>單頁應用程式:取得權杖以呼叫 API

使用 MSAL.js 獲取 API 權杖的模式`acquireTokenSilent`是首先使用方法 嘗試靜默權杖請求。 調用此方法時,庫首先檢查瀏覽器存儲中的緩存以查看是否存在有效的令牌並返回它。 當快取中沒有有效的權杖時,它將從隱藏的 iframe 向 Azure 活動目錄 (Azure AD) 發送靜默權杖請求。 此方法還允許庫續訂令牌。 有關 Azure AD 的單一登入工作階段和權杖存留期值的詳細資訊,請參考[權杖存留期](active-directory-configurable-token-lifetimes.md)。

由於過期的 Azure AD 工作階段或密碼更改等原因,對 Azure AD 的靜默權杖請求可能會失敗。 在這種情況下,您可以調用其中一種互動式方法(這將提示使用者)以取得權杖:

* [彈出視窗](#acquire-a-token-with-a-pop-up-window), 使用`acquireTokenPopup`
* [使用 重定向](#acquire-a-token-with-a-redirect),使用`acquireTokenRedirect`

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>在彈出視窗或重定向體驗之間進行選擇

 不能同時使用應用程式中的彈出視窗和重定向方法。 彈出視窗或重定向體驗之間的選擇取決於您的應用程式串流:

* 如果您不希望使用者在身份驗證期間離開主應用程式頁,我們建議使用彈出式方法。 由於身份驗證重定向發生在彈出視窗中,因此將保留主應用程式的狀態。

* 如果使用者具有禁用彈出視窗的瀏覽器約束或策略,則可以使用重定向方法。 使用重定向方法與 Internet 資源管理員瀏覽器,因為有[已知的問題與彈出視窗在 Internet 資源管理員](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)。

您可以設置在建立權限存取權杖請求時希望存取權杖包含的 API 作用域。 請注意,並非所有請求的範圍都未在訪問權杖中授予。 這取決於使用者的同意。

## <a name="acquire-a-token-with-a-pop-up-window"></a>使用彈出視窗抓取權杖

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下代碼將前面描述的模式與彈出式體驗的方法相結合:

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

MSAL 角包裝器提供 HTTP 攔截器,該攔截器將自動以靜默方式獲取訪問權杖,並將它們附加到 API 的 HTTP 請求。

您可以在`protectedResourceMap`設定選項中指定 API 的範圍。 `MsalInterceptor`將在自動獲取權杖時請求這些作用域。

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

對於靜默權杖獲取的成功和失敗,MSAL Angular 提供可以訂閱的回調。 記住取消訂閱也很重要。

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

或者,您可以使用核心 MSAL.js 庫中所述的獲取令牌方法顯式獲取權杖。

---

## <a name="acquire-a-token-with-a-redirect"></a>使用重定到取得權杖

# <a name="javascript"></a>[JavaScript](#tab/javascript)

如下圖所示,但使用重定向方法以交互方式獲取令牌。 您需要註冊重定向回調,如前面所述。

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

## <a name="request-optional-claims"></a>要求選擇宣告

您可以選擇選擇的聲明的以下目的:

- 在應用程式中的權杖中包括其他聲明。
- 變更 Azure AD 在權杖中傳回之特定宣告的行為。
- 新增和存取應用程式的自訂宣告。

要在`IdToken`中 請求可選聲明,可以將字串化聲明物件發送`claimsRequest``AuthenticationParameters.ts`到 類的欄位。

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

要瞭解更多資訊,請參閱[可選聲明](active-directory-optional-claims.md)。

# <a name="angular"></a>[Angular](#tab/angular)

此代碼與前面描述的代碼相同。

---

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 Web API](scenario-spa-call-api.md)
