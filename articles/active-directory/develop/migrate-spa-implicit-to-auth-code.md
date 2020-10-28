---
title: 將 JavaScript 單一頁面應用程式從隱含授與遷移至授權碼流程 | Azure
titleSuffix: Microsoft identity platform
description: 如何將使用 MSAL.js 1.x 和隱含授與流程的 JavaScript SPA 更新為 MSAL.js 2.x 和使用 PKCE 和 CORS 支援的授權碼流程。
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 05258e201c65138e53e861f0631eb33e08c9c199
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92673586"
---
# <a name="migrate-a-javascript-single-page-app-from-implicit-grant-to-auth-code-flow"></a>將 JavaScript 單一頁面應用程式從隱含授與遷移至授權碼流程

適用於 JavaScript 的 Microsoft 驗證程式庫 (MSAL) v2.0 提供授權碼流程的支援，其中包含在 Microsoft 身分識別平台上單一頁面應用程式的 PKCE 和 CORS。 遵循下列各節中的步驟，將您使用隱含授與的 MSAL.js 1.x 應用程式遷移至 MSAL.js 2.0 以上版本 (以下稱為 *2.x* ) 和驗證碼流程。

MSAL.js 2.x 改良了 MSAL.js 1.x，可在瀏覽器中支援授權碼流程，而非隱含授與流程。 MSAL.js 2.x **不** 支援隱含流程。

## <a name="migration-steps"></a>移轉步驟

若要將您的應用程式更新為 MSAL.js 2.x 和驗證碼流程，有三個主要步驟：

1. 將您的 [應用程式註冊](#switch-redirect-uris-to-spa-platform)重新導向 URI 從 **Web** 平台切換成 **單一頁面應用程式** 平台。
1. 將您的 [程式碼](#switch-redirect-uris-to-spa-platform)從 MSAL.js 1.x 更新為 **2.x** 。
1. 當共用註冊的所有應用程式都已更新為 MSAL.js 2.x 和驗證碼流程時，請停用應用程式註冊中的[隱含授與](#disable-implicit-grant-settings)。

下列各節將詳細說明每個步驟。

## <a name="switch-redirect-uris-to-spa-platform"></a>將重新導向 URI 切換為 SPA 平台

如果您想要繼續對您的應用程式使用現有的應用程式註冊，請使用 Azure 入口網站將註冊的重新導向 URI 更新為 SPA 平台。 這麼做可讓使用 PKCE 和 CORS 的授權碼流程支援使用註冊的應用程式 (您仍需要將您的應用程式的程式碼更新為 MSAL.js v2.x)。

針對目前設定使用 **Web** 平台重新導向 URI 的應用程式註冊，遵循下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後選取您的 **Azure Active Directory** 租用戶。
1. 在 [應用程式註冊] 中，選取您的應用程式，然後選取 [驗證]。
1. 在 **Web** 平台磚的 [重新導向URI] 底下，選取指出您應該遷移 URI 的警告橫幅。

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-01-implicit-warning-banner.png" alt-text="Azure 入口網站中 Web 應用程式磚上的隱含流程警告橫幅":::
1. *僅* 選取其應用程式將使用 MSAL.js 2.x 的重新導向 URI，然後選取 [設定]。

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-02-select-redirect-uri.png" alt-text="Azure 入口網站中 Web 應用程式磚上的隱含流程警告橫幅":::

這些重新導向 URI 現在應該會出現在 [單一頁面應用程式] 平台磚中，顯示具有授權碼流程的 CORS 支援，以及已針對這些 URI 啟用 PKCE。

:::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-03-spa-redirect-uri-tile.png" alt-text="Azure 入口網站中 Web 應用程式磚上的隱含流程警告橫幅":::

您也可以[建立新的應用程式註冊](scenario-spa-app-registration.md)，而非更新現有註冊中的重新導向 URI。

## <a name="update-your-code-to-msaljs-2x"></a>將您的程式碼更新為 MSAL.js 2.x

在 MSAL 1.x 中，您已藉由初始化 [UserAgentApplication][msal-js-useragentapplication] 來建立應用程式執行個體，如下所示：

```javascript
// MSAL 1.x
import * as msal from "msal";

const msalInstance = new msal.UserAgentApplication(config);
```

在 MSAL 2.x 中，改為初始化 [PublicClientApplication][msal-js-publicclientapplication]：

```javascript
// MSAL 2.x
import * as msal from "@azure/msal-browser";

const msalInstance = new msal.PublicClientApplication(config);
```

如需將 MSAL 2.x 新增至您的應用程式的完整逐步解說，請參閱[教學課程：使用授權碼流程來登入使用者，並從 JavaScript 單一頁面應用程式 (SPA) 呼叫 Microsoft Graph API](tutorial-v2-javascript-auth-code.md)。

如需您可能需要對程式碼進行的其他變更，請參閱 GitHub 上的[遷移指南](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/v1-migration.md)。

## <a name="disable-implicit-grant-settings"></a>停用隱含授與設定

將使用此應用程式註冊的所有生產應用程式和其用戶端識別碼更新至 MSAL 2.x 和授權碼流程之後，您應該在應用程式註冊中取消核取隱含授與設定。

當您在應用程式註冊中取消核取隱含授與設定時，會停用使用註冊和其用戶端識別碼的所有應用程式的隱含流程。

在您將所有應用程式更新至 MSAL.js 2.x 和 [PublicClientApplication][msal-js-publicclientapplication] 之前， **請勿** 停用隱含授與流程。

## <a name="next-steps"></a>後續步驟

若要深入了解授權碼流程，包括隱含流程和授權碼流程之間的差異，請參閱 [Microsoft 身分識別平台和 OAuth 2.0 授權碼流程](v2-oauth2-auth-code-flow.md)。

如果您想要深入了解如何在 Microsoft 身分識別平台上開發 JavaScript 單頁應用程式，有多個部分的[案例：單頁應用程式](scenario-spa-overview.md)系列文章可協助您開始著手。

<!-- LINKS - external -->
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/modules/_useragentapplication_.html
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/classes/_src_app_publicclientapplication_.publicclientapplication.html
