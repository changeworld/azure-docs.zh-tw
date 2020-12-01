---
title: 教學課程：以 Azure Active Directory 設定 Zscaler Beta 來自動佈建使用者 | Microsoft Docs
description: 了解如何將 Azure Active Directory 設定為可對 Zscaler Beta 自動佈建及取消佈建使用者帳戶。
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
ms.openlocfilehash: 59784b6e618e875a6eedc947ce75afdf3bd1df74
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327384"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>教學課程：設定 Zscaler Beta 來自動佈建使用者

本教學課程旨在示範將 Azure AD 設定為可對 Zscaler Beta 自動佈建及取消佈建使用者和/或群組時，Zscaler Beta 與 Azure Active Directory (Azure AD) 中須執行的步驟。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>


## <a name="prerequisites"></a>必要條件

本教學課程中說明的案例假設您已經具有下列項目：

* Azure AD 租用戶
* Zscaler Beta 租用戶
* Zscaler Beta 中具有管理員權限的使用者帳戶

> [!NOTE]
> Azure AD 佈建整合需仰賴 Zscaler Beta SCIM API，此 API 可供 Zscaler Beta 開發人員用於具有「企業」套件的帳戶。

## <a name="adding-zscaler-beta-from-the-gallery"></a>從資源庫新增 Zscaler Beta

將 Zscaler Beta 設定為可使用 Azure AD 自動佈建使用者之前，您必須將 Zscaler Beta 從 Azure AD 應用程式庫新增至您的受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式庫新增 Zscaler Beta，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中輸入 **Zscaler Beta**，並從結果面板中選取 [Zscaler Beta]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 Zscaler Beta](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>將使用者指派給 Zscaler Beta

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和/或群組會進行同步處理。

設定並啟用自動使用者佈建之前，您應先決定 Azure AD 中的哪些使用者和/或群組需要存取 Zscaler Beta。 一旦決定後，您可以依照此處的指示，將這些使用者和/或群組指派給 Zscaler Beta：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>將使用者指派給 Zscaler Beta 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 Zscaler Beta，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 Zscaler Beta 時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特有角色 (如果有的話)。 具有 **預設存取** 角色的使用者會從佈建中排除。

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>對 Zscaler Beta 設定自動使用者佈建

本節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 Zscaler Beta 中建立、更新和停用使用者和/或群組。

> [!TIP]
> 建議您選擇為 Zscaler Beta 啟用 SAML 型單一登入，請遵循 [Zscaler Beta 單一登入教學課程](zscaler-beta-tutorial.md)中提供的指示。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>若要在 Azure AD 中為 Zscaler Beta 設定自動使用者佈建：

1. 登入 [Azure 入口網站](https://portal.azure.com)，依序選取 [企業應用程式]、[所有應用程式] 及 [Zscaler Beta]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Zscaler Beta]。

    ![應用程式清單中的 Zscaler Beta 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![其中有整理成類別的索引標籤清單，並以「ZScaler Beta - 佈建/企業應用程式」作為標題。 已選取 [管理] 類別的 [佈建] 索引標籤。](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![已從 [佈建模式] 下拉式清單中選取 [自動] 模式。 其中有一些屬於管理員認證的欄位，用來連線到 Zscaler Beta 的 API，此外還有一個 [測試連接] 按鈕。](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. 在 [管理員認證] 區段下，輸入 Zscaler Beta 帳戶的 [租用戶 URL] 和 [祕密權杖]，如步驟 6 所述。

6. 若要取得 [租用戶 URL] 和 [祕密權杖]，請瀏覽至 Zscaler Beta 入口網站使用者介面中的 [管理] > [驗證設定]，然後按一下 [驗證類型] 下方的 [SAML]。

    ![在驗證設定的驗證設定檔中，選取的目錄類型為 [託管 DB]，而選取的驗證類型為 [SAML]。](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    按一下 [設定 SAML] 以開啟 [設定 SAML] 選項。

    ![在 [設定 SAML] 上，[啟用 SAML 自動佈建] 和 [啟用 SCIM 型佈建] 選項皆已選取。 [基底 URL] 和 [持有人權杖] 文字方塊會醒目提示。](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    選取 [啟用 SCIM 型佈建] 並擷取 [基底 URL] 和 [持有人權杖]，然後儲存設定。 在 Azure 入口網站中，將 **基底 URL** 複製到 [租用戶 URL]，然後將 **持有人權杖** 複製到 [祕密權杖]。

7. 如步驟 5 所示填寫欄位後，按一下 [測試連線] 以確認 Azure AD 可以連線到 Zscaler Beta。 如果連線失敗，請確定您的 Zscaler Beta 帳戶具有管理員權限並再試一次。

    ![[管理員認證] 上的 [租用戶 URL] 和 [祕密權杖] 欄位具有值，且醒目提示 [測試連線] 按鈕。](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. 在 [通知電子郵件] 欄位中，輸入應收到佈建錯誤通知的個人或群組之電子郵件地址，然後勾選 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![[通知電子郵件] 文字方塊是空的，而且已清除 [發生失敗時傳送電子郵件通知] 核取方塊。](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. 按一下 [儲存]。

10. 在 [對應] 區段底下，選取 [將 Azure Active Directory 使用者同步至 Zscaler Beta]。

    ![已選取並啟用 [將 Azure Active Directory 使用者同步至 ZScalerBeta]。](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Zscaler Beta 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 Zscaler Beta 中的使用者帳戶以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![在使用者屬性的 [屬性對應] 區段中，Active Directory 屬性會顯示在與其同步的 Zscalar Beta 屬性旁邊。 一對屬性顯示為「相符」。](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. 在 [對應] 區段底下，選取 [將 Azure Active Directory 群組同步至 Zscaler Beta]。

    ![已選取並啟用 [將 Azure Active Directory 群組同步至 ZScalerBeta]。](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Zscaler Beta 的群組屬性。 選取為 [比對] 屬性的屬性會用來比對 Zscaler Beta 中的群組以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![在群組屬性的 [屬性對應] 區段中，Active Directory 屬性會顯示在與其同步的 Zscalar Beta 屬性旁邊。 一對屬性顯示為「相符」。](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

15. 若要啟用 Zscaler Beta 的 Azure AD 佈建服務，在 [設定]區段中，將 [佈建狀態] 變更為 [開啟]。

    ![[佈建狀態] 會顯示並設定為 [開啟]。](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. 透過在 [設定] 區段的 [範圍] 中選擇需要的值，可定義要佈建到 Zscaler Beta 的使用者和/或群組。

    ![[範圍] 下拉式清單隨即顯示，並且選取 [只同步已指派的使用者和群組]。 另一個可用的值為 [同步所有使用者和群組]。](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. 當您準備好要佈建時，按一下 [儲存]  。

    ![[Zscaler Beta - 佈建] 頂端的 [儲存] 按鈕會醒目提示。 另外還有一個 [捨棄] 按鈕。](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

此作業會對在 [設定]  區段的 [範圍]  中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [同步詳細資料] 區段來監視進度，並遵循連結來取得佈建活動報告，當中會描述 Azure AD 佈建服務在 Zscaler Beta 上執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png