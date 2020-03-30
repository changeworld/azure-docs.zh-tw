---
title: AuthN/AuthO 的高級使用
description: 瞭解如何針對不同方案自訂應用服務中的身份驗證和授權功能，並獲取使用者聲明和不同的權杖。
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: d57b196bf95ebdf31bc459ad4b9d718fd32ca495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280829"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>在 Azure App Service 中進階使用驗證和授權

本文說明如何自訂 [App Service 中的內建驗證與授權](overview-authentication-authorization.md)，以及管理您應用程式的身分識別。 

若要快速開始，請參閱下列其中一個教學課程︰

* [教學課程：在 Azure App Service 中端對端驗證和授權使用者 (Windows)](app-service-web-tutorial-auth-aad.md)
* [教學課程：在適用於 Linux 的 Azure App Service 中端對端驗證和授權使用者](containers/tutorial-auth-aad.md)
* [如何設定 App 使用 Azure Active Directory 登入](configure-authentication-provider-aad.md)
* [如何設定 App 使用 Facebook 登入](configure-authentication-provider-facebook.md)
* [如何設定 App 以使用 Google 登入](configure-authentication-provider-google.md)
* [如何設定 App 使用 Microsoft 帳戶登入](configure-authentication-provider-microsoft.md)
* [如何設定 App 以使用 Twitter 登入](configure-authentication-provider-twitter.md)

## <a name="use-multiple-sign-in-providers"></a>使用多個登入提供者

入口網站設定不會提供周全的方式，向您的使用者顯示多個登入提供者 (例如 Facebook 和 Twitter)。 不過，要將功能新增至您的應用程式並不困難。 步驟概述如下：

首先，在 Azure 入口網站的 [驗證/授權]**** 頁面中，設定您需要啟用的每一個識別提供者。

在 [當要求未經驗證時所要採取的動作]**** 中，選取 [允許匿名要求 (無動作)]****。

在登入頁面或導覽列、或是您應用程式的任何其他位置中，將登入連結新增至您啟用的每個提供者 (`/.auth/login/<provider>`)。 例如：

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

當使用者按一下其中一個連結時，隨即會開啟個別登入頁面將使用者登入。

若要將登入後的使用者重新導向至自訂 URL，請使用 `post_login_redirect_url` 查詢字串參數 (請勿與您身分識別提供者組態中的重新導向 URI 混淆)。 例如，若要在使用者登入之後，將他們導向 `/Home/Index`，請使用下列 HTML 程式碼：

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>驗證提供者的權杖

