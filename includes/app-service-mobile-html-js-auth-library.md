---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 5fe9fe8ced675f68161f0df9f2665b47f9d47ac5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174490"
---
### <a name="how-to-authenticate-with-a-provider-server-flow"></a><a name="server-auth"></a>作法：向提供者驗證 (伺服器流程)
若要讓 Mobile Apps 管理應用程式中的驗證程序，您必須向識別提供者註冊應用程式。 接著在您的 Azure App Service 中，您必須設定提供者所提供的應用程式識別碼和密碼。
如需詳細資訊，請參閱 [將驗證新增至您的應用程式](../articles/app-service-mobile/app-service-mobile-cordova-get-started-users.md)教學課程。

註冊識別提供者之後，請以提供者的名稱來呼叫 `.login()` 方法。 例如，若要以 Facebook 登入，請使用下列程式碼：

```javascript
client.login("facebook").done(function (results) {
     alert("You are now signed in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

提供者的有效值是 'aad'、'facebook'、'google'、'microsoftaccount' 和 'twitter'。

> [!NOTE]
> Google 驗證目前無法透過伺服器流程運作。  若要使用 Google 進行驗證，您必須使用[用戶端流程方法](#client-auth)。

在此情況下，Azure App Service 會管理 OAuth 2.0 驗證流程。  它會顯示所選提供者的登入頁面，並在使用識別提供者成功登入後產生 App Service 驗證權杖。 login 函式完成時會傳回 JSON 物件，此物件會在 userId 和 authenticationToken 欄位中分別顯示使用者識別碼和 App Service 驗證權杖。 您可以快取並重複使用此權杖，直到它到期為止。

### <a name="how-to-authenticate-with-a-provider-client-flow"></a><a name="client-auth"></a>作法：向提供者驗證 (用戶端流程)

您的應用程式也可以個別連絡識別提供者，然後將傳回的權杖提供給 App Service 進行驗證。 此用戶端流程可讓您為使用者提供單一登入體驗，或從識別提供者擷取其他使用者資料。

#### <a name="social-authentication-basic-example"></a>社交驗證基本範例

這個範例會使用 Facebook 用戶端 SDK 進行驗證：

```javascript
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now signed in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});

```
此範例假設個別提供者 SDK 所提供的權杖儲存在 token 變數中。

### <a name="how-to-obtain-information-about-the-authenticated-user"></a><a name="auth-getinfo"></a>作法：取得已驗證使用者的相關資訊

您可以使用 HTTP 呼叫搭配任何 AJAX 程式庫，從 `/.auth/me` 端點擷取驗證資訊。  請確定您設定 `X-ZUMO-AUTH` 標頭至您的驗證 Token。  驗證 Token 儲存於 `client.currentUser.mobileServiceAuthenticationToken`。  例如，若要使用提取 API：

```javascript
var url = client.applicationUrl + '/.auth/me';
var headers = new Headers();
headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken);
fetch(url, { headers: headers })
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

提取作為[npm 包](https://www.npmjs.com/package/whatwg-fetch)或可從[CDNJS](https://cdnjs.com/libraries/fetch)下載的瀏覽器可用。 您也可以使用 jQuery 或另一個 AJAX API 擷取資訊。  系統會以 JSON 物件形式接收資料。
