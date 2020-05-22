---
title: Azure 靜態 Web Apps 中的路由
description: 了解後端路由規則，以及如何使用角色來保護路由。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 4a9639343827ebc5bb17a6d62d9b65d0b561e932
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595127"
---
# <a name="routes-in-azure-static-web-apps-preview"></a>Azure 靜態 Web Apps 預覽版中的路由

Azure 靜態 Web Apps 中的路由會定義靜態內容和 API 的後端路由規則及授權行為。 這些規則會定義為 _routes.json_ 檔案中的規則陣列。

- _routes.json_ 檔案必須存在於應用程式組建成品資料夾的根目錄。
- 規則會依其在 `routes` 陣列中出現的順序來執行。
- 規則評估會在最初相符停止。 路由規則不會鏈結在一起。
- 角色會在 _routes.json_ 檔案中定義，而使用者會透過[邀請](authentication-authorization.md)與角色相關聯。
- 您可以完全控制角色名稱。

路由的主題與驗證和授權概念明顯重疊。 請務必閱讀[驗證和授權](authentication-authorization.md)指南以及這篇文章。

## <a name="location"></a>Location

_routes.json_ 檔案必須存在於應用程式組建成品資料夾的根目錄。 如果您的 Web 應用程式包含從特定資料夾將建置的檔案複製到組建成品資料夾的組建步驟，則 _routes.json_ 檔案必須存在於該特定資料夾中。

下表列出可讓您放置 _routes.json_ 檔案的適當位置，以用於多個前端 JavaScript 架構和程式庫。

|Framework / 程式庫 | Location  |
|---------|----------|
| Angular | _assets_   |
| React   | _public_  |
| Svelte  | _public_   |
| Vue     | _public_ |

## <a name="defining-routes"></a>定義路由

