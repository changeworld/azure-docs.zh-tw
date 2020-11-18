---
title: 教學課程：以 Azure Active Directory 設定 Visitly 來自動佈建使用者 | Microsoft Docs
description: 了解如何將 Azure Active Directory 設定為可對 Visitly 自動佈建及取消佈建使用者帳戶。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: ff3f3ab65df2d801b7c962de7cce645e9fc00b30
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358604"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>教學課程：設定 Visitly 來自動佈建使用者

本教學課程旨在示範將 Azure AD 設定為可對 Visitly 自動佈建及取消佈建使用者或群組時，Visitly 與 Azure Active Directory (Azure AD) 中須執行的步驟。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題集的重要詳細資料，請參閱[使用 Azure Active Directory 對 軟體即服務 (SaaS) 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [Visitly 租用戶](https://www.visitly.io/pricing/)
* Visitly 中具有管理員權限的使用者帳戶

## <a name="assign-users-to-visitly"></a>將使用者指派給 Visitly 

Azure Active Directory 使用所謂「指派」的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動佈建使用者的情況下，只會同步處理 Azure AD 中已指派給應用程式的使用者或群組。

在設定並啟用自動佈建使用者之前，請決定 Azure AD 中有哪些使用者或群組需要存取 Visitly。 然後遵循此處的指示，將這些使用者或群組指派給 Visitly：
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>將使用者指派給 Visitly 的重要秘訣 

* 建議您將單一 Azure AD 使用者指派給 Visitly，以測試自動佈建使用者的設定。 稍後可以指派其他使用者或群組。

* 將使用者指派給 Visitly 時，您必須在指派對話方塊中選取任何有效的應用程式專屬角色 (如果有的話)。 具有預設存取角色的使用者會從佈建中排除。

## <a name="set-up-visitly-for-provisioning"></a>設定 Visitly 以進行佈建

使用 Azure AD 設定 Visitly 來自動佈建使用者之前，您必須在 Visitly 上啟用跨網域身分識別管理系統 (SCIM) 的佈建。

1. 登入 [Visitly](https://app.visitly.io/login)。 選取 [整合] > [主機同步]。

    ![主機同步](media/Visitly-provisioning-tutorial/login.png)

2. 選取 [Azure AD] 區段。

    ![Azure AD 區段](media/Visitly-provisioning-tutorial/integration.png)

3. 複製 **API 金鑰**。 這些值會輸入到 Azure 入口網站中 Visitly 應用程式 [佈建] 索引標籤中的 [祕密權杖] 方塊中。

    ![API 金鑰](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>從資源庫新增 Visitly

若要將 Visitly 設定為可使用 Azure AD 自動佈建使用者，請將 Visitly 從 Azure AD 應用程式庫新增至您的受控 SaaS 應用程式清單。

若要從 Azure AD 應用程式庫新增 Visitly，請遵循下列步驟。

1. 在 [Azure 入口網站](https://portal.azure.com) 的左導覽窗格中，選取 [Azure Active Directory]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取窗格頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Visitly**，在結果窗格中選取 [Visitly]，然後選取 [新增] 以新增應用程式。

    ![結果清單中的 Visitly](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>設定使用者自動佈建至 Visitly 

本節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者或群組指派，在 Visitly 中建立、更新和停用使用者或群組。

> [!TIP]
> 若要啟用 Visitly 的 SAML 型單一登入，請依照 [Visitly 單一登入教學課程](Visitly-tutorial.md)中的指示進行。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將這兩者分開設定。

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>在 Azure AD 中為 Visitly 設定自動使用者佈建

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式] > [所有應用程式]。

    ![所有應用程式](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Visitly]。

    ![應用程式清單中的 Visitly 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![佈建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![將 [佈建模式] 設定為 [自動]](common/provisioning-automatic.png)

5. 在 [管理員認證] 區段下，分別在 [租用戶 URL] 和 [秘密權杖] 中輸入稍早擷取到的 `https://api.visitly.io/v1/usersync/SCIM` 和 [API 金鑰] 值。 若要確保 Azure AD 可以連線到 Visitly，請選取 [測試連線]。 如果連線失敗，請確定您的 Visitly 帳戶具有管理員權限並再試一次。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件] 方塊中，輸入應收到佈建錯誤通知的個人或群組電子郵件地址。 選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]。

8. 在 [對應] 區段下，選取 [將 Azure Active Directory 使用者同步至 Visitly]。

    ![Visitly 使用者對應](media/visitly-provisioning-tutorial/usermapping.png)

9. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Visitly 的使用者屬性。 選取為 [比對] 屬性 (Property) 的屬性 (Attribute) 會用來比對 Visitly 中的使用者帳戶以進行更新作業。 選取 [儲存] 認可任何變更。

    ![Visitly 使用者屬性](media/visitly-provisioning-tutorial/userattribute.png)

10. 若要設定範圍篩選條件，請遵循[範圍篩選條件教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的指示。

11. 若要啟用 Visitly 的 Azure AD 佈建服務，在 [設定] 區段中，請將 [佈建狀態] 變更為 [開啟]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 透過在 [設定] 區段的 [範圍] 中選擇需要的值，可定義要佈建到 Visitly 的使用者或群組。

    ![佈建範圍](common/provisioning-scope.png)

13. 準備好要佈建時，請選取 [儲存]。

    ![儲存佈建設定](common/provisioning-configuration-save.png)

針對 [設定] 區段的 [範圍] 中定義的所有使用者或群組，此作業會啟動首次同步處理。 首次同步所花的時間會比後續同步更久。 如需佈建使用者或群組所需時間長度的詳細資訊，請參閱[佈建使用者需要多久時間？](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)。

您可以使用 [目前狀態] 區段來監視進度，並進入佈建活動報告的連結，以透過報告了解 Visitly 上的 Azure AD 佈建服務所執行的所有動作。 如需詳細資訊，請參閱[檢查使用者佈建的狀態](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 若要讀取 Azure AD 佈建記錄，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

Visitly 不支援實刪除。 所有項目都只會進行虛刪除。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
