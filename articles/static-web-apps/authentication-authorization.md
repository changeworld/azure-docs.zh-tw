---
title: Azure 靜態 Web Apps 的驗證和授權
description: 了解如何使用不同的授權提供者來保護您的靜態應用程式。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: e95cd313d341844eabf4f5c5feae8a8ca3dc9c2e
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91826537"
---
# <a name="authentication-and-authorization-for-azure-static-web-apps-preview"></a>Azure 靜態 Web Apps 預覽版的驗證和授權

Azure 靜態 Web Apps 透過下列提供者來管理驗證，從而簡化驗證體驗：

- Azure Active Directory
- GitHub
- Facebook
- Google<sup>1</sup>
- Twitter

提供者特定的[邀請](#invitations)會將使用者與角色產生關聯，且會依 _routes.json_ 檔案中定義的規則授與[路由](routes.md)的存取權給已授權的使用者。

預設會啟用所有驗證提供者。 若要限制驗證提供者，請使用自訂路由規則來[封鎖存取](#block-an-authorization-provider)。

驗證和授權的主題與路由概念明顯重疊。 請務必閱讀[路由指南](routes.md)和這篇文章。

## <a name="roles"></a>角色

每位存取靜態 Web 應用程式的使用者都屬於一或多個角色。  使用者可以屬於兩個內建角色：

- **匿名**：所有使用者都會自動屬於_匿名_角色。
- **已驗證**：所有登入的使用者都屬於_已驗證_角色。

除了內建角色以外，您還可以建立新的角色、透過邀請將其指派給使用者，並在 _routes.json_ 檔案中加以參考。

## <a name="role-management"></a>角色管理

### <a name="add-a-user-to-a-role"></a>將使用者新增至角色

若要將使用者新增至您的網站，您可以產生邀請，讓您將使用者與特定角色建立關聯。 角色會在 _routes.json_ 檔案中定義及維護。

<a name="invitations" id="invitations"></a>

#### <a name="create-an-invitation"></a>建立邀請

邀請專屬於個別的授權提供者，因此當您選取要支援的提供者時，請考慮您的應用程式需求。 有些提供者會公開使用者的電子郵件地址，而有些則只提供網站的使用者名稱。

<a name="provider-user-details" id="provider-user-details"></a>

| 授權提供者 | 公開使用者的  |
| ---------------------- | ----------------- |
| Azure Active Directory | 電子郵件地址     |
| Facebook               | 電子郵件地址     |
| GitHub                 | username          |
| Google<sup>1</sup>     | 電子郵件地址     |
| Twitter                | username          |

1. 在 [Azure 入口網站](https://portal.azure.com)中瀏覽至靜態 Web Apps 資源。
1. 按一下 [設定] 底下的 [角色管理]。
1. 按一下 [邀請] 按鈕。
1. 從選項清單中選取 [授權的提供者]。
1. 將收件者的使用者名稱或電子郵件地址新增至 [邀請詳細資料] 方塊中。
    - 針對 GitHub 和 Twitter，請輸入使用者名稱。 針對所有其他項目，輸入收件者的電子郵件地址。
1. 從 [網域] 下拉式選單中，選取您靜態網站的網域。
    - 您選取的網域是出現在邀請中的網域。 如果您有與網站相關聯的自訂網域，則可能要選擇自訂網域。
1. 在 [角色] 方塊中，新增以逗號分隔的角色名稱清單。
1. 輸入您希望邀請保持有效的最長時數。
    - 可能的最大限制為 168 個小時，也就是 7 天。
1. 按一下 [產生] 按鈕。
1. 從 [邀請連結] 方塊中複製連結。
1. 以電子郵件將邀請連結傳送給您要授與應用程式存取權的人員。

當使用者按一下邀請中的連結時，系統會提示他們使用其對應的帳戶登入。 成功登入之後，使用者會與選取的角色相關聯。

> [!CAUTION]
> 請確定您的路由規則不會與選取的驗證提供者衝突。 使用路由規則封鎖提供者，會讓使用者無法接受邀請。

### <a name="update-role-assignments"></a>更新角色指派

1. 在 [Azure 入口網站](https://portal.azure.com)中瀏覽至靜態 Web Apps 資源。
1. 按一下 [設定] 底下的 [角色管理]。
1. 按一下清單中的使用者。
1. 編輯 [角色] 方塊中的角色清單。
1. 按一下 [更新] 按鈕。

### <a name="remove-user"></a>移除使用者

1. 在 [Azure 入口網站](https://portal.azure.com)中瀏覽至靜態 Web Apps 資源。
1. 按一下 [設定] 底下的 [角色管理]。
1. 在清單中找出使用者。
1. 勾選使用者資料列的核取方塊。
1. 按一下 [刪除]  按鈕。

當您移除使用者時，請記住下列項目：

1. 移除使用者會使其權限失效。
1. 全球傳播可能需要幾分鐘的時間。
1. 如果將使用者新增回應用程式，則 [`userId` 會變更](user-information.md)。

## <a name="remove-personal-identifying-information"></a>移除個人識別資訊

當您以使用者身分對應用程式授與同意時，應用程式可存取您的電子郵件地址或使用者名稱，端視識別提供者而定。 提供這項資訊之後，應用程式的擁有者會決定如何管理個人識別資訊。

終端使用者必須聯絡個別 web 應用程式的系統管理員，才能從他們的系統撤銷此資訊。

若要從 Azure 靜態 Web Apps 平台移除個人識別資訊，並避免平台在未來的要求中提供此資訊，請使用 URL 提交要求：

```url
https://identity.azurestaticapps.net/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

若要避免平台在未來對個別應用程式提出要求時提供這項資訊，請將要求提交至下列 URL：

```url
https://<WEB_APP_DOMAIN_NAME>/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

## <a name="system-folder"></a>系統資料夾

Azure 靜態 Web Apps 會使用 `/.auth` 系統資料夾來提供授權相關 API 的存取權。 請考慮建立[路由規則](routes.md)來建立易記的 URL，而不是直接向使用者公開 `/.auth` 資料夾下的任何路由。

## <a name="login"></a>登入

使用下表來尋找提供者特定的登入路由。

| 授權提供者 | 登入路由             |
| ---------------------- | ----------------------- |
| Azure Active Directory | `/.auth/login/aad`      |
| Facebook               | `/.auth/login/facebook` |
| GitHub                 | `/.auth/login/github`   |
| Google<sup>1</sup>     | `/.auth/login/google`   |
| Twitter                | `/.auth/login/twitter`  |

例如，若要使用 GitHub 登入，您可以包含如下列程式碼片段的登入連結：

```html
<a href="/.auth/login/github">Login</a>
```

如果您選擇支援多個提供者，則需要在您的網站上公開每個提供者特定的連結。

您可以使用[路由規則](routes.md)，將預設提供者對應到易記的路由，例如 _/login_。

```json
{
  "route": "/login",
  "serve": "/.auth/login/github"
}
```

### <a name="post-login-redirect"></a>登入後重新導向

如果您希望使用者在登入之後返回特定頁面，請在 `post_login_redirect_uri` 查詢字串參數中提供 URL。


## <a name="logout"></a>Logout

`/.auth/logout` 路由會將使用者從網站登出。 您可以新增網站導覽的連結來讓使用者登出，如下列範例所示。

```html
<a href="/.auth/logout">Log out</a>
```

您可以使用[路由規則](routes.md)來對應易記的路由，例如 _/logout_。

```json
{
  "route": "/logout",
  "serve": "/.auth/logout"
}
```

### <a name="post-logout-redirect"></a>登出後重新導向

如果您希望使用者在登出之後返回特定頁面，請在 `post_logout_redirect_uri` 查詢字串參數中提供 URL。

## <a name="block-an-authorization-provider"></a>封鎖授權提供者

您可能要限制應用程式使用授權提供者。 例如，您的應用程式可能只要在[公開電子郵件地址的提供者](#provider-user-details)上進行標準化。

若要封鎖提供者，您可以建立[路由規則](routes.md)，針對已封鎖提供者特定路由的要求傳回 404。 例如，若要將 Twitter 限制為提供者，請新增下列路由規則。

```json
{
  "route": "/.auth/login/twitter",
  "statusCode": "404"
}
```

## <a name="restrictions"></a>限制

如需一般限制和限制，請參閱 [配額文章](quotas.md) 。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [存取使用者驗證和授權資料](user-information.md)

<sup>1</sup> 擱置的外部認證。
