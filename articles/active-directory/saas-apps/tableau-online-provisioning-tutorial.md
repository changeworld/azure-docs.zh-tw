---
title: 教學課程︰以 Azure Active Directory 設定 Tableau Online 來自動佈建使用者 | Microsoft Docs
description: 瞭解如何設定 Azure Active Directory，將使用者帳戶自動布建和取消布建至 Tableau Online。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0be9c435-f9a1-484d-8059-e578d5797d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fae770950810899f7c6583fa401110c3e85022b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77064202"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>教學課程︰設定 Tableau Online 來自動佈建使用者

本教學課程示範在 Tableau Online 和 Azure Active Directory （Azure AD）中執行的步驟，以設定 Azure AD 自動將使用者和群組布建和取消布建至 Tableau Online。

> [!NOTE]
> 本教學課程說明建立在 Azure AD 使用者布建服務之上的連接器。 如需此服務的用途、運作方式和常見問題的資訊，請參閱[使用 Azure Active Directory 將使用者布建和取消布建至軟體即服務（SaaS）應用程式](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>先決條件

本教學課程中所述的案例假設您有：

*   Azure AD 租用戶。
*   [Tableau Online 租](https://www.tableau.com/)使用者。
*   Tableau Online 中具有系統管理員許可權的使用者帳戶。

> [!NOTE]
> Azure AD 布建整合依賴[Tableau Online REST API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm)。 此 API 可供 Tableau 線上開發人員使用。

## <a name="add-tableau-online-from-the-azure-marketplace"></a>從 Azure Marketplace 新增 Tableau Online
在您將 Tableau Online 設定為使用 Azure AD 自動布建使用者之前，請從 Azure Marketplace 將 Tableau Online 新增至受控 SaaS 應用程式清單。

若要從 Marketplace 新增 Tableau Online，請遵循下列步驟。

1. 在[Azure 入口網站](https://portal.azure.com)的左側導覽窗格中，選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 圖示](common/select-azuread.png)

2. 移至 [**企業應用程式**]，然後選取 [**所有應用程式**]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請選取對話方塊頂端的 [新增應用程式]****。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入**Tableau online** ，然後從結果面板中選取 [ **Tableau Online** ]。 若要新增應用程式，請選取 [**新增**]。

    ![結果清單中的 Tableau Online](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>將使用者指派給 Tableau Online

Azure Active Directory 使用稱為「*指派*」的概念，來判斷哪些使用者應接收所選應用程式的存取權。 在自動使用者布建的內容中，只有指派給 Azure AD 中應用程式的使用者或群組會進行同步處理。

在您設定並啟用自動使用者布建之前，請先決定 Azure AD 中的哪些使用者或群組需要 Tableau Online 的存取權。 若要將這些使用者或群組指派給 Tableau Online，請依照[將使用者或群組指派給企業應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)中的指示進行。

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>將使用者指派給 Tableau Online 的重要秘訣

*   建議您將單一 Azure AD 使用者指派給 Tableau Online，以測試自動使用者布建設定。 您稍後可以指派其他使用者或群組。

*   當您將使用者指派給 Tableau Online 時，請在 [指派] 對話方塊中選取任何有效的應用程式特定角色（如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>設定自動使用者布建至 Tableau Online

本節會引導您完成設定 Azure AD 布建服務的步驟。 您可以使用它，根據 Azure AD 中的使用者或群組指派，在 Tableau Online 中建立、更新和停用使用者或群組。

> [!TIP]
> 您也可以啟用 Tableau Online 的 SAML 型單一登入。 遵循[Tableau Online 單一登入教學](tableauonline-tutorial.md)課程中的指示進行。 單一登入可以與自動使用者布建分開設定，雖然這兩個功能彼此互補。

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>在 Azure AD 中設定線上 Tableau 的自動使用者布建

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [**企業應用程式** > ] [**所有應用程式** > **Tableau Online**]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Tableau Online] ****。

    ![應用程式清單中的 Tableau Online 連結](common/all-applications.png)

3. 選取 [**布**建] 索引標籤。

    ![Tableau Online 佈建](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. 將布建模式設定為 [**自動** **]** 。

    ![Tableau Online 布建模式](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. 在 [**管理員認證**] 區段下，輸入 Tableau Online 帳戶的 [網域]、[管理員使用者名稱]、[管理員密碼] 和 [內容 URL]：

   * 在 [**網域**] 方塊中，根據步驟6填入子域。

   * 在 [**管理員使用者名稱**] 方塊中，填入 Clarizen 租使用者上系統管理員帳戶的使用者名稱。 例如 admin@contoso.com。

   * 在 [**管理員密碼**] 方塊中，填入與管理員使用者名稱對應的系統管理員帳戶密碼。

   * 在 [**內容 URL** ] 方塊中，根據步驟6填入子域。

6. 在您登入 Tableau Online 的系統管理帳戶之後，您可以從 [管理] 頁面的 URL 取得 [**網域**和**內容 url** ] 的值。

    * Tableau Online 帳戶的**網域**可以從 URL 的這個部分複製：

        ![Tableau Online 網域](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * 您可以從此區段複製 Tableau Online 帳戶的**內容 URL** 。 這是在帳戶設定期間定義的值。 在此範例中，值是 "contoso"：

        ![Tableau 線上內容 URL](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > 您的**網域**可能與這裡所示的不同。

7. 填入步驟5所示的方塊之後，請選取 [**測試**連線] 以確定 Azure AD 可以連線至 Tableau Online。 如果連線失敗，請確定您的 Tableau Online 帳戶具有系統管理員許可權，然後再試一次。

    ![Tableau Online 測試連接](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. 在 [**通知電子郵件**] 方塊中，輸入要接收布建錯誤通知的人員或群組的電子郵件地址。 選取 [**發生失敗時傳送電子郵件通知**] 核取方塊。

    ![Tableau 線上通知電子郵件](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. 選取 [儲存]  。

10. 在 [對應]**** 區段中，選取 [將 Azure Active Directory 使用者同步至 Tableau]****。

    ![Tableau Online 使用者同步處理](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. 在 [**屬性**對應] 區段中，檢查從 Azure AD 同步至 Tableau Online 的使用者屬性。 選取為 [比對]**** 屬性的屬性會用來比對 Tableau Online 中的使用者帳戶以進行更新作業。 若要儲存任何變更，請選取 [**儲存**]。

    ![Tableau Online 符合的使用者屬性](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. 在 [對應]**** 區段中，選取 [同步處理 Azure Active Directory 群組至 Tableau]****。

    ![Tableau Online 群組同步處理](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. 在 [**屬性**對應] 區段中，檢查從 Azure AD 同步至 Tableau Online 的群組屬性。 選取為 [比對]**** 屬性的屬性會用來比對 Tableau Online 中的使用者帳戶以進行更新作業。 若要儲存任何變更，請選取 [**儲存**]。

    ![Tableau Online 相符的群組屬性](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. 若要設定範圍篩選準則，請遵循[範圍篩選教學](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)課程中的指示。

15. 若要啟用 Tableau Online 的 Azure AD 布建服務，請在 [**設定**] 區段中，將 [布建**狀態**] 變更為 [**開啟**]。

    ![Tableau Online 布建狀態](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. 定義您想要布建到 Tableau Online 的使用者或群組。 在 [**設定**] 區段的 [**範圍**] 中，選取您想要的值。

    ![Tableau Online 範圍](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. 當您準備好要布建時，請選取 [**儲存**]。

    ![Tableau Online 儲存](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

這項作業會啟動 [**設定**] 區段的 [**範圍**] 中定義的所有使用者或群組的首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行。 只要執行 Azure AD 布建服務，它們大約每40分鐘就會發生一次。 

您可以使用 [**同步處理詳細資料**] 區段來監視進度，並遵循 [布建活動報告] 的連結。 此報告會描述 Tableau Online 上的 Azure AD 布建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
