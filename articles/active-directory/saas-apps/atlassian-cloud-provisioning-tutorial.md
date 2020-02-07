---
title: 教學課程：使用 Azure Active Directory 設定 Atlassian Cloud 來自動布建使用者 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory 以自動布建和取消布建使用者帳戶到 Atlassian Cloud。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 7ddccef00cf1b5ad524c0e1eaa7aed52c0e55197
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "77059329"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>教學課程：設定 Atlassian Cloud 來自動布建使用者

本教學課程的目的是要示範在 Atlassian Cloud 和 Azure Active Directory （Azure AD）中執行的步驟，以設定 Azure AD 自動布建和取消布建使用者和/或群組至 Atlassian 雲端。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [Atlassian 雲端租使用者](https://www.atlassian.com/licensing/cloud)
* Atlassian Cloud 中具有系統管理員許可權的使用者帳戶。

> [!NOTE]
> Azure AD 布建整合依賴**Atlassian CLOUD SCIM API**，可供 Atlassian 雲端團隊使用。

## <a name="add-atlassian-cloud-from-the-gallery"></a>從資源庫新增 Atlassian Cloud

將 Atlassian Cloud 設定為使用 Azure AD 自動布建使用者之前，您需要從 Azure AD 應用程式庫將 Atlassian Cloud 新增至受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式庫新增 Atlassian Cloud，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左側導覽窗格中，選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取窗格頂端的 [**新增應用程式**] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入**Atlassian Cloud**，在結果面板中選取 [ **Atlassian cloud** ]，然後按一下 [**新增**] 按鈕以新增應用程式。

    ![結果清單中的 [Atlassian Cloud]](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>將使用者指派給 Atlassian Cloud

Azure Active Directory 使用稱為「*指派*」的概念，來判斷哪些使用者應接收所選應用程式的存取權。 在自動使用者布建的內容中，只有已指派給 Azure AD 中應用程式的使用者和/或群組會進行同步處理。

在設定並啟用自動使用者布建之前，您應該決定 Azure AD 中的哪些使用者和/或群組需要 Atlassian Cloud 的存取權。 一旦決定後，您可以遵循此處的指示，將這些使用者和/或群組指派給 Atlassian Cloud：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>將使用者指派給 Atlassian Cloud 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 Atlassian Cloud，以測試自動使用者布建設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 Atlassian Cloud 時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特定角色（如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>設定自動使用者布建至 Atlassian Cloud 

本節將引導您逐步設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和/或群組指派，在 Atlassian Cloud 中建立、更新和停用使用者和/或群組。

> [!TIP]
> 您也可以選擇啟用 Atlassian Cloud 的 SAML 型單一登入，請遵循[Atlassian Cloud 單一登入教學](atlassian-cloud-tutorial.md)課程中提供的指示。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>若要在 Azure AD 中設定 Atlassian Cloud 的自動使用者布建：

1. 登入[Azure 入口網站](https://portal.azure.com)並選取 [**企業應用程式**]，選取 [**所有應用程式**]，然後選取 [ **Atlassian Cloud**]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Atlassian Cloud]。

    ![應用程式清單中的 [Atlassian Cloud] 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![Atlassian 雲端布建](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![Atlassian 雲端布建](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. 流覽至[Atlassian 組織管理員](https://admin.atlassian.com) **> 選取 [組織 >] 目錄**。

    ![Atlassian 雲端布建](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

6. 按一下 [**使用者**布建]，然後按一下 [**建立目錄**]。 分別將**目錄基底 url**和**持有人權杖**複製到 [**租使用者 URL** ] 和 [**秘密權杖**] 欄位。

    ![Atlassian 雲端布建](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Atlassian 雲端布建](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png) ![Atlassian 雲端布建](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. 在 [**管理員認證**] 區段下，輸入 Atlassian 雲端帳戶的 [**租使用者 URL** ] 和 [**秘密權杖**]。 這些值的範例如下：

   * 在 [**租使用者 URL** ] 欄位中，填入您從 Atlassian 接收的特定租使用者端點，如步驟6所述。 例如： `https://api.atlassian.com/scim/directory/{directoryId}`。

   * 在 [秘密權杖] 欄位中，填入秘密權杖，如同步驟 6 所述。

8. 填入步驟7中顯示的欄位之後，按一下 [**測試**連線] 以確保 Azure AD 可以連接到 Atlassian Cloud。 如果連線失敗，請確定您的 Atlassian 雲端帳戶具有系統管理員許可權，然後再試一次。

    ![Atlassian 雲端布建](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

9. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![Atlassian 雲端布建](./media/atlassian-cloud-provisioning-tutorial/notification.png)

10. 按一下 [檔案]。

11. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者 Atlassian 雲端**]。

    ![Atlassian 雲端布建](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

12. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步至 Atlassian Cloud 的使用者屬性。 選取為 [比對] 屬性**的屬性會**用來比對 Atlassian Cloud 中的使用者帳戶以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![Atlassian 雲端布建](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

13. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 群組至 Atlassian 雲端**]。

    ![Atlassian 雲端布建](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

14. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步至 Atlassian Cloud 的群組屬性。 選取為 [比對] 屬性**的屬性會**用來比對 Atlassian Cloud 中的群組以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![Atlassian 雲端布建](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

15. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

16. 若要啟用 Atlassian Cloud 的 Azure AD 布建服務，請在 [**設定**] 區段中，將 [布建**狀態**] 變更為 [**開啟**]。

    ![Atlassian 雲端布建](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

17. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建到 Atlassian Cloud 的使用者和/或群組。

    ![Atlassian 雲端布建](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

18. 當您準備好要佈建時，按一下 [儲存]。

    ![Atlassian 雲端布建](./media/atlassian-cloud-provisioning-tutorial/save.png)

此作業會對在 [設定] 區段的 [範圍] 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [**同步處理詳細資料**] 區段來監視進度，並遵循連結來布建活動報告，其中描述 Atlassian Cloud 上的 Azure AD 布建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

* Atlassian Cloud 只允許從[已驗證的網域](https://confluence.atlassian.com/cloud/organization-administration-938859734.html)布建使用者。
* Atlassian Cloud 目前不支援群組重新命名。 這表示在 Azure AD 中，對群組的 displayName 進行的任何變更都不會更新，而且會反映在 Atlassian Cloud 中。
* 只有在使用者具有 Microsoft Exchange 信箱時，才會填入 Azure AD 中的**mail**使用者屬性值。 如果使用者沒有帳戶，建議您將不同的所需屬性對應至 Atlassian Cloud 中的 [**電子郵件**] 屬性。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png