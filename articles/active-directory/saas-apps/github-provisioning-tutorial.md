---
title: 教學課程： GitHub 的使用者布建-Azure AD
description: 了解如何設定 Azure Active Directory 將使用者帳戶自動佈建和取消佈建至 GitHub。
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.openlocfilehash: 847c69a18a73d67b9b994e72686a4073ddd6d27f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91857529"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>教學課程︰設定 GitHub 來自動佈建使用者

本教學課程旨在說明您需要在 GitHub 和 Azure AD 中執行的步驟，以將使用者帳戶從 Azure AD 自動佈建和取消佈建至 GitHub。

## <a name="prerequisites"></a>Prerequisites

本教學課程中說明的案例假設您已經具有下列項目：

* Azure Active Directory 租用戶
* 在 [GitHub Enterprise 雲端](https://help.github.com/articles/github-s-products/#github-enterprise)中建立的 GitHub 組織，需使用 [GitHub Enterprise 的計費方案](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)
* GitHub 中具有組織管理員許可權的使用者帳戶
* [為 GitHub Enterprise 雲端組織設定的 SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/github-tutorial)
* 請確定已為您的組織提供 OAuth 存取權， [如下所述](https://help.github.com/en/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization)
* 只有在組織層級啟用 SSO 時，才支援 SCIM 布建至單一組織

> [!NOTE]
> Azure AD 布建整合依賴 [GITHUB SCIM API](https://developer.github.com/v3/scim/)，此 API 可供 [GitHub Enterprise 雲端](https://help.github.com/articles/github-s-products/#github-enterprise) 客戶使用 [GitHub Enterprise 計費方案](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)。

## <a name="assigning-users-to-github"></a>將使用者指派給 GitHub

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者帳戶佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和群組會進行同步處理。 

在設定並啟用佈建服務之前，您必須決定 Azure AD 中的哪些使用者及/或群組代表需要 GitHub 應用程式存取權的使用者。 一旦決定後，您可以依照此處的指示，將這些使用者指派給 GitHub 應用程式︰

[將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>將使用者指派給 GitHub 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 GitHub，以測試佈建設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 GitHub 時，您必須在 [指派] 對話方塊中選取 [使用者]**** 角色，或另一個有效的應用程式特有角色 (如果有的話)。 [預設存取]**** 角色不適用於佈建，系統會略過這些使用者。

## <a name="configuring-user-provisioning-to-github"></a>設定將使用者佈建至 GitHub

本節會引導您將 Azure AD 連線至 GitHub 的使用者帳戶佈建 API，以及根據 Azure AD 中的使用者和群組指派設定佈建服務，以在 GitHub 中建立、更新和停用指派的使用者帳戶。

### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>在 Azure AD 中設定將使用者帳戶自動佈建至 GitHub

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至 [Azure Active Directory > 企業應用程式 > 所有應用程式]**** 區段。

2. 如果您已將 GitHub 設定為單一登入，請使用 [搜尋] 欄位搜尋您的 GitHub 執行個體。 否則，請選取 [新增]****，並在應用程式庫中搜尋 [GitHub]****。 從搜尋結果中選取 GitHub，並將它新增至您的應用程式清單。

3. 選取您的 GitHub 執行個體，然後選取 [佈建]**** 索引標籤。

4. 將 [佈建模式] 設定為 [自動]。

    ![GitHub 佈建](./media/github-provisioning-tutorial/GitHub1.png)

5. 在 [系統管理員認證]**** 區段下，按一下 [授權]****。 此作業會在新的瀏覽器視窗中開啟 GitHub 授權對話方塊。 請注意，您必須確定已核准授權存取權。 遵循 [此處](https://help.github.com/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization)所述的指示。

6. 在新視窗中，使用您的管理帳戶登入 GitHub。 在產生的授權對話方塊中，選取您想要啟用佈建的 GitHub 小組，然後選取 [授權]****。 一旦完成後，回到 Azure 入口網站以完成佈建組態。

    ![螢幕擷取畫面顯示 GitHub 的登入頁面。](./media/github-provisioning-tutorial/GitHub2.png)

7. 在 Azure 入口網站中，輸入 [租用戶 URL]****，然後按一下 [測試連線]****，以確保 Azure AD 可以連線至您的 GitHub 應用程式。 如果連線失敗，請確定您的 GitHub 帳戶具有系統管理員權限，並且已正確輸入**租用戶 URl**，然後再試一次「授權」步驟 (您可以依照下列規則來組成**租用戶 URL**：`https://api.github.com/scim/v2/organizations/<Organization_name>`，您可以在 GitHub 帳戶下找到您的組織：[Settings]**** \(設定\) > [Organizations]**** \(組織\))。

    ![螢幕擷取畫面顯示 GitHub 中的組織頁面。](./media/github-provisioning-tutorial/GitHub3.png)

8. 在 [通知電子郵件]**** 欄位中，輸入應收到佈建錯誤通知的個人或群組之電子郵件地址，然後勾選 [發生失敗時傳送電子郵件通知] 核取方塊。

9. 按一下 **[儲存]** 。

10. 在 [對應] 區段下，選取 [同步處理 Azure Active Directory 使用者至 GitHub]****。

11. 在 [屬性對應]**** 區段中，檢閱從 Azure AD 同步至 GitHub 的使用者屬性。 選取為 [比對]**** 屬性的屬性會用來比對 GitHub 中的使用者帳戶，以進行更新作業。 選取 [儲存] 按鈕以認可任何變更。

12. 若要對 GitHub 啟用 Azure AD 佈建服務，請在 [設定]**** 區段中，將 [佈建狀態]**** 變更為 [開啟]****

13. 按一下 **[儲存]** 。

此作業會對 [使用者和群組] 區段中指派給 GitHub 的任何使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [同步處理詳細資料]**** 區段來監視進度，並依循連結前往佈建活動記錄，此記錄會描述佈建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
