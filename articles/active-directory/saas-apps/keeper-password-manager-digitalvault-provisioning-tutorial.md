---
title: 教學課程：以 Azure Active Directory 設定 Keeper Password Manager & Digital Vault 來自動佈建使用者 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 將使用者帳戶自動佈建和取消佈建至 Keeper Password Manager & Digital Vault。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 2670dc0cb56805a2afa966bee1d2aa52b6c8e46a
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358968"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>教學課程：設定 Keeper Password Manager & Digital Vault 以自動佈建使用者

本教學課程旨在示範將 Azure AD 設定為可對 Keeper Password Manager & Digital Vault 自動佈建及取消佈建使用者和/或群組時，應在 Keeper Password Manager & Digital Vault 與 Azure Active Directory (Azure AD) 中執行的步驟。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [Keeper Password Manager & Digital Vault 租用戶](https://keepersecurity.com/pricing.html?t=e)
* Keeper Password Manager & Digital Vault 中具有管理員權限的使用者帳戶。

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>從資源庫新增 Keeper Password Manager & Digital Vault

在以 Azure AD 設定 Keeper Password Manager & Digital Vault 來自動佈建使用者之前，您必須從 Azure AD 應用程式庫將 Keeper Password Manager & Digital Vault 新增至受控 SaaS 應用程式的清單。

**若要從 Azure AD 應用程式庫新增 Keeper Password Manager & Digital Vault，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，選取 [Azure Active Directory]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取窗格頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中輸入 **Keeper Password Manager & Digital Vault**，並從結果面板中選取 [Keeper Password Manager & Digital Vault]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 Keeper Password Manager & Digital Vault](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>將使用者指派至 Keeper Password Manager & Digital Vault

Azure Active Directory 使用所謂「指派」的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動使用者佈建的內容中，只有已指派至 Azure AD 中應用程式的使用者和/或群組會進行同步處理。

設定並啟用自動使用者佈建之前，您應先決定 Azure AD 中的哪些使用者和/或群組需要存取 Keeper Password Manager & Digital Vault。 決定好之後，您就可以遵循此處的指示，將這些使用者和/或群組指派給 Keeper Password Manager & Digital Vault：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>將使用者指派至 Keeper Password Manager & Digital Vault 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 Keeper Password Manager & Digital Vault，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 Keeper Password Manager & Digital Vault 時，您必須在 [指派] 對話方塊中選取任何有效應用程式特有的角色 (如果有的話)。 具有 **預設存取** 角色的使用者會從佈建中排除。

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>設定將使用者自動佈建至 Keeper Password Manager & Digital Vault 

本節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 Keeper Password Manager & Digital Vault 中建立、更新和停用使用者和/或群組。

> [!TIP]
> 建議您選擇為 Keeper Password Manager & Digital Vault 啟用 SAML 型單一登入，方法是遵循 [Keeper Password Manager & Digital Vault 單一登入教學課程](keeperpasswordmanager-tutorial.md)中提供的指示。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>若要在 Azure AD 中設定 Keeper Password Manager & Digital Vault 的自動使用者佈建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 **Keeper Password Manager & Digital Vault**。

    ![應用程式清單中的 Keeper Password Manager & Digital Vault 連結](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![[管理] 選項的螢幕擷取畫面，並已指出 [佈建] 選項。](common/provisioning.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![[佈建模式] 下拉式清單的螢幕擷取畫面，並已指出 [自動] 選項。](common/provisioning-automatic.png)

5. 在 [管理員認證] 區段下，輸入 Keeper Password Manager & Digital Vault 帳戶的 [租用戶 URL] 和 [祕密權杖]，如步驟 6 所述。

6. 登入您的 [Keeper 管理主控台](https://keepersecurity.com/console/#login)。 按一下 [管理]，然後選取現有節點或建立新的節點。 瀏覽至 [佈建] 索引標籤，然後選取 [新增方法]。

    ![Keeper 管理主控台](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    選取 [SCIM (跨網域身分識別管理系統)]。

    ![Keeper 新增 SCIM](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    按一下 [建立佈建權杖]。

    ![Keeper 建立端點](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    複製 [URL] 和 [權杖] 的值，並將其貼入 Azure AD 中的 [租用戶 URL] 和 [秘密權杖]。 按一下 [儲存]，以完成 Keeper 上的佈建設定。

    ![Keeper 建立權杖](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. 如步驟 5 所示填入欄位後，按一下 [測試連線] 以確認 Azure AD 可連線至 Keeper Password Manager & Digital Vault。 如果連線失敗，請確定 Keeper Password Manager & Digital Vault 帳戶具有管理員權限，然後再試一次。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

8. 在 [通知電子郵件]  欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]  核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

9. 按一下 [檔案]  。

10. 在 [對應] 區段下，選取 [將 Azure Active Directory 使用者同步至 Keeper Password Manager & Digital Vault]。

    ![Keeper 使用者對應](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Keeper Password Manager & Digital Vault 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 Keeper Password Manager & Digital Vault 中的使用者帳戶，以進行更新作業。 選取 [儲存]  按鈕以認可所有變更。

    ![Keeper 使用者屬性](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. 在 [對應] 區段下，選取 [將 Azure Active Directory 群組同步至 Keeper Password Manager & Digital Vault]。

    ![Keeper 群組對應](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Keeper Password Manager & Digital Vault 的群組屬性。 選取為 [比對] 屬性的屬性會用來比對 Keeper Password Manager & Digital Vault 中的群組，以進行更新作業。 選取 [儲存]  按鈕以認可所有變更。

    ![Keeper 群組屬性](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

15. 若要對 Keeper Password Manager & Digital Vault 啟用 Azure AD 佈建服務，請在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

16. 透過在 [設定] 區段的 [範圍] 中選擇需要的值，可定義要佈建到 Keeper Password Manager & Digital Vault 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

17. 當您準備好要佈建時，按一下 [儲存]  。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]  區段的 [範圍]  中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [同步處理詳細資料] 區段來監視進度，並進入佈建活動報告的連結，以透過報告了解 Keeper Password Manager & Digital Vault 上的 Azure AD 佈建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

* Keeper Password Manager & Digital Vault 要求 [電子郵件] 和 [使用者名稱] 必須具有相同的來源值，因為只要對任一屬性進行更新都會修改到另一個屬性。
* Keeper Password Manager & Digital Vault 不支援使用者刪除，只支援停用。 在 Keeper 管理主控台 UI 中，已停用的使用者會顯示為已鎖定。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

