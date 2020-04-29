---
title: 教學課程︰以 Azure Active Directory 設定 Samanage 來自動佈建使用者 | Microsoft Docs
description: 瞭解如何設定 Azure Active Directory 將使用者帳戶自動布建和取消布建至 Samanage。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988efc2087b3b30e6073bd7f6e2cf08f91fd397c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77060425"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>教學課程︰設定 Samanage 來自動佈建使用者

本教學課程示範在 Samanage 和 Azure Active Directory （Azure AD）中執行的步驟，以設定 Azure AD 自動將使用者和群組布建和取消布建至 Samanage。

> [!NOTE]
> 本教學課程說明建立在 Azure AD 使用者布建服務之上的連接器。 如需此服務的用途、運作方式和常見問題的資訊，請參閱[使用 Azure Active Directory 將使用者布建和取消布建至軟體即服務（SaaS）應用程式](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>先決條件

本教學課程中所述的案例假設您有：

* Azure AD 租用戶。
* 具有 Professional 套件的[Samanage 租](https://www.samanage.com/pricing/)使用者。
* Samanage 中具有系統管理員許可權的使用者帳戶。

> [!NOTE]
> Azure AD 布建整合依賴[Samanage REST API](https://www.samanage.com/api/)。 此 API 可供 Samanage 開發人員使用 Professional 套件的帳戶。

## <a name="add-samanage-from-the-azure-marketplace"></a>從 Azure Marketplace 新增 Samanage

使用 Azure AD 設定 Samanage 來自動布建使用者之前，請先從 Azure Marketplace 將 Samanage 新增至受控 SaaS 應用程式清單。

若要從 Marketplace 新增 Samanage，請遵循下列步驟。

1. 在[Azure 入口網站](https://portal.azure.com)的左側導覽窗格中，選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 圖示](common/select-azuread.png)

2. 移至 [**企業應用程式**]，然後選取 [**所有應用程式**]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請選取對話方塊頂端的 [新增應用程式]****。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入**Samanage** ，然後從結果面板中選取 [ **Samanage** ]。 若要新增應用程式，請選取 [**新增**]。

    ![結果清單中的 Samanage](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>將使用者指派給 Samanage

Azure Active Directory 使用稱為「*指派*」的概念，來判斷哪些使用者應接收所選應用程式的存取權。 在自動使用者布建的內容中，只有指派給 Azure AD 中應用程式的使用者或群組會進行同步處理。

在您設定並啟用自動使用者布建之前，請先決定 Azure AD 中的哪些使用者或群組需要存取 Samanage。 若要將這些使用者或群組指派給 Samanage，請依照[將使用者或群組指派給企業應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)中的指示進行。

### <a name="important-tips-for-assigning-users-to-samanage"></a>將使用者指派給 Samanage 的重要秘訣

*    目前，Samanage 角色會自動和動態地填入 Azure 入口網站 UI 中。 將 Samanage 角色指派給使用者之前，請確定已針對 Samanage 完成初始同步處理，以取得 Samanage 租使用者中的最新角色。

*    建議您將單一 Azure AD 使用者指派給 Samanage，以測試您的初始自動使用者布建設定。 您可以稍後在測試成功之後指派其他使用者和群組。

*    當您將使用者指派給 Samanage 時，請在 [指派] 對話方塊中選取任何有效的應用程式特定角色（如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configure-automatic-user-provisioning-to-samanage"></a>設定自動使用者布建至 Samanage

本節會引導您完成設定 Azure AD 布建服務的步驟。 用來根據 Azure AD 中的使用者或群組指派，在 Samanage 中建立、更新和停用使用者或群組。

> [!TIP]
> 您也可以啟用 Samanage 的 SAML 型單一登入。 遵循[Samanage 單一登入教學](samanage-tutorial.md)課程中的指示進行。 單一登入可以與自動使用者布建分開設定，雖然這兩個功能彼此互補。

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>在 Azure AD 中設定自動使用者布建以進行 Samanage

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [**企業應用程式** > ] [**所有應用程式** > ]**Samanage**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Samanage] ****。

    ![應用程式清單中的 Samanage 連結](common/all-applications.png)

3. 選取 [**布**建] 索引標籤。

    ![Samanage 佈建](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. 將布建模式設定為 [**自動** **]** 。

    ![布建索引標籤](common/provisioning-automatic.png)

5. 在 [**管理員認證**] 區段下，輸入您的 Samanage**租使用者 URL**和**密碼權杖**。 按一下 [**測試連接**] 以確保 Azure AD 可以連接到 Samanage。 如果連線失敗，請確定您的 Samanage 帳戶具有管理員權限，然後再試一次。

    ![Samanage 測試連接](./media/samanage-provisioning-tutorial/provisioning.png)

6. 在 [**通知電子郵件**] 方塊中，輸入要接收布建錯誤通知的人員或群組的電子郵件地址。 選取 [**發生失敗時傳送電子郵件通知**] 核取方塊。

    ![Samanage 通知電子郵件](./media/samanage-provisioning-tutorial/EmailNotification.png)

7. 選取 [儲存]  。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者至 Samanage**]。

    ![Samanage 使用者同步處理](./media/samanage-provisioning-tutorial/UserMappings.png)

9. 在 [屬性對應]**** 區段中，檢閱從 Azure AD 同步至 Samanage 的使用者屬性。 選取為 [比對]**** 屬性的屬性會用來比對 Samanage 中的使用者帳戶，以進行更新作業。 若要儲存任何變更，請選取 [**儲存**]。

    ![Samanage 相符的使用者屬性](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

10. 若要啟用群組對應，請在 [對應]**** 區段下方，選取 [將 Azure Active Directory 群組同步處理至 Samanage]****。

    ![Samanage 群組同步處理](./media/samanage-provisioning-tutorial/GroupMappings.png)

11. 將 [已啟用]**** 設定為 [是]****，以同步處理群組。 在 [屬性對應]**** 區段中，檢閱從 Azure AD 同步處理至 Samanage 的群組屬性。 選取為 [比對]**** 屬性的屬性會用來比對 Samanage 中的使用者帳戶，以進行更新作業。 若要儲存任何變更，請選取 [**儲存**]。

    ![Samanage 相符的群組屬性](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. 若要設定範圍篩選準則，請遵循[範圍篩選教學](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)課程中的指示。

13. 若要啟用 Samanage 的 Azure AD 布建服務，在 [**設定**] 區段中，將 [布建**狀態**] 變更為 [**開啟**]。

    ![Samanage 布建狀態](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

14. 定義您想要布建到 Samanage 的使用者或群組。 在 [**設定**] 區段的 [**範圍**] 中，選取您想要的值。 當您選取 [**同步所有使用者和群組**] 選項時，請考慮下列「連接器限制」一節中所述的限制。

    ![Samanage 範圍](./media/samanage-provisioning-tutorial/ScopeSync.png)

15. 當您準備好要布建時，請選取 [**儲存**]。

    ![Samanage 儲存](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


這項作業會啟動 [**設定**] 區段的 [**範圍**] 中定義的所有使用者或群組的首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行。 只要執行 Azure AD 布建服務，它們大約每40分鐘就會發生一次。 

您可以使用 [**同步處理詳細資料**] 區段來監視進度，並遵循 [布建活動報告] 的連結。 此報告會描述 Samanage 上的 Azure AD 布建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

如果您選取 [**同步所有使用者和群組**] 選項，並設定 [Samanage**角色**] 屬性的值，則在 [**如果 null （是選擇性）** ] 方塊中，[預設值] 下的值必須以下列格式表示：

- {"displayName"： "role"}，其中 role 是您想要的預設值。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
