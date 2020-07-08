---
title: 教學課程：使用 Azure Active Directory 設定 Wrike 來自動布建使用者 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory 將使用者帳戶自動布建和取消布建至 Wrike。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: 5dd4f5ac6152c22b5e2a84ecc0774672bcd5590b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77064184"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>教學課程：設定 Wrike 來自動布建使用者

本教學課程的目的是要示範您在 Wrike 和 Azure Active Directory （Azure AD）中執行的步驟，以設定 Azure AD 自動布建和取消布建使用者或群組至 Wrike。

> [!NOTE]
> 本教學課程說明建立在 Azure AD 使用者布建服務之上的連接器。 如需此服務的用途、運作方式和常見問題的重要詳細資料，請參閱[使用 Azure Active Directory 將使用者布建和取消布建至軟體即服務（SaaS）應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
>
> 此連接器目前為公開預覽版。 如需預覽功能之一般 Microsoft Azure 使用規定的詳細資訊，請參閱[Microsoft Azure 預覽的補充使用](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)規定。

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [Wrike 租使用者](https://www.wrike.com/price/)
* Wrike 中具有系統管理員許可權的使用者帳戶

## <a name="assign-users-to-wrike"></a>將使用者指派給 Wrike
Azure Active Directory 使用所謂「指派」的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動佈建使用者的情況下，只會同步處理 Azure AD 中已指派給應用程式的使用者或群組。

在您設定並啟用自動使用者布建之前，請先決定 Azure AD 中的哪些使用者或群組需要存取 Wrike。 然後遵循此處的指示，將這些使用者或群組指派給 Wrike：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>將使用者指派給 Wrike 的重要秘訣

* 建議您將單一 Azure AD 使用者指派給 Wrike，以測試自動使用者布建設定。 稍後可以指派其他使用者或群組。

* 當您將使用者指派給 Wrike 時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特定角色（如果有的話）。 具有預設存取角色的使用者會從佈建中排除。

## <a name="set-up-wrike-for-provisioning"></a>設定 Wrike 以提供布建

在您設定 Wrike 以 Azure AD 自動布建使用者之前，您必須啟用 [系統] 以在 Wrike 上進行跨網域身分識別管理（SCIM）布建。

1. 登入您的[Wrike 管理主控台](https://www.Wrike.com/login/)。 移至您的租使用者識別碼。 選取 [**應用程式 &** 整合]。

    ![應用程式 & 整合](media/Wrike-provisioning-tutorial/admin.png)

2.  移至**Azure AD**並加以選取。

    ![Azure AD](media/Wrike-provisioning-tutorial/Capture01.png)

3.  選取 [SCIM]。 複製**基底 URL**。

    ![基底 URL](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. 選取 [ **API**] [  >  **Azure SCIM**]。

    ![Azure SCIM](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  快顯視窗隨即開啟。 輸入您稍早建立的相同密碼來建立帳戶。

    ![Wrike 建立權杖](media/Wrike-provisioning-tutorial/password.png)

6.  複製**秘密權杖**，並將它貼入 Azure AD。 選取 [**儲存**] 以完成 Wrike 上的布建設定。

    ![永久存取權杖](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>從資源庫新增 Wrike

在您設定 Wrike 以 Azure AD 自動布建使用者之前，請先從 Azure AD 應用程式庫將 Wrike 新增至受控 SaaS 應用程式清單。

若要從 Azure AD 應用程式庫新增 Wrike，請遵循下列步驟。

1. 在[Azure 入口網站](https://portal.azure.com)的左側流覽窗格中，選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取窗格頂端的 [**新增應用程式**] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入**Wrike**，在結果面板中選取 [ **Wrike** ]，然後選取 [**新增**] 以新增應用程式。

    ![結果清單中的 Wrike](common/search-new-app.png)


## <a name="configure-automatic-user-provisioning-to-wrike"></a>設定自動使用者布建至 Wrike 

本節將引導您逐步設定 Azure AD 布建服務，以根據 Azure AD 中的使用者或群組指派，在 Wrike 中建立、更新和停用使用者或群組。

> [!TIP]
> 若要啟用 Wrike 的 SAML 型單一登入，請遵循[Wrike 單一登入教學](wrike-tutorial.md)課程中的指示。 單一登入可以與自動使用者布建分開設定，雖然這兩個功能彼此互補。

### <a name="configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>在 Azure AD 中設定自動使用者布建以進行 Wrike

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [**企業應用程式**] [  >  **所有應用程式**]。

    ![所有應用程式](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Wrike]****。

    ![應用程式清單中的 Wrike 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![佈建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![[布建模式] 設定為 [自動]](common/provisioning-automatic.png)

5. 在 [管理員認證] 區段下，分別輸入 [**租使用者 URL** ] 和 [**秘密權杖**] 中稍早取得的**基底 url**和**永久存取權杖**值。 選取 [**測試連接**] 以確保 Azure AD 可以連接到 Wrike。 如果連線失敗，請確定您的 Wrike 帳戶具有系統管理員許可權，然後再試一次。

    ![租使用者 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

7. 在 [**通知電子郵件**] 方塊中，輸入應收到布建錯誤通知之個人或群組的電子郵件地址。 選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

8. 選取 [儲存]。

9. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者至 Wrike**]。

    ![Wrike 使用者對應](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. 在 [**屬性**對應] 區段中，檢查從 Azure AD 同步處理到 Wrike 的使用者屬性。 選取為 [比對] 屬性**的屬性會**用來比對 Wrike 中的使用者帳戶，以進行更新作業。 選取 [儲存] 認可任何變更。

    ![Wrike 使用者屬性](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. 若要設定範圍篩選準則，請遵循[範圍篩選教學](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)課程中的指示。

12. 若要啟用 Wrike 的 Azure AD 布建服務，請在 [**設定**] 區段中將 [布建**狀態**] 變更為 [**開啟**]。

    ![布建狀態已切換為開啟](common/provisioning-toggle-on.png)

13. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建到 Wrike 的使用者或群組。

    ![佈建範圍](common/provisioning-scope.png)

14. 準備好要佈建時，請選取 [儲存]。

    ![正在儲存布建設定](common/provisioning-configuration-save.png)

針對 [設定] 區段的 [範圍] 中定義的所有使用者或群組，此作業會啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行。 如需使用者或群組布建多久時間的詳細資訊，請參閱布建[使用者需要](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)多久時間？。

您可以使用 [**目前狀態**] 區段來監視進度，並遵循 [布建活動報告] 的連結，其中描述 Wrike 上的 Azure AD 布建服務所執行的所有動作。 如需詳細資訊，請參閱[檢查使用者佈建的狀態](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 若要讀取 Azure AD 布建記錄，請參閱[關於自動使用者帳戶](../app-provisioning/check-status-user-account-provisioning.md)布建的報告。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
