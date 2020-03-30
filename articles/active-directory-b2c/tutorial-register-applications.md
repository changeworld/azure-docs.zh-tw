---
title: 教程：註冊應用程式
titleSuffix: Azure AD B2C
description: 了解如何使用 Azure 入口網站在 Azure Active Directory B2C 中註冊 Web 應用程式。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a688f5e75f7513d0ea4308b751f87f75a2c9510a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183086"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>教程：在 Azure 活動目錄 B2C 中註冊應用程式

在[應用程式](application-types.md)可以與 Azure 活動目錄 B2C（Azure AD B2C）交互之前，它們必須註冊到您管理的租戶中。 本教學課程示範如何使用 Azure 入口網站註冊 Web 應用程式。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 註冊 Web 應用程式
> * 建立用戶端密碼

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

如果尚未創建自己的[Azure AD B2C 租戶](tutorial-create-tenant.md)，請立即創建一個。 您可以使用現有的 Azure AD B2C 租用戶。

## <a name="register-a-web-application"></a>註冊 Web 應用程式

若要在您的 Azure AD B2C 租用戶中註冊應用程式，您可以使用目前的**應用程式**體驗，或使用新整合的**應用程式註冊 (預覽)** 體驗。 [深入了解新的體驗](https://aka.ms/b2cappregintro)。

#### <a name="applications"></a>[應用程式](#tab/applications/)

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 在門戶工具列中選擇**目錄 + 訂閱**圖示，然後選擇包含 Azure AD B2C 租戶的目錄。
1. 在 Azure 門戶中，搜索並選擇**Azure AD B2C**。
1. 選擇 **"應用程式**"，然後選擇 **"添加**"。
1. 輸入應用程式的名稱。 例如，*webapp1*。
1. 針對 [包含 Web 應用程式/Web API]**** 和 [允許隱含流程]****，選取 [是]****。
1. 針對**回覆 URL**，請輸入 Azure AD B2C 應傳回您的應用程式所要求任何權杖的端點。 例如，您可以將其設置為在 本地偵聽`https://localhost:44316`。 如果還不知道連接埠號碼，您可以輸入預留位置值，之後再變更。

    出於測試目的（如本教程），您可以將其`https://jwt.ms`設置為顯示要檢查的權杖的內容。 對於本教程，將 **"答覆"URL** `https://jwt.ms`設置為 。

    以下限制適用于回復 URL：

    * 答覆 URL 必須以方案`https`開頭。
    * 答覆 URL 區分大小寫。 其大小寫必須與正在運行的應用程式的 URL 路徑的情況匹配。 例如，如果應用程式包含作為其路徑`.../abc/response-oidc`的一部分，請不要在答覆 URL`.../ABC/response-oidc`中指定。 由於 Web 瀏覽器將路徑視為區分大小寫的，因此，`.../abc/response-oidc`如果重定向到案例不匹配`.../ABC/response-oidc`的 URL，可能會排除與路徑關聯的 Cookie。

1. 選擇 **"創建**"以完成應用程式註冊。

#### <a name="app-registrations-preview"></a>[應用程式註冊 (預覽)](#tab/app-reg-preview/)

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 在門戶工具列中選擇**目錄 + 訂閱**圖示，然後選擇包含 Azure AD B2C 租戶的目錄。
1. 在 Azure 門戶中，搜索並選擇**Azure AD B2C**。
1. 選取 [應用程式註冊 (預覽)]****，然後選取 [新增註冊]****。
1. 輸入應用程式的 [名稱]****。 例如，*webapp1*。
1. 選擇**任何組織目錄中的帳戶或任何標識提供程式**。
1. 在**重定向 URI**下，選擇**Web** `https://jwt.ms` ，然後在 URL 文字方塊中輸入。

    重定向 URI 是授權伺服器（本例中為 Azure AD B2C）將使用者發送到的終結點，並在成功授權後向其發送訪問權杖或授權代碼。 在生產應用程式中，它通常是應用運行的公開訪問終結點，如`https://contoso.com/auth-response`。 出於測試目的（如本教程），您可以將其設置為`https://jwt.ms`，Microsoft 擁有的 Web 應用程式顯示權杖的解碼內容（權杖的內容永遠不會離開瀏覽器）。 在應用開發期間，您可以添加應用程式在本地偵聽的終結點，如`https://localhost:5000`。 您可以隨時在已註冊的應用程式中添加和修改重定向 URI。

    以下限制適用于重定向 URI：

    * 答覆 URL 必須以方案`https`開頭。
    * 答覆 URL 區分大小寫。 其大小寫必須與正在運行的應用程式的 URL 路徑的情況匹配。 例如，如果應用程式包含作為其路徑`.../abc/response-oidc`的一部分，請不要在答覆 URL`.../ABC/response-oidc`中指定。 由於 Web 瀏覽器將路徑視為區分大小寫的，因此，`.../abc/response-oidc`如果重定向到案例不匹配`.../ABC/response-oidc`的 URL，可能會排除與路徑關聯的 Cookie。

1. 在 [權限]**** 下，選取 [對 openid 與 offline_access 權限授與管理員同意]** 核取方塊。
1. 選取 [註冊]****。

完成應用程式註冊後，啟用隱式授予流：

1. 在 [管理]**** 底下，選取 [驗證]****。
1. 選取 [試用全新體驗]**** (若顯示的話)。
1. 在**隱式授予**下，選擇 **"訪問權杖**"和 **"ID 權杖"** 核取方塊。
1. 選取 [儲存]****。

* * *

## <a name="create-a-client-secret"></a>建立用戶端密碼

如果應用程式為訪問權杖交換授權代碼，則需要創建應用程式金鑰。

#### <a name="applications"></a>[應用程式](#tab/applications/)

1. 在**Azure AD B2C - 應用程式**頁中，選擇您創建的應用程式，例如*Webapp1*。
1. 選擇**鍵**，然後選擇 **"生成鍵**"。
1. 選取 [儲存]**** 以檢視金鑰。 請記下 [應用程式金鑰]**** 值。 在應用程式代碼中使用此值作為應用程式機密。

#### <a name="app-registrations-preview"></a>[應用程式註冊 (預覽)](#tab/app-reg-preview/)

1. 在**Azure AD B2C - 應用註冊（預覽）** 頁中，選擇您創建的應用程式，例如*Webapp1*。
1. 在 [管理]**** 下，選取 [憑證和密碼]****。
1. 選取 [新增用戶端密碼]****。
1. 在 [描述]**** 方塊中，輸入用戶端密碼的描述。 例如，*clientsecret1*。
1. 在 [到期]**** 下，選取密碼有效的持續時間，然後選取 [新增]****。
1. 記錄密碼的**值**。 在應用程式代碼中使用此值作為應用程式機密。

* * *

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何：

> [!div class="checklist"]
> * 註冊 Web 應用程式
> * 建立用戶端密碼

接下來，瞭解如何創建使用者流，使使用者能夠註冊、登錄和管理其設定檔。

> [!div class="nextstepaction"]
> [在 Azure 活動目錄 B2C >中創建使用者流](tutorial-create-user-flows.md)