在用戶端導向的登入中，應用程式會以手動方式將使用者登入提供者，然後將驗證權杖提交給 App Service 進行驗證 (請參閱[驗證流程](overview-authentication-authorization.md#authentication-flow))。 此驗證本身並不會實際為您授與所需應用程式資源的存取權，但成功的驗證會給予您可用來存取應用程式資源的工作階段權杖。 

若要驗證提供者權杖，App Service 應用程式必須先以所需的提供者進行設定。 在執行階段，在您從提供者擷取驗證權杖之後，請將權杖公佈到 `/.auth/login/<provider>` 進行驗證。 例如： 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

權杖的格式會隨著提供者而稍有不同。 如需詳細資訊，請參閱下表︰

| 提供者值 | 要求本文中需要 | 註解 |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | `expires_in` 是選用屬性。 <br/>從即時服務要求權杖時，請一律要求 `wl.basic` 範圍。 |
| `google` | `{"id_token":"<id_token>"}` | `authorization_code` 是選用屬性。 若已指定，也可以選擇性地與 `redirect_uri` 屬性搭配使用。 |
| `facebook`| `{"access_token":"<user_access_token>"}` | 使用來自 Facebook 的有效[使用者存取權杖](https://developers.facebook.com/docs/facebook-login/access-tokens)。 |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

如果提供者權杖驗證成功，API 會連同 `authenticationToken` 在回應本文中一起傳回，這是您的工作階段權杖。 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

在取得此工作階段權杖之後，您可以藉由將 `X-ZUMO-AUTH` 標頭新增至 HTTP 要求，來存取受保護的應用程式資源。 例如： 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>登出工作階段

使用者可以將 `GET` 要求傳送到應用程式的 `/.auth/logout` 端點起始登出。 `GET` 要求會執行下列作業：

- 清除目前工作階段的驗證 Cookie。
- 從安全性權杖存放區中刪除目前使用者的安全性權杖。
- 對於 Azure Active Directory 和 Google，對身分識別提供者執行伺服器端登出。

以下是網頁中的簡單登出連結：

```HTML
<a href="/.auth/logout">Sign out</a>
```

成功登出預設會將用戶端重新導向到 URL `/.auth/logout/done`。 您可以新增 `post_logout_redirect_uri` 查詢參數來變更登出後重新導向頁面。 例如：

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

建議您將值 `post_logout_redirect_uri`[編碼](https://wikipedia.org/wiki/Percent-encoding)。

使用完整的 URL，URL 必須裝載於相同的網域，或設定為應用程式允許的外部重新導向 URL。 在下列範例中，將重新導向至不在相同的網域中裝載的 `https://myexternalurl.com`：

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

在[Azure 雲外殼](../cloud-shell/quickstart.md)中運行以下命令 ：

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>保留 URL 片段

使用者登入您的應用程式之後，通常想要重新導向至相同頁面的相同區段，例如 `/wiki/Main_Page#SectionZ`。 不過，因為 [URL 片段](https://wikipedia.org/wiki/Fragment_identifier) (例如，`#SectionZ`) 不會傳送到伺服器，因此 OAuth 登入完成之後預設不會保留，而會重新導向回到您的應用程式。 使用者會在需要再次瀏覽至所需的錨點時感受到欠佳的體驗。 這項限制適用於所有的伺服器端驗證解決方案。

在 App Service 驗證中，您可以在 OAuth 登入保留 URL 片段。 若要這麼做，將稱為 `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` 的應用程式設定設為 `true`。 您可以在 [Azure 入口網站](https://portal.azure.com)中這麼做，也可以直接在 [Azure Cloud Shell](../cloud-shell/quickstart.md) 中執行下列命令：

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>存取使用者宣告

App Service 會使用特殊標頭，將使用者宣告傳遞至您的應用程式。 外部要求不得設定這些標頭，因此僅在由 App Service 設定時，這些標頭才會出現。 某些範例標頭包括︰

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

以任何語言或架構撰寫的程式碼可以從這些標頭中取得所需的資訊。 針對 ASP.NET 4.6 應用程式， **ClaimsPrincipal** 會自動設定適當的值。 但是，ASP.NET Core 不提供與應用服務使用者聲明集成的身份驗證中介軟體。 有關解決方法，請參閱[MaximeRouiller.Azure.AppService.EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)。

您的應用程式也可以藉由呼叫 `/.auth/me` 來取得關於已驗證使用者的其他詳細資料。 Mobile Apps 伺服器 SDK 提供 Helper 方法來處理此資料。 如需詳細資訊，請參閱[如何使用 Azure Mobile Apps Node.js SDK ](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity)和[使用適用於 Azure Mobile Apps 的 .NET 後端伺服器 SDK](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info)。

## <a name="retrieve-tokens-in-app-code"></a>在應用程式程式碼中取出權杖

提供者專屬權杖會從伺服器程式碼插入到要求標頭，以供您輕鬆地存取。 下表顯示可能的權杖標頭名稱：

| 提供者 | 標頭名稱 |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook 權杖 | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Microsoft 帳戶 | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

從用戶端程式碼 (例如行動應用程式或瀏覽器內的 JavaScript) 將 HTTP `GET` 要求傳送至 `/.auth/me`。 傳回的 JSON 具有提供者專屬權杖。

> [!NOTE]
> 存取權杖是用於存取提供者資源，因此僅在您使用用戶端密碼設定您的提供者時，這些權杖才會出現。 若要了解如何取得重新整理權杖，請參閱「重新整理存取權杖」。

## <a name="refresh-identity-provider-tokens"></a>重新整理識別提供者權杖

當提供者的存取權杖 (而非[工作階段權杖](#extend-session-token-expiration-grace-period)) 到期時，您必須於再次使用該權杖之前重新驗證使用者。 您可以向應用程式的 `/.auth/refresh` 端點發出 `GET` 呼叫，以避免權杖到期。 一經呼叫，App Service 就會自動重新整理已驗證使用者在權杖放區中的存取權杖。 您應用程式程式碼的後續權杖要求會取得重新整理過的權杖。 不過，若要進行權杖重新整理，權杖存放區必須包含您提供者的[重新整理權杖](https://auth0.com/learn/refresh-tokens/)。 每個提供者會記載其重新整理權杖的取得方法，而下列清單僅為簡短摘要：

- **Google**：將 `access_type=offline` 查詢字串參數附加至您的 `/.auth/login/google` API 呼叫。 如果是使用 Mobile Apps SDK，您可以將參數新增至其中一個 `LogicAsync` 多載 (請參閱 [Google 重新整理權杖](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens))。
- **Facebook**：不提供重新整理權杖。 長時間執行的權杖會在 60 天內到期 (請參閱 [Facebook 到期和存取權杖的擴充功能](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension))。
- **Twitter**：存取權杖不會到期 (請參閱 [Twitter OAuth 常見問題集](https://developer.twitter.com/en/docs/basics/authentication/FAQ))。
- **Microsoft 帳戶**：當您[設定 Microsoft 帳戶驗證設定](configure-authentication-provider-microsoft.md)時，請選取 `wl.offline_access` 範圍。
- **Azure Active Directory**：在 [https://resources.azure.com](https://resources.azure.com) 中，執行下列步驟：
    1. 在頁面的頂端，選取 [讀取/寫入]****。
    2. 在左側瀏覽器中，導航到**subscriptions** > **_\<訂閱\_名稱_** > **providers** >  > **sites** >  > **config** > **resourceGroups** >  > 資源組**_\<\_資源組\_名稱>_** 提供程式**Microsoft.Web**網站**_\<應用\_名稱>_** 配置**身份驗證**。 
    3. 按一下 **[編輯]**。
    4. 修改下列屬性。 將_\<應用\_id>_ 替換為要訪問的服務的 Azure 活動目錄應用程式 ID。

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. 按一下 [放置]****。 

設定好提供者之後，您可以在權杖存放區中[尋找重新整理權杖和存取權杖的到期時間](#retrieve-tokens-in-app-code)。 

要隨時刷新訪問權杖，只需使用任何語言調用`/.auth/refresh`。 下列程式碼片段會使用 jQuery 來重新整理 JavaScript 用戶端的存取權杖。

```JavaScript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

如果使用者將授予您應用程式的權限撤銷，您對 `/.auth/me` 的呼叫可能會失敗，並出現 `403 Forbidden` 回應。 若要診斷錯誤，請檢查您的應用程式記錄以取得詳細資訊。

## <a name="extend-session-token-expiration-grace-period"></a>延長工作階段權杖到期寬限期

經過驗證的工作階段會在 8 小時後過期。 已驗證的工作階段到期之後，依預設會有 72 小時的寬限期。 在此寬限期內，您可以使用 App Service 重新整理工作階段權杖，而無須重新驗證使用者。 當您的工作階段權杖失效時，您只需要呼叫 `/.auth/refresh`，而不必自行追蹤權杖到期日。 一旦 72 小時寬限期結束後，使用者必須重新登入，才能取得有效的工作階段權杖。

如果 72 小時的時間不夠您使用，您可以延長此到期時間範圍。 將到期日延長超過一段很長的期間可能會造成重大的安全性影響 (例如，當驗證權杖外洩或遭竊時)。 因此，請將它保留為預設的 72 小時，或將延長期間設為最小值。

若要延長預設的到期時間範圍，請在 [Cloud Shell](../cloud-shell/overview.md) 中執行下列命令。

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> 寬限期僅適用於 App Service 的已驗證工作階段，而不適用於識別提供者的權杖。 已過期的提供者權杖沒有任何寬限期。 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>限制登入帳戶的網域

Microsoft 帳戶和 Azure Active Directory 都可讓您從多個網域登入。 例如，Microsoft 帳戶允許 _outlook.com_、_live.com_ 和 _hotmail.com_ 帳戶。 Azure AD 允許登錄帳戶的任意數量的自訂域。 但是，您可能希望將使用者直接加速到自己的品牌 Azure AD 登錄頁（如`contoso.com`）。 要建議登錄帳戶的功能變數名稱，請按照以下步驟操作。

在[https://resources.azure.com](https://resources.azure.com)中 導航到**訂閱** > **_\<\_名稱_** > **資源** > **_\<\_組資源組\_名稱>_****providers** >  > **sites** >  > **config** > **Microsoft.Web** > 提供程式 Microsoft.Web 網站**_\<應用\_名稱>_** 配置**身份驗證**。 

按一下 [編輯]****、修改下列屬性，然後按一下 [放置]****。 請確保將_\<功能變數名稱\_>_ 替換為所需的域。

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

此設置將`domain_hint`查詢字串參數追加到登錄重定向 URL。 

> [!IMPORTANT]
> 用戶端可以在收到重定向 URL 後刪除`domain_hint`參數，然後使用其他域登錄。 因此，雖然此功能很方便，但它不是一個安全功能。
>

## <a name="authorize-or-deny-users"></a>授權或拒絕使用者

雖然應用服務負責最簡單的授權案例（即拒絕未經身份驗證的請求），但應用可能需要更細細微性的授權行為，例如僅限制對特定使用者組的訪問。 在某些情況下，您需要編寫自訂應用程式代碼以允許或拒絕對登錄使用者的訪問。 在其他情況下，應用服務或您的身份供應商可能無需更改代碼即可提供説明。

- [伺服器層級](#server-level-windows-apps-only)
- [標識提供程式級別](#identity-provider-level)
- [應用程式級別](#application-level)

### <a name="server-level-windows-apps-only"></a>伺服器級別（僅限 Windows 應用）

對於任何 Windows 應用，您可以通過編輯*Web.config*檔來定義 IIS Web 服務器的授權行為。 Linux 應用程式不使用 IIS，並且無法通過*Web.config*進行配置。

1. 巡覽到 `https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. 在應用服務檔的瀏覽器資源管理器中，導航到*網站/wwwroot*。 如果*Web.config*不存在，請通過選擇**+** > **"新檔**"來創建它。 

1. 選擇*Web.config*的鉛筆進行編輯。 添加以下配置代碼，然後按一下 **"保存**"。 如果*Web.config*已存在，只需`<authorization>`添加元素中的所有內容。 添加要在元素中允許的`<allow>`帳戶。

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>標識提供程式級別

標識提供程式可能會提供某些交鑰匙授權。 例如：

- 對於[Azure 應用服務](configure-authentication-provider-aad.md)，可以直接在 Azure AD 中[管理企業級訪問](../active-directory/manage-apps/what-is-access-management.md)。 有關說明，請參閱[如何刪除使用者對應用程式的訪問](../active-directory/manage-apps/methods-for-removing-user-access.md)。
- 對於[Google，](configure-authentication-provider-google.md)可以配置屬於[組織的](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations)Google API 專案，以便僅允許組織中的使用者訪問（請參閱 Google[設置**OAuth 2.0**支援頁面](https://support.google.com/cloud/answer/6158849?hl=en)）。

### <a name="application-level"></a>應用程式層級

如果其他任一級別不提供所需的授權，或者平臺或標識提供程式不受支援，則必須編寫自訂代碼，根據[使用者聲明](#access-user-claims)授權使用者。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教程：對使用者進行端到端（Windows）](app-service-web-tutorial-auth-aad.md)
> [教程的身份驗證和授權：對使用者進行端點對端點驗證和授權 （Linux）](containers/tutorial-auth-aad.md)
