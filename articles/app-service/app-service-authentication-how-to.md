---
title: 驗證/AuthZ 的 Advanced 使用
description: 瞭解如何針對不同的案例自訂 App Service 中的驗證和授權功能，以及取得使用者宣告和不同的權杖。
ms.topic: article
ms.date: 07/08/2020
ms.custom: seodec18
ms.openlocfilehash: 93c697162bfcb51b77c2e6f48b5824b81070bf51
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91816419"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>在 Azure App Service 中進階使用驗證和授權

本文說明如何自訂 [App Service 中的內建驗證與授權](overview-authentication-authorization.md)，以及管理您應用程式的身分識別。 

若要快速開始，請參閱下列其中一個教學課程︰

* [教學課程：在 Azure App Service 中對使用者進行端對端驗證和授權](tutorial-auth-aad.md)
* [如何設定 App 使用 Azure Active Directory 登入](configure-authentication-provider-aad.md)
* [如何設定 App 使用 Facebook 登入](configure-authentication-provider-facebook.md)
* [如何設定 App 以使用 Google 登入](configure-authentication-provider-google.md)
* [如何設定 App 使用 Microsoft 帳戶登入](configure-authentication-provider-microsoft.md)
* [如何設定 App 以使用 Twitter 登入](configure-authentication-provider-twitter.md)
* [如何設定您的應用程式使用 OpenID Connect 提供者登入 (預覽) ](configure-authentication-provider-openid-connect.md)

## <a name="use-multiple-sign-in-providers"></a>使用多個登入提供者

入口網站設定不會提供周全的方式，向您的使用者顯示多個登入提供者 (例如 Facebook 和 Twitter)。 不過，要將功能新增至您的應用程式並不困難。 步驟概述如下：

首先，在 Azure 入口網站的 [驗證/授權]**** 頁面中，設定您需要啟用的每一個識別提供者。

在 [當要求未經驗證時所要採取的動作]**** 中，選取 [允許匿名要求 (無動作)]****。

在登入頁面或導覽列、或是您應用程式的任何其他位置中，將登入連結新增至您啟用的每個提供者 (`/.auth/login/<provider>`)。 例如：

```html
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

當使用者按一下其中一個連結時，隨即會開啟個別登入頁面將使用者登入。

若要將登入後的使用者重新導向至自訂 URL，請使用 `post_login_redirect_url` 查詢字串參數 (請勿與您身分識別提供者組態中的重新導向 URI 混淆)。 例如，若要在使用者登入之後，將他們導向 `/Home/Index`，請使用下列 HTML 程式碼：

```html
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

權杖的格式會隨著提供者而稍有不同。 如需詳細資訊，請參閱下表：

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

