---
title: 教學課程︰以 Azure Active Directory 設定 Tableau Online 來自動佈建使用者 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建並取消佈建使用者帳戶至 Tableau Online。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: a42790e079985b003776b381c74f837b0ba619b1
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359199"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>教學課程︰設定 Tableau Online 來自動佈建使用者

本教學課程中的步驟示範如何在 Tableau Online 與 Azure Active Directory (Azure AD) 中，將 Azure AD 設定為對 Tableau Online 自動佈建和取消佈建使用者和群組。

> [!NOTE]
> 本教學課程描述以 Azure AD 使用者佈建服務為基礎的連接器。 如需此服務的用途、運作方式和常見問題集的詳細資訊，請參閱[使用 Azure Active Directory 對 軟體即服務 (SaaS) 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中說明的案例假設您有下列項目：

*   Azure AD 租用戶。
*   [Tableau Online 租用戶](https://www.tableau.com/)。
*   Tableau Online 中具有管理員權限的使用者帳戶。

> [!NOTE]
> Azure AD 佈建整合依賴 [Tableau Online Rest API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm)。 此 API 可供 Tableau Online 開發人員使用。

## <a name="add-tableau-online-from-the-azure-marketplace"></a>從 Azure Marketplace 新增 Tableau Online
以 Azure AD 設定 Tableau Online 來自動佈建使用者之前，請先從 Azure Marketplace 將 Tableau Online 新增至您的受控 SaaS 應用程式清單。

若要從 Marketplace 新增 Tableau Online，請遵循下列步驟。

1. 在 [Azure 入口網站](https://portal.azure.com)左側的導覽窗格上，選取 [Azure Active Directory]。

    ![Azure Active Directory 圖示](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請選取對話方塊頂端的 [新增應用程式]。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Tableau Online**，然後從結果面板中選取 [Tableau Online]。 若要新增應用程式，請選取 [新增]。

    ![結果清單中的 Tableau Online](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>將使用者指派給 Tableau Online

Azure Active Directory 使用所謂「指派」的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動佈建使用者的情況下，只會同步處理 Azure AD 中已指派給應用程式的使用者或群組。

在設定並啟用自動佈建使用者之前，請決定 Azure AD 中有哪些使用者或群組需要存取 Tableau Online。 若要將這些使用者或群組指派給 Tableau Online，請遵循[將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)中的指示。

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>將使用者指派給 Tableau Online 的重要秘訣

*   建議您將單一 Azure AD 使用者指派至 Tableau Online，以測試自動使用者佈建組態。 您可以稍後再指派其他使用者或群組。

*   將使用者指派至 Tableau Online 時，請在 [指派] 對話方塊中選取任何有效的應用程式特有角色 (如果有的話)。 具有 **預設存取** 角色的使用者會從佈建中排除。

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>設定將使用者自動佈建至 Tableau Online

本節將逐步引導您設定 Azure AD 佈建服務。 請利用此服務，以根據 Azure AD 中的使用者或群組指派，在 Tableau Online 中建立、更新和停用使用者或群組。

> [!TIP]
> 您也可以為 Tableau Online 啟用 SAML 型單一登入。 請遵循 [Tableau Online 單一登入教學課程](tableauonline-tutorial.md)中的指示。 單一登入和自動使用者佈建可以分開設定，但這兩個功能是互補的。

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>在 Azure AD 中為 Tableau Online 設定自動使用者佈建

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式] > [所有應用程式] > [Tableau Online]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Tableau Online]  。

    ![應用程式清單中的 Tableau Online 連結](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![Tableau Online 佈建](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![Tableau Online 佈建模式](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. 在 [管理員認證] 區段下，輸入 Tableau Online 帳戶的 [網域]、[管理員使用者名稱]、[管理員密碼]和 [內容 URL]：

   * 在 [網域] 方塊中，根據步驟 6 填入子網域。

   * 在 [管理員使用者名稱] 方塊中，填入 Tableau Online 租用戶上管理員帳戶的使用者名稱。 例如 admin@contoso.com。

   * 在 [管理員密碼] 方塊中，填入對應於管理員使用者名稱的管理員帳戶密碼。

   * 在 [內容 URL] 方塊中，根據步驟 6 填入子網域。

6. 登入 Tableau Online 的系統管理帳戶之後，即可從管理員頁面的 URL 中取得 [網域] 和 [內容 URL] 的值。

    * Tableau Online 帳戶的 **網域** 可以從 URL 的這個部分複製：

        ![Tableau Online 網域](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * 您可以從此區段複製 Tableau Online 帳戶的 [內容 URL]。 這是在設定帳戶期間所定義的值。 在此範例中，值是 "contoso"：

        ![Tableau Online 內容 URL](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > 您的 **網域** 可能會與此處顯示的不一樣。

7. 填入步驟 5 所示的方塊之後，請選取 [測試連線]，以確定 Azure AD 可以連線至 Tableau Online。 如果連線失敗，請確定您的 Tableau Online 帳戶具有管理員權限，然後再試一次。

    ![Tableau Online 測試連線](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. 在 [通知電子郵件] 方塊中，輸入應收到佈建錯誤通知之個人或群組的電子郵件地址。 選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![Tableau Online 通知電子郵件](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. 選取 [儲存]。

10. 在 [對應] 區段中，選取 [將 Azure Active Directory 使用者同步至 Tableau]。

    ![Tableau Online 使用者同步](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步處理至 Tableau Online 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 Tableau Online 中的使用者帳戶以進行更新作業。 若要儲存任何變更，請選取 [儲存]。

    ![Tableau Online 比對使用者屬性](./media/tableau-online-provisioning-tutorial/attribute.png)

12. 在 [對應] 區段中，選取 [同步處理 Azure Active Directory 群組至 Tableau]。

    ![Tableau Online 群組同步](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步處理至 Tableau Online 的群組屬性。 選取為 [比對] 屬性的屬性會用來比對 Tableau Online 中的使用者帳戶以進行更新作業。 若要儲存任何變更，請選取 [儲存]。

    ![Tableau Online 比對群組屬性](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. 若要設定範圍篩選條件，請遵循[範圍篩選條件教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的指示。

15. 若要啟用 Tableau Online 的 Azure AD 佈建服務，請在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

    ![Tableau Online 佈建狀態](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. 定義您想要佈建到 Tableau Online 的使用者或群組。 在 [設定] 區段的 [範圍] 中，選取您想要的值。

    ![Tableau Online 範圍](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. 準備好要佈建時，請選取 [儲存]。

    ![Tableau Online 儲存](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

針對 [設定] 區段的 [範圍] 中定義的所有使用者或群組，此作業會啟動首次同步處理。 首次同步處理所花的時間比後續同步處理更久。 只要 Azure AD 佈建服務在執行中，則後續同步處理大約每 40 分鐘進行一次。 

您可以使用 [同步處理詳細資料] 區段來監視進度，並循著連結取得佈建活動報告。 此報告描述 Azure AD 佈建服務在 Tableau Online 上執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="change-log"></a>變更記錄
* 2020/09/30 - 已為使用者新增屬性 "authSetting" 的支援。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png