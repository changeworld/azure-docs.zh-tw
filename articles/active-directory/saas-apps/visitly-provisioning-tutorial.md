---
title: 教學課程：使用 Azure Active Directory 設定 Visitly 來自動布建使用者 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory，以將使用者帳戶自動布建和取消布建至 Visitly。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 15031a3e139265410179baa13db9ed3f1b41cf17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88531798"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>教學課程：設定 Visitly 來自動布建使用者

本教學課程的目的是要示範您在 Visitly 和 Azure Active Directory (中執行的步驟，Azure AD) 將 Azure AD 設定為自動布建和取消布建要 Visitly 的使用者或群組。

> [!NOTE]
> 本教學課程說明建立在 Azure AD 使用者布建服務之上的連接器。 如需此服務的用途、運作方式和常見問題的重要詳細資訊，請參閱 [使用 Azure Active Directory 將使用者布建和解除布建至軟體即服務 (SaaS) 應用程式](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需預覽功能的一般 Microsoft Azure 使用條款的詳細資訊，請參閱 [Microsoft Azure 預覽的補充使用](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)規定。

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [Visitly 租使用者](https://www.visitly.io/pricing/)
* Visitly 中具有系統管理員許可權的使用者帳戶

## <a name="assign-users-to-visitly"></a>將使用者指派給 Visitly 

Azure Active Directory 使用所謂「指派」的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動佈建使用者的情況下，只會同步處理 Azure AD 中已指派給應用程式的使用者或群組。

設定並啟用自動使用者布建之前，請先決定 Azure AD 中的哪些使用者或群組需要存取 Visitly。 然後依照此處的指示，將這些使用者或群組指派給 Visitly：
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>將使用者指派給 Visitly 的重要秘訣 

* 建議您將單一 Azure AD 使用者指派給 Visitly，以測試自動使用者布建設定。 您可以稍後再指派其他使用者或群組。

* 當您將使用者指派給 Visitly 時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特定角色 (如果有的話) 。 具有預設存取角色的使用者會從佈建中排除。

## <a name="set-up-visitly-for-provisioning"></a>設定布建的 Visitly

使用 Azure AD 設定 Visitly 來自動布建使用者之前，您必須在 Visitly 上啟用系統以進行跨網域身分識別管理 (SCIM) 布建。

1. 登入 [Visitly](https://app.visitly.io/login)。 選取**Integrations**  >  **[整合主機同步**處理]。

    ![主機同步處理](media/Visitly-provisioning-tutorial/login.png)

2. 選取 **Azure AD** 區段。

    ![Azure AD 區段](media/Visitly-provisioning-tutorial/integration.png)

3. 複製 **API 金鑰**。 這些值會在 Azure 入口網站中您 Visitly 應用程式的 [布**建] 索引**標籤上的 [**秘密權杖**] 方塊中輸入。

    ![API 金鑰](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>從資源庫新增 Visitly

若要使用 Azure AD 設定 Visitly 來自動布建使用者，請從 Azure AD 應用程式資源庫將 Visitly 新增至受控 SaaS 應用程式清單。

若要從 Azure AD 應用程式庫新增 Visitly，請遵循下列步驟。

1. 在 [ [Azure 入口網站](https://portal.azure.com)] 的左方流覽窗格中，選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要加入新的應用程式，請選取窗格頂端的 [ **新增應用程式** ] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Visitly**，在 [結果] 面板中選取 [ **Visitly** ]，然後選取 [ **新增** ] 以新增應用程式。

    ![結果清單中的 Visitly](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>設定自動使用者布建至 Visitly 

本節將引導您完成設定 Azure AD 布建服務，以根據 Azure AD 中的使用者或群組指派，在 Visitly 中建立、更新及停用使用者或群組的步驟。

> [!TIP]
> 若要啟用 Visitly 的 SAML 型單一登入，請遵循 [Visitly 單一登入教學](Visitly-tutorial.md)課程中的指示。 您可以獨立設定自動使用者布建的單一登入，雖然這兩個功能彼此互補。

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>在 Azure AD 中為 Visitly 設定自動使用者布建

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取**企業應用**程式  >  的**所有應用程式**。

    ![所有應用程式](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Visitly]****。

    ![應用程式清單中的 Visitly 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![佈建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![布建模式設定為自動](common/provisioning-automatic.png)

5. 在 [管理員認證] 區段底下， `https://api.visitly.io/v1/usersync/SCIM` 分別輸入**租使用者 URL**和**秘密權杖**中稍早抓取的和**API 金鑰**值。 選取 [ **測試連接** ] 以確保 Azure AD 可以連線至 Visitly。 如果連接失敗，請確定您的 Visitly 帳戶具有系統管理員許可權，然後再試一次。

    ![租使用者 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [ **通知電子郵件** ] 方塊中，輸入應收到布建錯誤通知之個人或群組的電子郵件地址。 選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]****。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者至 Visitly**]。

    ![Visitly 使用者對應](media/visitly-provisioning-tutorial/usermapping.png)

9. 在 [ **屬性** 對應] 區段中，檢查從 Azure AD 同步處理到 Visitly 的使用者屬性。 選取為 [比對 **] 屬性的屬性會** 用來比對 Visitly 中的使用者帳戶以進行更新作業。 選取 [儲存]  認可任何變更。

    ![Visitly 使用者屬性](media/visitly-provisioning-tutorial/userattribute.png)

10. 若要設定範圍篩選器，請遵循 [範圍篩選器教學](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)課程中的指示。

11. 若要啟用 Visitly Azure AD 的布建服務，請在 [**設定**] 區段中，將 [布建**狀態**] 變更為 [**開啟**]。

    ![布建狀態已切換開啟](common/provisioning-toggle-on.png)

12. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建到 Visitly 的使用者或群組。

    ![佈建範圍](common/provisioning-scope.png)

13. 準備好要佈建時，請選取 [儲存]。

    ![正在儲存布建設定](common/provisioning-configuration-save.png)

針對 [設定] 區段的 [範圍] 中定義的所有使用者或群組，此作業會啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行。 如需布建使用者或群組所需的時間長度的詳細資訊，請參閱布建使用者需要多久的時間 [？](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)。

您可以使用 [ **目前狀態** ] 區段來監視進度，並遵循布建活動報告的連結，其中描述 Visitly 上的 Azure AD 布建服務所執行的所有動作。 如需詳細資訊，請參閱[檢查使用者佈建的狀態](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 若要讀取 Azure AD 布建記錄，請參閱 [有關自動布建使用者帳戶的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

Visitly 不支援實刪除。 所有專案都只是虛刪除。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
