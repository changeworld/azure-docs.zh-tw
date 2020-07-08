---
title: 教學課程：設定 Zscaler 一個以 Azure Active Directory 自動布建使用者 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory 自動布建和取消布建使用者帳戶，以 Zscaler 一個。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 72f6ba2b-73ed-420a-863a-aff672f26fa3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 87413932acee576934ee50b59546371b03ceaf7e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77064167"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>教學課程：設定 Zscaler 一個來自動布建使用者

本教學課程示範在 Zscaler One 和 Azure Active Directory （Azure AD）中執行的步驟，以設定 Azure AD 自動布建和取消布建使用者和群組，以 Zscaler 一個。

> [!NOTE]
> 本教學課程描述以 Azure AD 使用者佈建服務為基礎的連接器。 如需此服務的用途、運作方式和常見問題集的詳細資訊，請參閱[使用 Azure Active Directory 對 軟體即服務 (SaaS) 應用程式自動佈建和取消佈建使用者](../active-directory-saas-app-provisioning.md)。


## <a name="prerequisites"></a>Prerequisites

本教學課程中說明的案例假設您有下列項目：

* Azure AD 租用戶。
* 一個 Zscaler 的租使用者。
* Zscaler 中具有系統管理員許可權的使用者帳戶。

> [!NOTE]
> Azure AD 布建整合依賴 Zscaler One SCIM API。 此 API 可讓您針對具有企業套件的帳戶 Zscaler 一位開發人員。

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>從 Azure Marketplace 新增一個 Zscaler

將 Zscaler 設定為使用 Azure AD 自動布建使用者之前，請先從 Azure Marketplace 將 Zscaler 新增至受控 SaaS 應用程式清單。

若要從 Marketplace 新增 Zscaler，請遵循下列步驟。

1. 在 [Azure 入口網站](https://portal.azure.com)左側的導覽窗格上，選取 [Azure Active Directory]。

    ![Azure Active Directory 圖示](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請選取對話方塊頂端的 [新增應用程式]。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入**Zscaler one** ，然後從結果面板中選取 [ **Zscaler** ]。 若要新增應用程式，請選取 [新增]。

    ![結果清單中的 Zscaler One](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>將使用者指派給 Zscaler 一個

Azure Active Directory 使用所謂「指派」的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動佈建使用者的情況下，只會同步處理 Azure AD 中已指派給應用程式的使用者或群組。

在您設定並啟用自動使用者布建之前，請先決定 Azure AD 中的哪些使用者或群組需要存取 Zscaler One。 若要將這些使用者或群組指派給 Zscaler，請依照[將使用者或群組指派給企業應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)中的指示進行。

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>將使用者指派給 Zscaler 一的重要秘訣

* 我們建議您將單一 Azure AD 使用者指派給 Zscaler 一個，以測試自動使用者布建設定。 您可以稍後再指派其他使用者或群組。

* 當您將使用者指派給 Zscaler 時，請在 [指派] 對話方塊中選取任何有效的應用程式特定角色（如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>設定自動使用者布建以 Zscaler 一個

本節將逐步引導您設定 Azure AD 佈建服務。 您可以使用它，根據 Azure AD 中的使用者或群組指派，在 Zscaler 中建立、更新和停用使用者或群組。

> [!TIP]
> 您也可以為 Zscaler 一個啟用 SAML 型單一登入。 依照[Zscaler 單一登入教學](zscaler-One-tutorial.md)課程中的指示進行。 單一登入可以與自動使用者布建分開設定，雖然這兩個功能彼此互補。

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>在 Azure AD 中設定 Zscaler 一個的自動使用者布建

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [**企業應用程式**] [  >  **所有應用程式**]  >  **Zscaler 一個**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Zscaler One]****。

    ![應用程式清單中的 Zscaler 一個連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![Zscaler 一個布建](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![Zscaler 一種布建模式](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. 在 [**管理員認證**] 區段下，使用 Zscaler 一個帳戶的設定填入 [**租使用者 URL** ] 和 [**秘密權杖**] 方塊，如步驟6所述。

6. 若要取得租使用者 URL 和秘密權杖，請**Administration**移至  >  Zscaler 單一入口網站 UI 中的 [系統管理] [**驗證設定**]。 在 [驗證類型]**** 下方，選取 [SAML]****。

    ![Zscaler 一項驗證設定](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. 選取 [**設定 saml** ] 以開啟 [**設定 saml** ] 選項。

    ![Zscaler 一個設定 SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. 選取 [**啟用 SCIM 型布建**] 以取得 [**基底 URL** ] 和 [**持有人權杖**] 中的設定。 然後儲存設定。 將 [**基底 URL** ] 設定複製到 Azure 入口網站中的 [**租使用者 URL** ]。 將 [**持有人權杖**] 設定複製到 Azure 入口網站中的 [**秘密權杖**]。

7. 填入步驟5所示的方塊之後，請選取 [**測試連接**] 以確定 Azure AD 可以連接到 Zscaler。 如果連線失敗，請確定您的 Zscaler 一個帳戶具有系統管理員許可權，然後再試一次。

    ![Zscaler 一個測試連接](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. 在 [通知電子郵件] 方塊中，輸入應收到佈建錯誤通知之個人或群組的電子郵件地址。 選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![Zscaler 一封通知電子郵件](./media/zscaler-one-provisioning-tutorial/notification.png)

9. 選取 [儲存]。

10. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者] 以 Zscaler 一個**。

    ![Zscaler 一個使用者同步處理](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. 檢查從 Azure AD 同步處理的使用者屬性，並在 [**屬性**對應] 區段中 Zscaler 一個。 選取為 [比對] 屬性**的屬性會**用來比對 Zscaler 中的使用者帳戶，以進行更新作業。 若要儲存任何變更，請選取 [儲存]。

    ![Zscaler 一個相符的使用者屬性](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 群組] 以 Zscaler 一個**。

    ![Zscaler 一個群組同步處理](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. 在 [**屬性**對應] 區段中，檢查從 Azure AD 同步處理到 Zscaler 的群組屬性。 選取為 [比對] 屬性**的屬性會**用來比對 Zscaler 中的群組以進行更新作業。 若要儲存任何變更，請選取 [儲存]。

    ![Zscaler 一個相符的群組屬性](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. 若要設定範圍篩選條件，請遵循[範圍篩選條件教學課程](./../active-directory-saas-scoping-filters.md)中的指示。

15. 若要啟用 Zscaler 的 Azure AD 布建服務，請在 [**設定**] 區段中，將 [布建**狀態**] 變更為 [**開啟**]。

    ![Zscaler 一個布建狀態](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. 定義您想要布建的使用者或群組，以 Zscaler 一個。 在 [設定] 區段的 [範圍] 中，選取您想要的值。

    ![Zscaler 一個範圍](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. 準備好要佈建時，請選取 [儲存]。

    ![Zscaler 一個儲存](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

針對 [設定] 區段的 [範圍] 中定義的所有使用者或群組，此作業會啟動首次同步處理。 首次同步處理所花的時間比後續同步處理更久。 只要 Azure AD 佈建服務在執行中，則後續同步處理大約每 40 分鐘進行一次。 

您可以使用 [同步處理詳細資料] 區段來監視進度，並循著連結取得佈建活動報告。 此報表會描述 Zscaler 上的 Azure AD 布建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../active-directory-saas-provisioning-reporting.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