路由會在 _routes.json_ 檔案中定義，做為 `routes` 屬性上的路由規則陣列。 每個規則都是由路由模式組成，以及一或多個選擇性規則屬性。 如需使用範例，請參閱[範例路由檔案](#example-route-file)。

| 規則內容  | 必要 | 預設值 | 註解                                                      |
| -------------- | -------- | ------------- | ------------------------------------------------------------ |
| `route`        | 是      | n/a          | 呼叫者所要求的路由模式。<ul><li>路由路徑結尾處支援[萬用字元](#wildcards)。 例如，路由 _admin/\*_ 符合 _admin_ 路徑下的任何路由。<li>路由的預設檔案是 _index.html_。</ul>|
| `serve`        | 否       | n/a          | 定義從要求傳回的檔案或路徑。 檔案路徑和名稱可與所要求的路徑不同。 如果定義了 `serve` 值，則會使用要求的路徑。 |
| `allowedRoles` | 否       | 匿名     | 角色名稱的陣列。 <ul><li>有效的字元包括 `a-z`、`A-Z`、`0-9` 和 `_`。<li>內建角色 `anonymous` 適用於所有未經驗證的使用者。<li>內建角色 `authenticated` 適用於任何已登入的使用者。<li>使用者必須至少屬於一個角色。<li>角色會以 _OR_ 為基礎進行比對。 如果使用者是在任何列出的角色中，則會授與存取權。<li>個別使用者會透過[邀請](authentication-authorization.md)，與角色相關聯。</ul> |
| `statusCode`   | 否       | 200           | 要求的 [HTTP 狀態碼](https://wikipedia.org/wiki/List_of_HTTP_status_codes)回應。 |

## <a name="securing-routes-with-roles"></a>使用角色保護路由

將一或多個角色名稱新增至規則的 `allowedRoles` 陣列來保護路由。 如需使用範例，請參閱[範例路由檔案](#example-route-file)。

根據預設，每個使用者都屬於內建的 `anonymous` 角色，且所有登入的使用者都是 `authenticated` 角色的成員。 例如，若要將路由限制為僅經驗證的使用者，請將內建的 `authenticated` 角色新增至 `allowedRoles` 陣列。

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

您可以視需要在 `allowedRoles` 陣列中建立新的角色。 若要將路由限制為僅系統管理員，您可以在 `allowedRoles` 陣列中定義 `administrator` 角色。

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- 您可以完全控制角色名稱；您的角色不一定要遵守主要清單。
- 個別使用者會透過[邀請](authentication-authorization.md)，與角色相關聯。

## <a name="wildcards"></a>萬用字元

萬用字元規則符合指定路由模式下的所有要求。 如果您在規則中定義 `serve` 值，則會將具名檔案或路徑當做回應來提供。

例如，若要實作行事曆應用程式的路由，您可以對應落在 _calendar_ 路由下的所有 URL 以提供單一檔案。

```json
{
  "route": "/calendar/*",
  "serve": "/calendar.html"
}
```

然後，_calendar.html_ 檔案可以使用用戶端路由，針對 URL 變化 (例如 `/calendar/january/1`、`/calendar/2020` 和 `/calendar/overview`) 提供不同的觀點。

您也可以使用萬用字元來保護路由。 在下列範例中，_admin_ 路徑下要求的任何檔案，都必須屬於 _administrator_ 角色成員的已驗證使用者。

```json
{
  "route": "/admin/*",
  "allowedRoles": ["administrator"]
}
```

> [!NOTE]
> 服務檔案所參考之檔案的要求只會針對驗證檢查進行評估。 例如，萬用字元路徑下的 CSS 檔案要求會提供 CSS 檔案，而不是定義為 `serve` 檔案的檔案。 只要使用者符合必要的驗證需求，就會提供 CSS 檔案。

## <a name="fallback-routes"></a>後援路由

前端 JavaScript 架構或程式庫通常依賴 Web 應用程式瀏覽的用戶端路由。 這些用戶端路由規則會更新瀏覽器的視窗位置，而不會向伺服器提出要求。 如果您重新整理頁面，或直接瀏覽至用戶端路由規則所產生的位置，就必須有伺服器端的後援路由，才能提供適當的 HTML 網頁。

常見的後援路由如下列範例所示：

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

後援路由必須列在您路由規則中的最後，因為其會攔截先前所定義規則未攔截到的所有要求。

## <a name="redirects"></a>重新導向

您可以使用 [301](https://en.wikipedia.org/wiki/HTTP_301) 和 [302](https://en.wikipedia.org/wiki/HTTP_302) HTTP 狀態碼，將要求從一個路由重新導向至另一個路由。

例如，下列規則會建立從 _old-page.html_ 至 _new-page.html_的 301 重新導向。

```json
{
  "route": "/old-page.html",
  "serve": "/new-page.html",
  "statusCode": 301
}
```

重新導向也會使用不會定義相異檔案的路徑。

```json
{
  "route": "/about-us",
  "serve": "/about",
  "statusCode": 301
}
```

## <a name="custom-error-pages"></a>自訂錯誤頁面

使用者可能會遇到一些可能導致錯誤的不同情況。 使用 `platformErrorOverrides` 陣列，您可以提供自訂的體驗，以回應這些錯誤。 請參閱[範例路由檔案](#example-route-file)，以在 _routes.json_ 檔案中放置陣列。

下表列出可用的平台錯誤覆寫：

| 錯誤類型  | HTTP 狀態碼 | 描述 |
|---------|---------|---------|
| `NotFound` | 404  | 在伺服器上找不到頁面。 |
| `Unauthenticated` | 401 | 使用者未以[驗證提供者](authentication-authorization.md)登入。 |
| `Unauthorized_InsufficientUserInformation` | 401 | 驗證提供者上的使用者帳戶未設定為公開所需的資料。 當應用程式向驗證提供者詢問使用者的電子郵件地址，但使用者選擇限制電子郵件地址存取權的情況下，就可能會發生此錯誤。 |
| `Unauthorized_InvalidInvitationLink` | 401 | 邀請已過期，或使用者已遵循為另一位收件者產生的邀請連結。  |
| `Unauthorized_MissingRoles` | 401 | 使用者不是所需角色的成員。 |
| `Unauthorized_TooManyUsers` | 401 | 網站已達到使用者的數目上限，且伺服器正限制進一步新增。 會向用戶端公開此錯誤，因為您可以產生的[邀請](authentication-authorization.md)數目沒有限制，且有些使用者可能永遠不接受其邀請。|
| `Unauthorized_Unknown` | 401 | 嘗試驗證使用者時發生未知的問題。 此錯誤的其中一個原因，可能是使用者並未授與同意給應用程式，因而無法辨識使用者。|

## <a name="example-route-file"></a>範例路由檔案

下列範例示範如何在 _routes.json_ 檔案中建置靜態內容和 API 的路由規則。 某些路由會使用 [ _/.auth_ 系統資料夾](authentication-authorization.md)，存取與驗證相關的端點。

```json
{
  "routes": [
    {
      "route": "/profile",
      "allowedRoles": ["authenticated"]
    },
    {
      "route": "/admin/*",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/api/admin",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/customers/contoso",
      "allowedRoles": ["administrator", "customers_contoso"]
    },
    {
      "route": "/login",
      "serve": "/.auth/login/github"
    },
    {
      "route": "/.auth/login/twitter",
      "statusCode": "404"
    },
    {
      "route": "/logout",
      "serve": "/.auth/logout"
    },
    {
      "route": "/calendar/*",
      "serve": "/calendar.html"
    },
    {
      "route": "/specials",
      "serve": "/deals",
      "statusCode": 301
    }
  ],
  "platformErrorOverrides": [
    {
      "errorType": "NotFound",
      "serve": "/custom-404.html"
    },
    {
      "errorType": "Unauthenticated",
      "serve": "/login"
    }
  ]
}
```

下列範例說明當要求符合規則時，會發生什麼事。

|要求...  | 結果... |
|---------|---------|---------|
| _/profile_ | 已驗證的使用者會提供 _/profile/index.html_ 檔案。 未驗證的使用者已重新導向至 _/login_。 |
| _/admin/reports_ | _administrators_角色中的已驗證使用者，會提供 _/admin/reports/index.html_ 檔案。 不在 _administrators_ 角色中的已驗證使用者，會提供 401 錯誤<sup>1</sup>。 未驗證的使用者已重新導向至 _/login_。 |
| _/api/admin_ | 來自 _administrators_ 角色中已驗證使用者的要求會傳送至 API。 不在 _administrators_ 角色中的已驗證使用者和未驗證的使用者，會提供 401 錯誤。 |
| _/customers/contoso_ | 屬於 _administrators_ 或 _customers\_contoso_ 角色的已驗證使用者，會提供 _/customers/contoso/index.html_ 檔案<sup>1</sup>。 不在 _administrators_ 或 _customers\_contoso_ 角色中的已驗證使用者，會提供 401 錯誤。 未驗證的使用者已重新導向至 _/login_。 |
| _/login_     | 未經驗證的使用者會受到向 GitHub 驗證的查問。 |
| _/.auth/login/twitter_     | 已停用 Twitter 的授權。 伺服器回應 404 錯誤。 |
| _/logout_     | 使用者已登出任何驗證提供者。 |
| _/calendar/2020/01_ | 瀏覽器會提供 _/calendar.html_ 檔案。 |
| _/specials_ | 瀏覽器將重新導向至 _/deals_。 |
| _/unknown-folder_     | 提供 _/custom-404.html_ 檔案。 |

<sup>1</sup> 您可以在 `platformErrorOverrides` 陣列中定義 `Unauthorized_MissingRoles` 規則，以提供自訂錯誤網頁。

## <a name="restrictions"></a>限制

- _routes.json_ 檔案不能超過 100 KB
- _routes.json_ 檔案支援最多 50 個不同的角色

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [設定驗證和授權](authentication-authorization.md)
