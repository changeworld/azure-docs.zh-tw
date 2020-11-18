---
title: 教學課程︰以 Azure Active Directory 設定 Zendesk 來自動佈建使用者 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 來對 Zendesk 自動佈建及取消佈建使用者帳戶。
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: 620dd8fd586352ebeaf097a8f870a606f8e06c01
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359709"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>教學課程︰設定 Zendesk 來自動佈建使用者

本教學課程中的步驟示範如何在 Zendesk 與 Azure Active Directory (Azure AD) 中，將 Azure AD 設定為對 Zendesk 自動佈建和取消佈建使用者和群組。

> [!NOTE]
> 本教學課程描述以 Azure AD 使用者佈建服務為基礎的連接器。 如需此服務的用途、運作方式和常見問題集的詳細資訊，請參閱[使用 Azure Active Directory 對 軟體即服務 (SaaS) 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中說明的案例假設您有下列項目：

* Azure AD 租用戶。
* 已啟用 Professional 方案或更好方案的 Zendesk 租用戶。
* Zendesk 中具有管理員權限的使用者帳戶。

## <a name="add-zendesk-from-the-azure-marketplace"></a>從 Azure Marketplace 新增 Zendesk

以 Azure AD 設定 Zendesk 來自動佈建使用者之前，請先從 Azure Marketplace 將 Zendesk 新增至您的受控 SaaS 應用程式清單。

若要從 Azure Marketplace 新增 Zendesk，請遵循下列步驟。

1. 在 [Azure 入口網站](https://portal.azure.com)左側的導覽窗格上，選取 [Azure Active Directory]。

    ![Azure Active Directory 圖示](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請選取對話方塊頂端的 [新增應用程式]。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Zendesk**，然後從結果面板中選取 [Zendesk]。 若要新增應用程式，請選取 [新增]。

    ![結果清單中的 Zendesk](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>將使用者指派到 Zendesk

Azure Active Directory 使用所謂「指派」的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動佈建使用者的情況下，只會同步處理 Azure AD 中已指派給應用程式的使用者或群組。

在設定並啟用自動佈建使用者之前，請決定 Azure AD 中有哪些使用者或群組需要存取 Zendesk。 若要將這些使用者或群組指派給 Zendesk，請遵循[將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)中的指示。

### <a name="important-tips-for-assigning-users-to-zendesk"></a>將使用者指派給 Zendesk 的重要秘訣

* 目前，Zendesk 角色會在 Azure 入口網站 UI 中自動且動態地填入。 在將 Zendesk 角色指派給使用者之前，請確保已針對 Zendesk 完成初始同步，以擷取您 Zendesk 租用戶中的最新角色。

* 建議將單一 Azure AD 使用者指派給 Zendesk，以測試您的初始自動使用者佈建的設定。 您可以稍後於測試成功之後指派其他使用者或群組。

* 將使用者指派至 Zendesk 時，請在 [指派] 對話方塊中選取任何有效的應用程式特有角色 (如果有的話)。 具有 **預設存取** 角色的使用者會從佈建中排除。

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>設定將使用者自動佈建至 Zendesk 

本節將逐步引導您設定 Azure AD 佈建服務。 請利用此服務，以根據 Azure AD 中的使用者或群組指派，在 Zendesk 中建立、更新和停用使用者或群組。

> [!TIP]
> 您也可以為 Zendesk 啟用 SAML 型單一登入。 請遵循 [Zendesk 單一登入教學課程](zendesk-tutorial.md)中的指示。 單一登入和自動使用者佈建可以分開設定，但這兩個功能是互補的。

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>在 Azure AD 中為 Zendesk 設定自動使用者佈建

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式] > [所有應用程式] > [Zendesk]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Zendesk]  。

    ![應用程式清單中的 Zendesk 連結](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![Zendesk 佈建](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![Zendesk 佈建模式](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. 在 [管理員認證] 區段下，輸入 Zendesk 帳戶的 [管理員使用者名稱]、[祕密權杖] 和 [網域]。 這些值的範例如下：

   * 在 [管理員使用者名稱] 方塊中，填入 Zendesk 租用戶中管理帳戶的使用者名稱。 例如 admin@contoso.com。

   * 在 [祕密權杖] 方塊中填入祕密權杖，如步驟 6 所述。

   * 在 [網域] 方塊中，填入您的 Zendesk 租用戶子網域。 範例：針對租用戶 URL 為 `https://my-tenant.zendesk.com` 的帳戶，您的子網域會是 **my-tenant**。

6. Zendesk 帳戶的 [秘密權杖] 位於 [管理] > [API] > [設定]。 請確定 [權杖存取] 設為 [已啟用]。

    ![Zendesk 管理員設定](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Zendesk 秘密權杖](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. 填入步驟 5 所示的方塊之後，請選取 [測試連線]，以確定 Azure AD 可以連線至 Zendesk。 如果連線失敗，請確定您的 Zendesk 帳戶具有管理員權限並再試一次。

    ![Zendesk 測試連線](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. 在 [通知電子郵件] 方塊中，輸入應收到佈建錯誤通知之個人或群組的電子郵件地址。 選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![Zendesk 通知電子郵件](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. 選取 [儲存]。

10. 在 [對應] 區段中，選取 [同步處理 Azure Active Directory 使用者至 Zendesk]。

    ![Zendesk 使用者同步](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Zendesk 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 Zendesk 中的使用者帳戶以進行更新作業。 若要儲存任何變更，請選取 [儲存]。

    ![Zendesk 比對使用者屬性](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. 在 [對應] 區段底下，選取 [將 Azure Active Directory 群組同步至 Zendesk]。

    ![Zendesk 群組同步](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Zendesk 的群組屬性。 選取為 [比對] 屬性的屬性會用來比對 Zendesk 中的群組以進行更新作業。 若要儲存任何變更，請選取 [儲存]。

    ![Zendesk 比對群組屬性](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. 若要設定範圍篩選條件，請遵循[範圍篩選條件教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的指示。

15. 若要啟用 Zendesk 的 Azure AD 佈建服務，請在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

    ![Zendesk 佈建狀態](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. 定義您想要佈建到 Zendesk 的使用者或群組。 在 [設定] 區段的 [範圍] 中，選取您想要的值。

    ![Zendesk 範圍](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. 準備好要佈建時，請選取 [儲存]。

    ![Zendesk 儲存](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

針對 [設定] 區段的 [範圍] 中定義的所有使用者或群組，此作業會啟動首次同步處理。 首次同步處理所花的時間比後續同步處理更久。 只要 Azure AD 佈建服務在執行中，則後續同步處理大約每 40 分鐘進行一次。 

您可以使用 [同步處理詳細資料] 區段來監視進度，並循著連結取得佈建活動報告。 此報告描述 Azure AD 佈建服務在 Zendesk 上執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

* Zendesk 僅支援對具有 **代理程式** 角色的使用者使用群組。 如需詳細資訊，請參閱 [Zendesk 文件](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups)。

* 將自訂角色指派給使用者或群組時，Azure AD 自動使用者佈建服務還會指派預設角色 **代理程式**。 只有代理程式才能指派自訂角色。 如需詳細資訊，請參閱 [Zendesk API 文件](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests)。 

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