```html
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

在 [Azure Cloud Shell](../cloud-shell/quickstart.md)中執行下列命令：

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

以任何語言或架構撰寫的程式碼可以從這些標頭中取得所需的資訊。 針對 ASP.NET 4.6 應用程式， **ClaimsPrincipal** 會自動設定適當的值。 不過，ASP.NET Core 不會提供與 App Service 使用者宣告整合的驗證中介軟體。 如需因應措施，請參閱 [MaximeRouiller AppService. EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)。

如果您的應用程式已啟用 [權杖存放區](overview-authentication-authorization.md#token-store) ，您也可以藉由呼叫來取得已驗證使用者的其他詳細資料 `/.auth/me` 。 Mobile Apps 伺服器 SDK 提供 Helper 方法來處理此資料。 如需詳細資訊，請參閱[如何使用 Azure Mobile Apps Node.js SDK ](/previous-versions/azure/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk#howto-tables-getidentity)和[使用適用於 Azure Mobile Apps 的 .NET 後端伺服器 SDK](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#user-info)。

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

從您的用戶端程式代碼 (例如行動應用程式或在瀏覽器中的 JavaScript) ，將 HTTP `GET` 要求傳送至 `/.auth/me` (的 [權杖存放區](overview-authentication-authorization.md#token-store) 必須) 啟用。 傳回的 JSON 具有提供者專屬權杖。

> [!NOTE]
> 存取權杖是用於存取提供者資源，因此僅在您使用用戶端密碼設定您的提供者時，這些權杖才會出現。 若要了解如何取得重新整理權杖，請參閱「重新整理存取權杖」。

## <a name="refresh-identity-provider-tokens"></a>重新整理識別提供者權杖

當提供者的存取權杖 (而非[工作階段權杖](#extend-session-token-expiration-grace-period)) 到期時，您必須於再次使用該權杖之前重新驗證使用者。 您可以向應用程式的 `/.auth/refresh` 端點發出 `GET` 呼叫，以避免權杖到期。 當呼叫時，App Service 會自動重新整理已驗證使用者的 [權杖存放區](overview-authentication-authorization.md#token-store) 中的存取權杖。 您應用程式程式碼的後續權杖要求會取得重新整理過的權杖。 不過，若要進行權杖重新整理，權杖存放區必須包含您提供者的[重新整理權杖](https://auth0.com/learn/refresh-tokens/)。 每個提供者會記載其重新整理權杖的取得方法，而下列清單僅為簡短摘要：

- **Google**：將 `access_type=offline` 查詢字串參數附加至您的 `/.auth/login/google` API 呼叫。 如果是使用 Mobile Apps SDK，您可以將參數新增至其中一個 `LogicAsync` 多載 (請參閱 [Google 重新整理權杖](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens))。
- **Facebook**：不提供重新整理權杖。 長時間執行的權杖會在 60 天內到期 (請參閱 [Facebook 到期和存取權杖的擴充功能](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension))。
- **Twitter**：存取權杖不會到期 (請參閱 [Twitter OAuth 常見問題集](https://developer.twitter.com/en/docs/basics/authentication/FAQ))。
- **Microsoft 帳戶**：當您[設定 Microsoft 帳戶驗證設定](configure-authentication-provider-microsoft.md)時，請選取 `wl.offline_access` 範圍。
- **Azure Active Directory**：在 [https://resources.azure.com](https://resources.azure.com) 中，執行下列步驟：
    1. 在頁面的頂端，選取 [讀取/寫入]****。
    2. 在左側瀏覽器中，流覽至 [訂用帳戶 **] > *** *_ \<subscription\_name_** > **resourceGroups** > *_* * * [ \<resource\_group\_name> _>**提供者**]  >  **Microsoft.Web**  >  **sites** > * *_ \<app\_name> _ * * > 設定**config**  >  **[authsettings**。 
    3. 按一下 **[編輯]** 。
    4. 修改下列屬性。 取代 _\<app\_id>_ 為您要存取之服務的 Azure Active Directory 應用程式識別碼。

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. 按一下 [放置]****。 

設定好提供者之後，您可以在權杖存放區中[尋找重新整理權杖和存取權杖的到期時間](#retrieve-tokens-in-app-code)。 

若要隨時重新整理您的存取權杖，只要以 `/.auth/refresh` 任何語言呼叫即可。 下列程式碼片段會使用 jQuery 來重新整理 JavaScript 用戶端的存取權杖。

```javascript
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

Microsoft 帳戶和 Azure Active Directory 都可讓您從多個網域登入。 例如，Microsoft 帳戶允許 _outlook.com_、_live.com_ 和 _hotmail.com_ 帳戶。 Azure AD 允許登入帳戶有任意數目的自訂網域。 不過，您可能會想要將使用者直接帶到您自己的品牌 Azure AD 登入頁面 (例如 `contoso.com`) 。 若要建議登入帳戶的功能變數名稱，請遵循下列步驟。

