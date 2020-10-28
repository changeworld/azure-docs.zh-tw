---
title: 教學課程：註冊應用程式
titleSuffix: Azure AD B2C
description: 請遵循本教學課程以了解如何使用 Azure 入口網站在 Azure Active Directory B2C 中註冊 Web 應用程式。
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
ms.openlocfilehash: d38f10e5a4f2562825ed2374317602b0640894ae
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92275882"
---
# <a name="tutorial-register-a-web-application-in-azure-active-directory-b2c"></a>教學課程：在 Azure Active Directory B2C 中註冊 Web 應用程式

[應用程式](application-types.md)必須先在您管理的租用戶中完成註冊，才可以與 Azure Active Directory B2C (Azure AD B2C) 互動。 本教學課程示範如何使用 Azure 入口網站註冊 Web 應用程式。 

「Web 應用程式」是指在伺服器上執行大部分應用程式邏輯的傳統 Web 應用程式。 您可使用 ASP.NET Core、Maven (Java)、Flask (Python) 和 Express (Node.js) 之類的架構來建置這類應用程式。

> [!IMPORTANT]
> 如果您改為使用單頁應用程式 (SPA) (例如使用 Angular、Vue 或 React)，請了解[如何註冊單頁應用程式](tutorial-register-spa.md)。
> 
> 如果您改為使用原生應用程式 (例如 iOS、Android、行動裝置和桌上型電腦)，請了解[如何註冊原生用戶端應用程式](add-native-application.md)。

## <a name="prerequisites"></a>必要條件
如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

如果您尚未建立自己的 [Azure AD B2C 租用戶](tutorial-create-tenant.md)，請立即建立一個。 您可以使用現有的 Azure AD B2C 租用戶。

## <a name="register-a-web-application"></a>註冊 Web 應用程式

若要在您的 Azure AD B2C 租用戶中註冊 Web 應用程式，您可以使用我們新的整合 **應用程式註冊** 體驗，或使用舊版 **應用程式 (舊版)** 體驗。 [深入了解新的體驗](https://aka.ms/b2cappregtraining)。

#### <a name="app-registrations"></a>[應用程式註冊](#tab/app-reg-ga/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在入口網站工具列中選取 **目錄 + 訂用帳戶** 圖示，然後選取包含 Azure AD B2C 租用戶的目錄。
1. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。
1. 選取 [應用程式註冊]，然後選取 [新增註冊]。
1. 輸入應用程式的 [名稱]。 例如， *webapp1* 。
1. 在 [支援的帳戶類型] 中，選取 [任何識別提供者或組織目錄中的帳戶 (適用於以使用者流程驗證使用者)]。
1. 在 [重新導向 URI] 底下，選取 [Web]，然後在 URL 文字方塊中輸入 `https://jwt.ms`。

    重新導向 URI 是由授權伺服器 (在此案例中為 Azure AD B2C) 在完成與使用者的互動之後傳送給使用者的端點，以及在成功授權時傳送存取權杖或授權碼的端點。 在實際執行應用程式中，其通常是您應用程式執行所在之可公開存取的端點，例如 `https://contoso.com/auth-response`。 基於類似此教學課程的測試目的，您可以將其設定為 `https://jwt.ms`，這是 Microsoft 擁有的 Web 應用程式，會顯示已解碼的權杖內容 (權杖內容永遠不會離開您的瀏覽器)。 在應用程式開發期間，您可以新增應用程式在本機接聽的端點，例如 `https://localhost:5000`。 您可以隨時在已註冊的應用程式中新增及修改重新導向 URI。

    下列限制會套用至重新導向 URI：

    * 回覆 URL 的開頭必須是配置 `https`。
    * 回覆 URL 會區分大小寫。 其大小寫必須符合您執行中應用程式之 URL 路徑的大小寫。 例如，如果您的應用程式包含作為其路徑 `.../abc/response-oidc` 的一部分，請勿在回覆 URL 中指定 `.../ABC/response-oidc`。 由於網頁瀏覽器會將路徑視為區分大小寫，因此，如果將與 `.../abc/response-oidc` 相關聯的 Cookie 重新導向至不相符的 `.../ABC/response-oidc` URL，可能就會予以排除。

1. 在 [權限] 下，選取 [對 openid 與 offline_access 權限授與管理員同意] 核取方塊。
1. 選取 [註冊]。

#### <a name="applications-legacy"></a>[應用程式 (舊版)](#tab/applications-legacy/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在入口網站工具列中選取 **目錄 + 訂用帳戶** 圖示，然後選取包含 Azure AD B2C 租用戶的目錄。
1. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。
1. 選取 [應用程式 (舊版)]，然後選取 [新增]。
1. 輸入應用程式的名稱。 例如， *webapp1* 。
1. 針對 [包含 Web 應用程式/Web API]，選取 [是]。
1. 針對 **回覆 URL** ，請輸入 Azure AD B2C 應傳回您的應用程式所要求任何權杖的端點。 例如，您可以將其設定為在 `http://localhost:5000` 進行本機接聽。 您可以隨時在已註冊的應用程式中新增及修改重新導向 URI。

    下列限制會套用至重新導向 URI：

    * 回覆 URL 的開頭必須是 `https` 配置 (除非使用 `localhost`)。
    * 回覆 URL 會區分大小寫。 其大小寫必須符合您執行中應用程式之 URL 路徑的大小寫。 例如，如果您的應用程式包含作為其路徑 `.../abc/response-oidc` 的一部分，請勿在回覆 URL 中指定 `.../ABC/response-oidc`。 由於網頁瀏覽器會將路徑視為區分大小寫，因此，如果將與 `.../abc/response-oidc` 相關聯的 Cookie 重新導向至不相符的 `.../ABC/response-oidc` URL，可能就會予以排除。

1. 選取 [建立] 以完成應用程式註冊。

* * *

## <a name="create-a-client-secret"></a>建立用戶端密碼

針對 Web 應用程式，您必須建立應用程式秘密。 您的應用程式將使用此秘密來以授權碼交換存取權杖。

#### <a name="app-registrations"></a>[應用程式註冊](#tab/app-reg-ga/)

1. 在 [Azure AD B2C - 應用程式註冊] 頁面中，選取您建立的應用程式，例如 *webapp1* 。
1. 在左側功能表的 [管理] 下，選取 [驗證和密碼]。
1. 選取 [新增用戶端密碼]。
1. 在 [描述] 方塊中，輸入用戶端密碼的描述。 例如， *clientsecret1* 。
1. 在 [到期] 下，選取密碼有效的持續時間，然後選取 [新增]。
1. 記錄密碼的 **值** 。 您可以使用此值，作為應用程式程式碼中的應用程式密碼。

#### <a name="applications-legacy"></a>[應用程式 (舊版)](#tab/applications-legacy/)

1. 在 [Azure AD B2C - 應用程式] 頁面中，選取您建立的應用程式，例如 *webapp1* 。
1. 選取 [金鑰]，然後選取 [產生金鑰]。
1. 選取 [儲存] 以檢視金鑰。 請記下 [應用程式金鑰] 值。 您可以使用此值，作為應用程式程式碼中的應用程式密碼。

* * *

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何：

> [!div class="checklist"]
> * 註冊 Web 應用程式
> * 建立用戶端密碼

接下來，將了解如何建立使用者流程，讓您的使用者能夠註冊、登入及管理其設定檔。

> [!div class="nextstepaction"]
> [在 Azure Active Directory B2C 中建立使用者流程 >](tutorial-create-user-flows.md)
