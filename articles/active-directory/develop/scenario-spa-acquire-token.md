---
title: 獲取權杖來調用 Web API（單頁應用） - 微軟身份平臺 |蔚藍
description: 瞭解如何構建單頁應用程式（獲取權杖以調用 API）
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
ms.openlocfilehash: d5d48a2fc7aca184cf8b6e7761584a8800ca5151
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160061"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>單頁應用程式：獲取權杖以調用 API

使用 MSAL.js 獲取 API 權杖的模式是首先使用 方法`acquireTokenSilent`嘗試靜默權杖請求。 調用此方法時，庫首先檢查瀏覽器存儲中的緩存以查看是否存在有效的權杖並返回它。 當緩存中沒有有效的權杖時，它將從隱藏的 iframe 向 Azure 活動目錄 （Azure AD） 發送靜默權杖請求。 此方法還允許庫續訂權杖。 有關 Azure AD 中的單一登入會話和權杖存留期值的詳細資訊，請參閱[權杖存留期](active-directory-configurable-token-lifetimes.md)。

由於過期的 Azure AD 會話或密碼更改等原因，對 Azure AD 的靜默權杖請求可能會失敗。 在這種情況下，您可以調用其中一種互動式方法（這將提示使用者）以獲取權杖：

* [快顯視窗](#acquire-a-token-with-a-pop-up-window)， 通過使用`acquireTokenPopup`
* [使用 重定向](#acquire-a-token-with-a-redirect)，使用`acquireTokenRedirect`

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>在快顯視窗或重定向體驗之間進行選擇

 不能同時使用應用程式中的快顯視窗和重定向方法。 快顯視窗或重定向體驗之間的選擇取決於您的應用程式流：

* 如果您不希望使用者在身份驗證期間離開主應用程式頁，我們建議使用彈出式方法。 由於身份驗證重定向發生在快顯視窗中，因此將保留主應用程式的狀態。

* 如果使用者具有禁用快顯視窗的瀏覽器約束或策略，則可以使用重定向方法。 使用重定向方法與 Internet 資源管理器瀏覽器，因為有[已知的問題與快顯視窗在 Internet 資源管理器](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)。

您可以設置在構建訪問權杖請求時希望訪問權杖包含的 API 作用域。 請注意，並非所有請求的範圍都未在訪問權杖中授予。 這取決於使用者的同意。

## <a name="acquire-a-token-with-a-pop-up-window"></a>使用快顯視窗獲取權杖

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

以下代碼將前面描述的模式與彈出式體驗的方法相結合：

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

MSAL 角包裝器提供 HTTP 攔截器，該攔截器將自動以靜默方式獲取訪問權杖，並將它們附加到 API 的 HTTP 要求。

您可以在`protectedResourceMap`配置選項中指定 API 的範圍。 `MsalInterceptor`將在自動獲取權杖時請求這些作用域。

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                protectedResourceMap: {"https://graph.microsoft.com/v1.0/me", ["user.read", "mail.send"]}
            })]
         })

providers: [ ProductService, {
        provide: HTTP_INTERCEPTORS,
        useClass: MsalInterceptor,
        multi: true
    }
   ],
```

對於靜默權杖獲取的成功和失敗，MSAL Angular 提供可以訂閱的回檔。 記住取消訂閱也很重要。

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if(this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

或者，您可以使用核心 MSAL.js 庫中所述的獲取權杖方法顯式獲取權杖。

---

## <a name="acquire-a-token-with-a-redirect"></a>使用重定向獲取權杖

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

如下圖所示，但使用重定向方法以對話模式獲取權杖。 您需要註冊重定向回檔，如前面所述。

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

## <a name="request-optional-claims"></a>請求可選聲明

您可以將可選聲明用於以下目的：

- 在應用程式中的權杖中包括其他聲明。
- 變更 Azure AD 在權杖中傳回之特定宣告的行為。
- 新增和存取應用程式的自訂宣告。 

要在 中`IdToken`請求可選聲明，可以將字串化聲明物件發送到`claimsRequest``AuthenticationParameters.ts`類的欄位。

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

要瞭解更多資訊，請參閱[可選聲明](active-directory-optional-claims.md)。

# <a name="angular"></a>[Angular](#tab/angular)

此代碼與前面描述的代碼相同。

---

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 Web API](scenario-spa-call-api.md)