在中，流覽至 [訂用帳戶] [https://resources.azure.com](https://resources.azure.com) > * *_ \<subscription\_name_** > **resourceGroups** > *_* * * [ **subscriptions** \<resource\_group\_name> _>**提供者**]  >  **Microsoft.Web**  >  > * *** ** _ \<app\_name> _ * * > 設定**config**  >  **[authsettings**。 

按一下 [編輯]****、修改下列屬性，然後按一下 [放置]****。 請務必將取代為 _\<domain\_name>_ 您想要的網域。

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

此設定會將 `domain_hint` 查詢字串參數附加至登入重新導向 URL。 

> [!IMPORTANT]
> 用戶端可能會 `domain_hint` 在收到重新導向 URL 之後移除參數，然後以不同的網域登入。 因此，雖然此函式很方便，但它並不是安全性功能。
>

## <a name="authorize-or-deny-users"></a>授權或拒絕使用者

雖然 App Service 會處理最簡單的授權案例 (亦即拒絕未經驗證的要求) ，但您的應用程式可能需要更細緻的授權行為，例如限制只能存取特定的使用者群組。 在某些情況下，您需要撰寫自訂的應用程式程式碼，以允許或拒絕存取已登入的使用者。 在其他情況下，App Service 或您的身分識別提供者可能可以在不需要變更程式碼的情況下協助您。

- [伺服器層級](#server-level-windows-apps-only)
- [身分識別提供者層級](#identity-provider-level)
- [應用層級](#application-level)

### <a name="server-level-windows-apps-only"></a>伺服器層級 (僅限 Windows 應用程式) 

針對任何 Windows 應用程式，您可以藉由編輯 *Web.config* 檔案來定義 IIS web 伺服器的授權行為。 Linux 應用程式不會使用 IIS，也無法透過 *Web.config*設定。

1. 巡覽到 `https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. 在 App Service 檔案的瀏覽器瀏覽器中，流覽至 *site/wwwroot*。 如果*Web.config*不存在，請選取 [新增檔案] 加以建立 **+**  >  ** **。 

1. 選取 *Web.config* 的鉛筆來編輯它。 新增下列設定程式碼，然後按一下 [ **儲存**]。 如果 *Web.config* 已存在，只要 `<authorization>` 在其中新增專案。 在元素中新增您想要允許的帳戶 `<allow>` 。

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

### <a name="identity-provider-level"></a>身分識別提供者層級

身分識別提供者可能會提供特定的金鑰授權。 例如：

- 針對 [Azure App Service](configure-authentication-provider-aad.md)，您可以直接在 Azure AD 中 [管理企業層級的存取權](../active-directory/manage-apps/what-is-access-management.md) 。 如需相關指示，請參閱 [如何移除使用者的應用程式存取權](../active-directory/manage-apps/methods-for-removing-user-access.md)。
- 針對 [google](configure-authentication-provider-google.md)，屬於 [組織](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) 的 google API 專案可以設定為只允許您組織中的使用者存取 (請參閱 [Google 的 **設定 OAuth 2.0** 支援頁面](https://support.google.com/cloud/answer/6158849?hl=en)) 。

### <a name="application-level"></a>應用程式層級

如果其中一個層級未提供您所需的授權，或您的平臺或身分識別提供者不受支援，則您必須撰寫自訂程式碼，以根據 [使用者宣告](#access-user-claims)來授權使用者。

## <a name="configure-using-a-file-preview"></a><a name="config-file"> </a>使用檔案 (預覽設定) 

您可以選擇性地透過部署所提供的檔案來設定您的驗證設定。 App Service 驗證/授權的某些預覽功能可能需要這項功能。

> [!IMPORTANT]
> 請記住，您的應用程式裝載（因此此檔案）可能會在環境之間移動，就像使用 [插槽](./deploy-staging-slots.md)一樣。 您可能會想要將不同的應用程式註冊釘選到每個位置，在這些情況下，您應該繼續使用標準的設定方法，而不是使用設定檔案。

### <a name="enabling-file-based-configuration"></a>啟用以檔案為基礎的設定

> [!CAUTION]
> 在預覽期間，啟用檔案型設定將會停用透過某些用戶端（例如 Azure 入口網站、Azure CLI 和 Azure PowerShell）來管理應用程式的 App Service 驗證/授權功能。

1. 在您的專案根目錄中為您的設定建立新的 JSON 檔案 (部署至 web/函數應用程式) 中的 D:\home\site\wwwroot。 根據以檔案為基礎的設定 [參考](#configuration-file-reference)，填入所需的設定。 如果修改現有的 Azure Resource Manager 設定，請務必將集合中所捕獲的屬性轉譯為 `authsettings` 您的設定檔。

2. 修改現有的設定，此設定是在的 [Azure Resource Manager](../azure-resource-manager/management/overview.md) api 中所捕捉 `Microsoft.Web/sites/<siteName>/config/authsettings` 。 若要修改此程式，您可以使用 [Azure Resource Manager 範本](../azure-resource-manager/templates/overview.md) 或 [Azure 資源總管](https://resources.azure.com/)之類的工具。 在 [authsettings 集合中，您必須 (設定三個屬性，而且可能會將其他屬性) 移除：

    1.  設定 `enabled` 為 "true"
    2.  設定 `isAuthFromFile` 為 "true"
    3.  設定 `authFilePath` 為檔案名 (例如「auth.js開啟」 ) 

> [!NOTE]
> 的格式會 `authFilePath` 因平臺而異。 在 Windows 中，支援相對路徑和絕對路徑。 建議使用相對的。 針對 Linux，目前僅支援絕對路徑，因此設定的值應為 "/home/site/wwwroot/auth.json" 或類似。

當您進行此設定更新之後，檔案的內容就會用來定義該網站 App Service 驗證/授權的行為。 如果您想要回到 Azure Resource Manager 設定，可以將設定 `isAuthFromFile` 回「false」。

### <a name="configuration-file-reference"></a>設定檔案參考

將從設定檔中參考的任何秘密都必須儲存為 [應用程式設定](./configure-common.md#configure-app-settings)。 您可以將設定命名為任何您想要的名稱。 請確定來自設定檔的參考使用相同的金鑰。

下列耗盡檔案內可能的設定選項：

```json
{
    "platform": {
        "enabled": <true|false>
    },
    "globalValidation": {
        "requireAuthentication": <true|false>,
        "unauthenticatedClientAction": "RedirectToLoginPage|AllowAnonymous|Return401|Return403",
        "redirectToProvider": "<default provider alias>",
        "excludedPaths": [
            "/path1",
            "/path2"
        ]
    },
    "httpSettings": {
        "requireHttps": <true|false>,
        "routes": {
            "apiPrefix": "<api prefix>"
        },
        "forwardProxy": {
            "convention": "NoProxy|Standard|Custom",
            "customHostHeaderName": "<host header value>",
            "customProtoHeaderName": "<proto header value>"
        }
    },
    "login": {
        "routes": {
            "logoutEndpoint": "<logout endpoint>"
        },
        "tokenStore": {
            "enabled": <true|false>,
            "tokenRefreshExtensionHours": "<double>",
            "fileSystem": {
                "directory": "<directory to store the tokens in if using a file system token store (default)>"
            },
            "azureBlobStorage": {
                "sasUrlSettingName": "<app setting name containing the sas url for the Azure Blob Storage if opting to use that for a token store>"
            }
        },
        "preserveUrlFragmentsForLogins": <true|false>,
        "allowedExternalRedirectUri": [
            "https://uri1.azurewebsites.net/",
            "https://uri2.azurewebsites.net/",
            "url_scheme_of_your_app://easyauth.callback"
        ],
        "cookieExpiration": {
            "convention": "FixedTime|IdentityProviderDerived",
            "timeToExpiration": "<timespan>"
        },
        "nonce": {
            "validateNonce": <true|false>,
            "nonceExpirationInterval": "<timespan>"
        }
    },
    "identityProviders": {
        "azureActiveDirectory": {
            "enabled": <true|false>,
            "registration": {
                "openIdIssuer": "<issuer url>",
                "clientId": "<app id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_AAD_SECRET",
            },
            "login": {
                "loginParameters": [
                    "paramName1=value1",
                    "paramName2=value2"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "facebook": {
            "enabled": <true|false>,
            "registration": {
                "appId": "<app id>",
                "appSecretSettingName": "APP_SETTING_CONTAINING_FACEBOOK_SECRET"
            },
            "graphApiVersion": "v3.3",
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
        },
        "gitHub": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GITHUB_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "google": {
            "enabled": true,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GOOGLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "twitter": {
            "enabled": <true|false>,
            "registration": {
                "consumerKey": "<consumer key>",
                "consumerSecretSettingName": "APP_SETTING_CONTAINING TWITTER_CONSUMER_SECRET"
            }
        },
        "openIdConnectProviders": {
            "<providerName>": {
                "enabled": <true|false>,
                "registration": {
                    "clientId": "<client id>",
                    "clientCredential": {
                        "secretSettingName": "<name of app setting containing client secret>"
                    },
                    "openIdConnectConfiguration": {
                        "authorizationEndpoint": "<url specifying authorization endpoint>",
                        "tokenEndpoint": "<url specifying token endpoint>",
                        "issuer": "<url specifying issuer>",
                        "certificationUri": "<url specifying jwks endpoint>",
                        "wellKnownOpenIdConfiguration": "<url specifying .well-known/open-id-configuration endpoint - if this property is set, the other properties of this object are ignored, and authorizationEndpoint, tokenEndpoint, issuer, and certificationUri are set to the corresponding values listed at this endpoint>"
                    }
                },
                "login": {
                    "nameClaimType": "<name of claim containing name>",
                    "scope": [
                        "openid",
                        "profile",
                        "email"
                    ],
                    "loginParameterNames": [
                        "paramName1=value1",
                        "paramName2=value2"
                    ],
                }
            },
            //...
        }
    }
}
```

## <a name="pin-your-app-to-a-specific-authentication-runtime-version"></a>將您的應用程式釘選到特定的驗證執行階段版本

當您啟用驗證/授權時，會將平臺中介軟體插入至您的 HTTP 要求管線，如 [功能總覽](overview-authentication-authorization.md#how-it-works)中所述。 此平臺中介軟體會定期以新功能和改良功能更新，以做為例行平臺更新的一部分。 根據預設，您的 web 或函數應用程式將會在此平臺中介軟體的最新版本上執行。 這些自動更新一律是回溯相容。 不過，在罕見的情況下，此自動更新會為您的 web 或函數應用程式導入執行時間問題，您可以暫時回復為先前的中介軟體版本。 本文說明如何將應用程式暫時釘選至特定版本的驗證中介軟體。

### <a name="automatic-and-manual-version-updates"></a>自動和手動版本更新 

您可以藉由設定應用程式的設定，將應用程式釘選至特定版本的平臺中介軟體 `runtimeVersion` 。 除非您選擇將應用程式明確地釘選回特定版本，否則您的應用程式一律會在最新版本上執行。 每次都支援數個版本。 如果您釘選到已不再支援的無效版本，您的應用程式將會改用最新版本。 若要一律執行最新版本，請將設定 `runtimeVersion` 為 ~ 1。 

### <a name="view-and-update-the-current-runtime-version"></a>檢視並更新目前的執行階段版本

您可以變更應用程式所使用的執行階段版本。 新的執行階段版本應該會在重新開機應用程式之後生效。 

#### <a name="view-the-current-runtime-version"></a>檢視目前的執行階段版本

您可以使用 Azure CLI，或透過您應用程式中的其中一個 built0 版本 HTTP 端點，來查看平臺驗證中介軟體的目前版本。

##### <a name="from-the-azure-cli"></a>從 Azure CLI

使用 Azure CLI，使用 [az webapp auth show](/cli/azure/webapp/auth?view=azure-cli-latest&preserve-view=true#az-webapp-auth-show) 命令來查看目前的中介軟體版本。

```azurecli-interactive
az webapp auth show --name <my_app_name> \
--resource-group <my_resource_group>
```

在此程式碼中， `<my_app_name>` 將取代為您的應用程式名稱。 也請 `<my_resource_group>` 以您應用程式的資源組名取代。

您將會 `runtimeVersion` 在 CLI 輸出中看到欄位。 它會類似下列範例輸出，為了清楚起見，已截斷這些輸出： 
```output
{
  "additionalLoginParams": null,
  "allowedAudiences": null,
    ...
  "runtimeVersion": "1.3.2",
    ...
}
```

##### <a name="from-the-version-endpoint"></a>從版本端點

您也可以在應用程式上叫用/.auth/version 端點，以查看正在執行應用程式的目前中介軟體版本。 它會類似下列範例輸出：
```output
{
"version": "1.3.2"
}
```

#### <a name="update-the-current-runtime-version"></a>更新目前的執行階段版本

使用 Azure CLI，您可以使用 `runtimeVersion` [az webapp auth update](/cli/azure/webapp/auth?view=azure-cli-latest&preserve-view=true#az-webapp-auth-update) 命令來更新應用程式中的設定。

```azurecli-interactive
az webapp auth update --name <my_app_name> \
--resource-group <my_resource_group> \
--runtime-version <version>
```

`<my_app_name>`以您的應用程式名稱取代。 也請 `<my_resource_group>` 以您應用程式的資源組名取代。 此外，請 `<version>` 以1.x 執行時間的有效版本或 `~1` 最新版本取代。 您可以在 [這裡 (] 的不同執行階段版本上找到版本 https://github.com/Azure/app-service-announcements) 資訊，以協助判斷要釘選的版本。

您可以選擇上述程式碼範例中的 [試試看]****，從 [Azure Cloud Shell](../cloud-shell/overview.md) 執行此命令。 在執行 [az login](/cli/azure/reference-index#az-login) 登入之後，您也可以使用[本機 Azure CLI](/cli/azure/install-azure-cli) 來執行此命令。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：端對端驗證和授權使用者](tutorial-auth-aad.md)