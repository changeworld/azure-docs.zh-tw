---
title: 註冊單頁應用程式 (SPA) | Azure
titleSuffix: Microsoft identity platform
description: 了解如何建置單頁應用程式 (應用程式註冊)
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: hahamil
ms.custom: aaddev
ms.openlocfilehash: 238bcc6c706f2fab969c98b73ca879d064498693
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026504"
---
# <a name="single-page-application-app-registration"></a>單頁應用程式：應用程式註冊

若要在 Microsoft 身分識別平台中註冊單頁應用程式 (SPA)，請完成下列步驟。 註冊步驟在 MSAL.js 1.0 (支援隱含授與流程) 和 MSAL.js 2.0 (支援使用 PKCE 的授權碼流程) 之間有所不同。

## <a name="create-the-app-registration"></a>建立應用程式註冊

針對以 MSAL.js 1.0 和 2.0 為基礎的應用程式，請先完成下列步驟，以建立初始應用程式註冊。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 如果您的帳戶可存取多個租用戶，請選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選條件，然後選取應包含您即將建立之應用程式註冊的租用戶。
1. 搜尋並選取 [Azure Active Directory]。
1. 在 [管理] 底下選取 [應用程式註冊]。
1. 選取 [新增註冊]，輸入應用程式的 [名稱]，然後選擇應用程式 [支援的帳戶類型]。 請**不要**輸入**重新導向 URI**。 如需不同帳戶類型的說明，請參閱[使用 Azure 入口網站註冊新的應用程式](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)。
1. 選取 [註冊] 以建立應用程式註冊。

接下來，使用**重新導向 URI** 設定應用程式註冊，以指定 Microsoft 身分識別平台應將用戶端與任何安全性權杖一起重新導向的目標位置。 使用適用於您應用程式中所使用之 MSAL.js 版本的步驟：

- [具有驗證碼流程的 MSAL.js 2.0](#redirect-uri-msaljs-20-with-auth-code-flow) \(建議\)
- [具有隱含流程的 MSAL.js 1.0](#redirect-uri-msaljs-10-with-implicit-flow)

## <a name="redirect-uri-msaljs-20-with-auth-code-flow"></a>重新導向 URI： [MSAL.js 2.0 與驗證碼流程](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)

請遵循這些步驟，為使用 MSAL.js 2.0 或更新版本的應用程式新增重新導向 URI。 MSAL.js 2.0+ 支援使用 PKCE 和 CORS 的授權碼流程，以回應[瀏覽器協力廠商 Cookie 限制](reference-third-party-cookies-spas.md)。 MSAL.js 2.0+ 不支援隱含授與流程。

1. 在 Azure 入口網站中，選取您稍早在[建立應用程式註冊](#create-the-app-registration)中建立的應用程式註冊。
1. 在 [管理] 底下，選取 [驗證]，然後選取 [新增平台]。
1. 在 [Web 應用程式] 底下，選取 [單頁應用程式] 圖格。
1. 在 [重新導向 URI] 底下，輸入[重新導向 URI](reply-url.md)。 請**不要**選取 [隱含授與] 底下的任一核取方塊。
1. 選取 [設定] 以完成新增重新導向 URI。

您現在已完成單頁應用程式 (SPA) 的註冊，並已設定重新導向 URI；系統會將用戶端重新導向到該 URI，也會將任何安全性權杖傳送到該 URI。 透過使用 [新增平台] 窗格中的 [單頁應用程式] 圖格來設定您的重新導向 URI，您的應用程式註冊已設定為支援使用 PKCE 和 CORS 的授權碼流程。

遵循[教學](tutorial-v2-javascript-auth-code.md)課程以取得進一步的指引。

## <a name="redirect-uri-msaljs-10-with-implicit-flow"></a>重新導向 URI： [MSAL.js 1.0 與隱含流程](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)

請遵循這些步驟，針對使用 MSAL.js 1.3 或更早版本及隱含授與流程的單頁應用程式新增重新導向 URI。 使用 MSAL.js 1.3 或更早版本的應用程式不支援驗證碼流程。

1. 在 Azure 入口網站中，選取您稍早在[建立應用程式註冊](#create-the-app-registration)中建立的應用程式註冊。
1. 在 [管理] 底下，選取 [驗證]，然後選取 [新增平台]。
1. 在 [Web 應用程式] 底下，選取 [單頁應用程式] 圖格。
1. 在 [重新導向 URI] 底下，輸入[重新導向 URI](reply-url.md)。
1. 啟用**隱含流程**：
    - 如果您的應用程式會登入使用者，請選取 [識別碼權杖]。
    - 如果您的應用程式也需要呼叫受保護的 Web API，請選取 [存取權杖]。 如需有關這些權杖類型的詳細資訊，請參閱[識別碼權杖](id-tokens.md)與[存取權杖](access-tokens.md)。
1. 選取 [設定] 以完成新增重新導向 URI。

您現在已完成單頁應用程式 (SPA) 的註冊，並已設定重新導向 URI；系統會將用戶端重新導向到該 URI，也會將任何安全性權杖傳送到該 URI。 藉由個別或同時選取 [識別碼權杖] 和 [存取權杖]，您已啟用隱含授與流程。

遵循[教學](tutorial-v2-javascript-spa.md)課程以取得進一步的指引。 

## <a name="note-about-authorization-flows"></a>授權流程的相關注意事項

根據預設，使用單頁應用程式平台設定所建立的應用程式註冊，會啟用授權碼流程。 若要利用此流程，應用程式必須使用 MSAL.js 2.0 或更新版本。

如先前所述，使用 MSAL.js 1.3 的單頁應用程式僅限使用隱含授與流程。 目前的 [OAuth 2.0 最佳做法](v2-oauth2-auth-code-flow.md)建議針對 SPA 使用授權碼流程，而不是隱含流程。 具備有限存留期的重新整理權杖也可協助您的應用程式適應[新式瀏覽器 Cookie 隱私權限制](reference-third-party-cookies-spas.md)，例如 Safari ITP。

當應用程式註冊所代表的所有生產單頁應用程式都使用 MSAL.js 2.0 和授權碼流程時，請取消選取 Azure 入口網站中應用程式註冊之 [驗證] 窗格的隱含授與設定。 不過，如果您保持啟用隱含流程 (已核取)，使用 MSAL.js 1.x 和隱含流程的應用程式仍可繼續運作。

## <a name="next-steps"></a>後續步驟

接下來，設定應用程式的程式碼，以使用您在先前步驟中建立的應用程式註冊：

> [!div class="nextstepaction"]
> [應用程式的程式碼設定](scenario-spa-app-configuration.md)
