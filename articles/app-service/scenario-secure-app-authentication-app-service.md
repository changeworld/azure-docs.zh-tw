---
title: 教學課程 - 在 Azure App Service 的 Web 應用程式中新增驗證 | Azure
description: 在本教學課程中，您將了解如何針對在 Azure App Service 上執行的 Web 應用程式啟用驗證和授權。  限制組織中的使用者存取 Web 應用程式的權限。
services: active-directory, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: a1b2809371a01b1c6fd822e5c4aaa197d0de3c1b
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428204"
---
# <a name="tutorial-add-authentication-to-your-web-app-running-on-azure-app-service"></a>教學課程：將驗證新增至在 Azure App Service 上執行的 Web 應用程式

了解如何針對在 Azure App Service 上執行的 Web 應用程式啟用驗證，並限制存取組織中的使用者。

:::image type="content" source="./media/scenario-secure-app-authentication-app-service/web-app-sign-in.svg" alt-text="登入使用者" border="false":::

Azure App Service 提供內建的驗證和授權支援，因此您在 Web 應用程式中幾乎不需要寫入或完全無需寫入程式碼，即可登入使用者及存取資料。  不需要使用 App Service 驗證/授權模組，但有助於簡化應用程式的驗證和授權。 本文說明如何使用 Azure Active Directory 作為身分識別提供者，來保護您的 Web 應用程式與 App Service 驗證/授權模組。

驗證/授權模組會透過 Azure 入口網站和應用程式設定來啟用和設定。 不需要任何 SDK、特定程式語言或變更應用程式程式碼。 支援各種不同的識別提供者，包括：Azure AD、Microsoft 帳戶、Facebook、Google 及 Twitter。 驗證/授權模組啟用時，每個連入的 HTTP 要求會先通過此模組，再由應用程式程式碼處理。  若要深入了解，請參閱 [Azure App Service 中的驗證與授權](overview-authentication-authorization.md)。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 設定 Web 應用程式的驗證
> * 限制對您組織中的使用者存取 Web 應用程式的權限

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-publish-a-web-app-on-app-service"></a>在 App Service 上建立和發佈 Web 應用程式

在本教學課程中，您需要部署到 Azure App Service 的 Web 應用程式。  您可以使用現有的 Web 應用程式，也可以遵循 [ASP.NET Core 快速入門](quickstart-dotnetcore.md)建立新的 Web 應用程式，並發佈至 App Service。

無論是使用現有 Web 應用程式或建立新的，請記下 Web 應用程式的名稱和部署的資源群組的名稱。 您在本教學課程中需要這些名稱。 在本教學課程中，程序和螢幕擷取畫面中的範例名稱包含 SecureWebApp。

## <a name="configure-authentication-and-authorization"></a>設定驗證和授權

您現在有一個在 App Service 上執行的 Web 應用程式。  接下來，您會為 Web 應用程式啟用驗證和授權。 您會以 Azure Active Directory 作為身分識別提供者。 如需詳細資訊，請參閱[為您的 App Service 應用程式設定 Azure Active Directory 驗證](configure-authentication-provider-aad.md)。

在 [Azure 入口網站](https://portal.azure.com)功能表中，選取 [資源群組]，或從任何頁面搜尋並選取 [資源群組]。

在 [資源群組] 中，尋找並選取資源群組。 在 **概觀** 中，選取應用程式的管理頁面。

:::image type="content" alt-text="選取 App Service" source="./media/scenario-secure-app-authentication-app-service/select-app-service.png":::

在應用程式的左側功能表中，按一下 [驗證/授權]，然後選取 [開啟] 以啟用 App Service 驗證。

在 [當要求未經驗證時所要採取的動作] 中，選取 [使用 Azure Active Directory 登入]。

在 [驗證提供者] 底下，選取 [Azure Active Directory]。 選取 [快速]，然後接受預設設定以建立新的 AD 應用程式，並選取 [確定]。

:::image type="content" alt-text="快速驗證" source="./media/scenario-secure-app-authentication-app-service/configure-authentication.png":::

在 [驗證/授權] 頁面中，選取 [儲存]。

當您看到通知中的 `Successfully saved the Auth Settings for <app-name> App` 訊息後，請重新整理入口網站頁面。

您現在有一個由 App Service 驗證和授權所保護的應用程式。

## <a name="verify-limited-access-to-the-web-app"></a>驗證 Web 應用程式的有限存取權

啟用 App Service 驗證和授權模組時，系統會在您的 Azure AD 租用戶中建立應用程式註冊。  應用程式註冊與您的 Web 應用程式具有相同的顯示名稱。 若要檢查這些設定，請在 Azure 入口網站功能表中選取 [Azure Active Directory]，然後選取 [應用程式註冊]。  選取之前建立的應用程式註冊。  在概觀中，確認 **支援的帳戶類型** 已設定為 **僅限我的組織**。

:::image type="content" alt-text="驗證存取權" source="./media/scenario-secure-app-authentication-app-service/verify-access.png":::

若要確認應用程式的存取權僅限於您組織中的使用者，請以 incognito 或私人模式啟動瀏覽器，並瀏覽至 `https://<app-name>.azurewebsites.net`。  系統應該會將您導向至安全的登入頁面，目的是確認不允許未經驗證的使用者存取網站。  以組織中的使用者身分登入，取得網站的存取權。  您也可以啟動新的瀏覽器，並嘗試使用個人帳戶登入，以確認組織外部的使用者沒有存取權。  

## <a name="clean-up-resources"></a>清除資源

如果您已完成本教學課程，且不再需要 Web 應用程式或相關聯的資源，請[清除您建立的資源](scenario-secure-app-clean-up-resources.md)。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
>
> * 設定 Web 應用程式的驗證
> * 限制對您組織中的使用者存取 Web 應用程式的權限

> [!div class="nextstepaction"]
> [App Service 存取儲存體](scenario-secure-app-access-storage.md)
