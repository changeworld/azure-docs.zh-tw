---
title: 教學課程：使用 Azure Active Directory 設定 Zscaler Beta 以進行自動使用者布建 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory 以自動布建和取消布建使用者帳戶至 Zscaler Beta。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 83db6b8d-503b-48f3-b918-f9fba1369d53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 505cd7a3350c937885f0f03268ef326a4f784258
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062722"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>教學課程：設定 Zscaler Beta 來自動布建使用者

本教學課程的目的是要示範要在 Zscaler Beta 和 Azure Active Directory （Azure AD）中執行的步驟，以設定 Azure AD 自動布建和取消布建使用者和/或群組至 Zscaler Beta。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../active-directory-saas-app-provisioning.md)。
>


## <a name="prerequisites"></a>必要條件

本教學課程中說明的案例假設您已經具有下列項目：

* Azure AD 租用戶
* Zscaler 搶鮮版（Beta）租使用者
* Zscaler Beta 中具有系統管理員許可權的使用者帳戶

> [!NOTE]
> Azure AD 布建整合依賴 Zscaler 搶鮮版（Beta） SCIM API，其適用于具有企業封裝之帳戶的 Zscaler Beta 版開發人員。

## <a name="adding-zscaler-beta-from-the-gallery"></a>從資源庫新增 Zscaler Beta

設定 Zscaler Beta 以 Azure AD 自動布建使用者之前，您需要從 Azure AD 應用程式庫將 Zscaler Beta 新增至受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式庫新增 Zscaler Beta，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中輸入 **Zscaler Beta**，並從結果面板中選取 [Zscaler Beta]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 Zscaler Beta](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>將使用者指派給 Zscaler Beta

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和/或群組會進行同步處理。

在設定並啟用自動使用者布建之前，您應該決定 Azure AD 中的哪些使用者和/或群組需要 Zscaler Beta 的存取權。 一旦決定後，您可以遵循此處的指示，將這些使用者和/或群組指派給 Zscaler Beta：

* [將使用者或群組指派給企業應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>將使用者指派給 Zscaler Beta 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 Zscaler Beta，以測試自動使用者布建設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 Zscaler Beta 時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特定角色（如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>設定自動使用者布建至 Zscaler Beta

本節將引導您逐步設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和/或群組指派，在 Zscaler Beta 中建立、更新和停用使用者和/或群組。

> [!TIP]
> 您也可以選擇啟用 Zscaler Beta 的 SAML 型單一登入，請遵循[Zscaler Beta 單一登入教學](zscaler-beta-tutorial.md)課程中提供的指示。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>若要在 Azure AD 中設定 Zscaler Beta 的自動使用者布建：

1. 登入[Azure 入口網站](https://portal.azure.com)並選取 [**企業應用程式**]，選取 [**所有應用程式**]，然後選取 [ **Zscaler Beta**]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Zscaler Beta]。

    ![應用程式清單中的 Zscaler Beta 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![Zscaler Beta 提供](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![Zscaler Beta 提供](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. 在 [**管理員認證**] 區段下，輸入 Zscaler Beta 帳戶的 [**租使用者 URL** ] 和 [**秘密權杖**]，如步驟6所述。

6. 若要取得**租使用者 URL**和**密碼權杖**，請流覽至 Zscaler Beta 版入口網站使用者介面中的 [系統**管理] > 驗證設定**，然後按一下 [**驗證類型**] 底下的 [ **SAML** ]。

    ![Zscaler Beta 提供](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    按一下 [**設定 saml** ] 以開啟 [設定**saml**選項]。

    ![Zscaler Beta 提供](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    選取 [**啟用以 SCIM 為基礎的布建**] 來抓取**基底 URL**和**持有人權杖**，然後儲存設定。 將**基底 url**複製到 [**租使用者 URL**]，並將 [**持有人權杖**] 複製到 Azure 入口網站中的**秘密權杖**。

7. 填入步驟5所示的欄位之後，按一下 [**測試**連線] 以確保 Azure AD 可以連接到 Zscaler Beta。 如果連線失敗，請確定您的 Zscaler Beta 帳戶具有系統管理員許可權，然後再試一次。

    ![Zscaler Beta 提供](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. 在 [通知電子郵件] 欄位中，輸入應收到佈建錯誤通知的個人或群組之電子郵件地址，然後勾選 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![Zscaler Beta 提供](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Haga clic en **Guardar**.

10. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者至 Zscaler Beta**]。

    ![Zscaler Beta 提供](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步至 Zscaler Beta 的使用者屬性。 選取為 [比對] 屬性**的屬性會**用來比對 Zscaler Beta 中的使用者帳戶，以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![Zscaler Beta 提供](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 群組至 Zscaler Beta**]。

    ![Zscaler Beta 提供](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步至 Zscaler Beta 的群組屬性。 選取為 [比對] 屬性**的屬性會**用來比對 Zscaler Beta 中的群組以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![Zscaler Beta 提供](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. 若要設定範圍篩選，請參閱[範圍篩選教學課程](./../active-directory-saas-scoping-filters.md)中提供的下列指示。

15. 若要啟用 Zscaler Beta 的 Azure AD 布建服務，請在 [**設定**] 區段中，將 [布建**狀態**] 變更為 [**開啟**]。

    ![Zscaler Beta 提供](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建到 Zscaler Beta 的使用者和/或群組。

    ![Zscaler Beta 提供](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. 當您準備好要佈建時，按一下 [儲存]。

    ![Zscaler Beta 提供](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

此作業會對在 [設定] 區段的 [範圍] 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [**同步處理詳細資料**] 區段來監視進度，並遵循連結來布建活動報告，其中描述 Zscaler Beta 上的 Azure AD 布建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../active-directory-saas-provisioning-reporting.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png
