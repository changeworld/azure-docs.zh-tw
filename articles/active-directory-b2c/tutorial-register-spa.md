---
title: 教學課程：註冊單頁應用程式
titleSuffix: Azure AD B2C
description: 請遵循本教學課程以了解如何使用 Azure 入口網站在 Azure Active Directory B2C 中註冊單頁應用程式 (SPA)。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fc01fb4296226126b996840109d3bb305b042364
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92275802"
---
# <a name="tutorial-register-a-single-page-application-spa-in-azure-active-directory-b2c"></a>教學課程：在 Azure Active Directory B2C 中註冊單頁應用程式 (SPA)

[應用程式](application-types.md)必須先在您管理的租用戶中完成註冊，才可以與 Azure Active Directory B2C (Azure AD B2C) 互動。 本教學課程示範如何使用 Azure 入口網站註冊單頁應用程式 ("SPA")。

## <a name="overview-of-authentication-options"></a>驗證選項的概觀

許多新式 Web 應用程式都是以用戶端單頁應用程式 ("SPA") 的形式建立。 開發人員可以使用 JavaScript 或 SPA 架構 (例如 Angular、Vue 和 React) 來撰寫這些應用程式。 這些應用程式會在網頁瀏覽器上執行，且具有與傳統伺服器端 Web 應用程式不同的驗證特性。

Azure AD B2C 提供 **兩個** 選項，讓單頁應用程式可以登入使用者，並取得權杖以存取後端服務或 Web API：

### <a name="authorization-code-flow-with-pkce"></a>授權碼流程 (使用 PKCE)
- [OAuth 2.0 授權碼流程 (使用 PKCE)](./authorization-code-flow.md)。 授權碼流程可讓應用程式針對代表已驗證使用者的 **識別碼** 權杖，以及呼叫受保護 API 所需的 **存取** 權杖，交換授權碼。 此外，其會傳回 **重新整理** 權杖，以代表使用者提供長期的資源存取權，而不需要與使用者互動。 

這是 **建議** 的方法。 具備有限存留期的重新整理權杖可協助您的應用程式適應[新式瀏覽器 Cookie 隱私權限制](../active-directory/develop/reference-third-party-cookies-spas.md)，例如 Safari ITP。

若要利用此流程，您的應用程式可以使用支援此流程的驗證程式庫，例如 [MSAL.js 2.x](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa)。 

![Single-page applications-auth](./media/tutorial-single-page-app/spa-app-auth.svg)

### <a name="implicit-grant-flow"></a>隱含授與流程
- [OAuth 2.0 隱含流程](implicit-flow-single-page-application.md)。 有些架構 (例如 [MSAL.js 1.x](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)) 只支援隱含授與流程。 隱含授與流程可讓應用程式取得 **識別碼** 及 **存取** 權杖。 不同於授權碼流程，隱含授與流程不會傳回 **重新整理權杖** 。 

![Single-page applications-implicit](./media/tutorial-single-page-app/spa-app.svg)

此驗證流程不包含使用跨平台 JavaScript 架構 (例如 Electron 和 React-Native) 的應用程式案例。 這些案例需要進一步的功能來與原生平台互動。

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

如果您尚未建立自己的 [Azure AD B2C 租用戶](tutorial-create-tenant.md)，請立即建立一個。 您可以使用現有的 Azure AD B2C 租用戶。

## <a name="register-the-spa-application"></a>註冊 SPA 應用程式

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在入口網站工具列中選取 **目錄 + 訂用帳戶** 圖示，然後選取包含 Azure AD B2C 租用戶的目錄。
1. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。
1. 選取 [應用程式註冊]，然後選取 [新增註冊]。
1. 輸入應用程式的 [名稱]。 例如，spaapp1。
1. 在 [支援的帳戶類型] 中，選取 [任何識別提供者或組織目錄中的帳戶 (適用於以使用者流程驗證使用者)]
1. 在 [重新導向 URI] 底下，選取 [單頁應用程式 (SPA)]，然後在 URL 文字方塊中輸入 `https://jwt.ms`。

    重新導向 URI 是由授權伺服器 (在此案例中為 Azure AD B2C) 在完成與使用者的互動之後傳送給使用者的端點，以及在成功授權時傳送存取權杖或授權碼的端點。 在實際執行應用程式中，其通常是您應用程式執行所在之可公開存取的端點，例如 `https://contoso.com/auth-response`。 基於類似此教學課程的測試目的，您可以將其設定為 `https://jwt.ms`，這是 Microsoft 擁有的 Web 應用程式，會顯示已解碼的權杖內容 (權杖內容永遠不會離開您的瀏覽器)。 在應用程式開發期間，您可以新增應用程式在本機接聽的端點，例如 `http://localhost:5000`。 您可以隨時在已註冊的應用程式中新增及修改重新導向 URI。

    下列限制會套用至重新導向 URI：

    * 回覆 URL 的開頭必須是 `https` 配置 (除非使用 `localhost`)。
    * 回覆 URL 會區分大小寫。 其大小寫必須符合您執行中應用程式之 URL 路徑的大小寫。 例如，如果您的應用程式包含作為其路徑 `.../abc/response-oidc` 的一部分，請勿在回覆 URL 中指定 `.../ABC/response-oidc`。 由於網頁瀏覽器會將路徑視為區分大小寫，因此，如果將與 `.../abc/response-oidc` 相關聯的 Cookie 重新導向至不相符的 `.../ABC/response-oidc` URL，可能就會予以排除。

1. 在 [權限] 下，選取 [對 openid 與 offline_access 權限授與管理員同意] 核取方塊。
1. 選取 [註冊]。


## <a name="enable-the-implicit-flow"></a>啟用隱含流程
如果使用隱含流程，您必須在應用程式註冊中啟用隱含授與流程。

1. 在左側功能表的 [管理]下，選取 [驗證]。
1. 在 [隱含授與] 底下，同時選取 [存取權杖] 和 [識別碼權杖] 核取方塊。
1. 選取 [儲存]。

## <a name="migrate-from-the-implicit-flow"></a>從隱含流程遷移

如果您有使用隱含流程的現有應用程式，我們建議您使用支援的架構 (例如 [MSAL.js 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser))，將其遷移為使用授權碼流程。

當應用程式註冊所代表的所有生產單一頁面應用程式都使用授權碼流程時，請停用隱含授與流程設定。 

1. 在左側功能表的 [管理]下，選取 [驗證]。
1. 在 [隱含授與] 底下，取消選取 [存取權杖] 和 [識別碼權杖] 核取方塊。
1. 選取 [儲存]。

如果您保持啟用隱含流程 (已核取)，使用隱含流程的應用程式仍可繼續運作。

* * *

## <a name="next-steps"></a>後續步驟

接下來，將了解如何建立使用者流程，讓您的使用者能夠註冊、登入及管理其設定檔。

> [!div class="nextstepaction"]
> [在 Azure Active Directory B2C 中建立使用者流程 >](tutorial-create-user-flows.md)
