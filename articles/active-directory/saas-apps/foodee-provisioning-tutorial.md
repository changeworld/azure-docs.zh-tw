---
title: 教學課程：使用 Azure Active Directory 設定 Foodee 來自動布建使用者 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory，以將使用者帳戶自動布建和取消布建至 Foodee。
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
ms.openlocfilehash: 40549fa8880fb4dc4d29046b0037621610f0b130
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544177"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>教學課程：設定 Foodee 來自動布建使用者

本文說明如何在 Foodee 和 Azure AD 中設定 Azure Active Directory (Azure AD) ，以將使用者或群組自動布建或取消布建至 Foodee。

> [!NOTE]
> 本文說明建立在 Azure AD 使用者布建服務之上的連接器。 若要瞭解此服務的功能及其運作方式，以及取得常見問題的解答，請參閱 [使用 Azure Active Directory 自動進行 SaaS 應用程式的使用者](../app-provisioning/user-provisioning.md)布建和解除布建。
>
> 此連接器目前為預覽版。 如需預覽功能的「Azure 使用條款」功能的詳細資訊，請參閱 [Microsoft Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

本教學課程假設您已符合下列必要條件：

* Azure AD 租用戶
* [Foodee 租使用者](https://www.food.ee/about/)
* Foodee 中具有系統管理員許可權的使用者帳戶

## <a name="assign-users-to-foodee"></a>將使用者指派給 Foodee 

Azure AD 使用稱為「 *指派* 」的概念，來判斷哪些使用者應該會收到所選應用程式的存取權。 在自動使用者布建的內容中，只會同步處理在 Azure AD 中指派給應用程式的使用者或群組。

設定並啟用自動使用者布建之前，您應該決定 Azure AD 中的哪些使用者或群組需要存取 Foodee。 進行這項決定之後，您可以依照 [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)中的指示，將這些使用者或群組指派給 Foodee。

## <a name="important-tips-for-assigning-users-to-foodee"></a>將使用者指派給 Foodee 的重要秘訣 

當您要指派使用者時，請記住下列秘訣：

* 建議您只將單一 Azure AD 使用者指派給 Foodee，以測試自動使用者布建的設定。 您可以稍後再指派其他使用者或群組。

* 當您將使用者指派給 Foodee 時，請在 [ **指派** ] 窗格中選取任何有效的應用程式特有角色（如果有的話）。 具有 *預設存取* 角色的使用者會從布建中排除。

## <a name="set-up-foodee-for-provisioning"></a>設定布建的 Foodee

使用 Azure AD 設定 Foodee 來自動布建使用者之前，您必須在 Foodee 中啟用 (SCIM) 布建的系統，以進行跨網域身分識別管理。

1. 登入 [Foodee](https://www.food.ee/login/)，然後選取您的租使用者識別碼。

    ![Foodee](media/Foodee-provisioning-tutorial/tenant.png)

1. 在 [ **企業版入口網站**] 下，選取 [ **單一登入**]。

    ![Foodee 企業版入口網站左窗格功能表](media/Foodee-provisioning-tutorial/scim.png)

1. 複製 [ **API 權杖** ] 方塊中的值以供稍後使用。 您將在 Azure 入口網站中 Foodee 應用程式的 [布**建] 索引**標籤中，于 [**秘密權杖**] 方塊中輸入它。

    ![Foodee](media/Foodee-provisioning-tutorial/token.png)

## <a name="add-foodee-from-the-gallery"></a>從資源庫新增 Foodee

若要使用 Azure AD 設定 Foodee 來自動布建使用者，您需要從 Azure AD 應用程式資源庫將 Foodee 新增至受控 SaaS 應用程式清單。

若要從 Azure AD 應用程式庫新增 Foodee，請執行下列動作：

1. 在 [ [Azure 入口網站](https://portal.azure.com)] 的左窗格中，選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 命令](common/select-azuread.png)

1. 選取**企業應用**程式  >  的**所有應用程式**。

    ![[企業應用程式] 窗格](common/enterprise-applications.png)

1. 若要加入新的應用程式，請選取窗格頂端的 [ **新增應用程式** ]。

    ![新增應用程式按鈕](common/add-new-app.png)

1. 在搜尋方塊中，輸入 **Foodee**，在 [結果] 窗格中選取 [ **Foodee** ]，然後選取 [ **新增** ] 以新增應用程式。

    ![結果清單中的 Foodee](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>設定自動使用者布建至 Foodee 

在本節中，您會設定 Azure AD 布建服務，以根據 Azure AD 中的使用者或群組指派，在 Foodee 中建立、更新及停用使用者或群組。

> [!TIP]
> 您也可以遵循 [Foodee 單一登入教學](Foodee-tutorial.md)課程中的指示，啟用 FOODEE 的 SAML 型單一登入。 您可以設定與自動使用者布建無關的單一登入，雖然這兩個功能彼此互補。

藉由執行下列動作，在 Azure AD 中為 Foodee 設定自動使用者布建：

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，選取 [**企業應用**程式  >  **所有應用程式**]。

    ![企業應用程式窗格](common/enterprise-applications.png)

1. 在 **應用程式** 清單中，選取 [ **Foodee**]。

    ![應用程式清單中的 Foodee 連結](common/all-applications.png)

1. 選取 [佈建] 索引標籤。

    ![佈建索引標籤](common/provisioning.png)

1. 在 [布建 **模式]** 下拉式清單中，選取 [ **自動**]。

    ![佈建索引標籤](common/provisioning-automatic.png)

1. 在 [系統 **管理員認證**] 下，執行下列動作：

   a. 在 [ **租使用者 URL** ] 方塊中，輸入您稍早取出的 **HTTPs： \/ /concierge.food.ee/scim/v2** 值。

   b. 在 [ **秘密權杖** ] 方塊中，輸入您稍早取出的 **API 權杖** 值。
   
   c. 若要確保 Azure AD 可以連接到 Foodee，請選取 [ **測試連接**]。 如果連接失敗，請確定您的 Foodee 帳戶具有系統管理員許可權，然後再試一次。

    ![測試連接連結](common/provisioning-testconnection-tenanturltoken.png)

1. 在 [ **通知電子郵件** ] 方塊中，輸入應收到布建錯誤通知之個人或群組的電子郵件地址，然後選取 [ **發生失敗時傳送電子郵件通知** ] 核取方塊。

    ![通知電子郵件文字方塊](common/provisioning-notification-email.png)

1. 選取 [儲存]。

1. 在 **[** 對應] 底下，選取 [ **同步處理 Azure Active Directory 使用者至 Foodee**]。

    ![Foodee 使用者對應](media/Foodee-provisioning-tutorial/usermapping.png)

1. 在 [ **屬性**對應] 下，檢查從 Azure AD 同步處理到 Foodee 的使用者屬性。 選取為 [比對] **屬性的屬性會** 用來比對 Foodee 中的 *使用者帳戶* ，以進行更新作業。 

    ![Foodee 使用者對應](media/Foodee-provisioning-tutorial/userattribute.png)

1. 若要認可您的變更，請選取 [ **儲存**]。
1. 在 [對應] **底下，選取**[ **同步處理 Azure Active Directory 群組至 Foodee**]。

    ![Foodee 使用者對應](media/Foodee-provisioning-tutorial/groupmapping.png)

1. 在 [ **屬性**對應] 下，檢查從 Azure AD 同步處理到 Foodee 的使用者屬性。 選取為 [比對] **屬性的屬性會** 用來比對 Foodee 中的 *群組帳戶* 以進行更新作業。

    ![Foodee 使用者對應](media/Foodee-provisioning-tutorial/groupattribute.png)

1. 若要認可您的變更，請選取 [ **儲存**]。
1. 設定範圍篩選器。 若要深入瞭解，請參閱 [範圍篩選器教學](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)課程中的指示。

1. 若要為 Foodee 啟用 Azure AD 布建服務，請在 [ **設定** ] 區段中，將 [布建 **狀態** ] 變更為 [ **開啟**]。

    ![布建狀態參數](common/provisioning-toggle-on.png)

1. 在 [ **設定**] 底下的 [ **領域** ] 下拉式清單中，定義您想要布建到 Foodee 的使用者或群組。

    ![[布建領域] 下拉式清單](common/provisioning-scope.png)

1. 準備好要佈建時，請選取 [儲存]。

    ![布建配置儲存按鈕](common/provisioning-configuration-save.png)

上述作業會針對您在 [ **領域** ] 下拉式清單中定義的使用者或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行。 如需詳細資訊，請參閱布建 [使用者需要多久](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)的時間？。

您可以使用 [ **目前狀態** ] 區段來監視進度，並遵循您布建活動報告的連結。 報表會描述 Foodee 上 Azure AD 布建服務所執行的所有動作。 如需詳細資訊，請參閱[檢查使用者佈建的狀態](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 若要讀取 Azure AD 布建記錄，請參閱 [有關自動布建使用者帳戶的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
