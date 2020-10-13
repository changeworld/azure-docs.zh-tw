---
title: 教學課程：使用 Azure Active Directory 設定配置資源來自動布建使用者 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory，以將使用者帳戶自動布建和取消布建至配置資源。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 09/12/2019
ms.author: Zhchia
ms.openlocfilehash: e69b6aa5518f83dbbeba34d70b459c781b50dc9f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91299899"
---
# <a name="tutorial-configure-robin-for-automatic-user-provisioning"></a>教學課程：設定自動使用者布建的配置資源

本教學課程的目的是要示範如何在配置資源中執行的步驟，以及 Azure Active Directory (Azure AD) 將 Azure AD 設定為自動布建和解除布建使用者和/或群組至配置資源。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [配置資源租使用者](https://robinpowered.com/pricing/)
* 具有系統管理員許可權的使用者帳戶。

## <a name="assigning-users-to-robin"></a>將使用者指派給資源配置

Azure Active Directory 使用所謂「指派」的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動使用者布建的內容中，只會同步處理在 Azure AD 中指派給應用程式的使用者和/或群組。

在設定並啟用自動使用者布建之前，您應該決定 Azure AD 中的哪些使用者及/或群組需要存取配置資源。 一旦決定後，您可以遵循此處的指示，將這些使用者和/或群組指派給配置資源：
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-robin"></a>將使用者指派給配置資源的重要秘訣

* 建議將單一 Azure AD 使用者指派給配置資源，以測試自動使用者布建設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給「資源配置」時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特定角色 (如果有) 可用的話。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="set-up-robin-for-provisioning"></a>設定布建的配置資源

1. 登入您的配置資源 [管理主控台](https://dashboard.robinpowered.com/login)。 流覽以 **管理 > 整合 > SCIM > 管理**。

    ![配置資源管理主控台](media/robin-provisioning-tutorial/robin-admin.png)

2.  產生新的組織權杖。 如果您遺失此權杖，您永遠可以建立一個新的權杖，而不會影響現有的使用者。

    ![配置資源支援的新增 SCIM](media/robin-provisioning-tutorial/robin-token.png)

3.  複製 **SCIM Authentication Token**。 在 Azure 入口網站中，您配置資源應用程式的 [布建] 索引標籤中，將會在 [秘密權杖] 欄位中輸入此值。



## <a name="add-robin-from-the-gallery"></a>從資源庫新增資源庫

在設定 Azure AD 的自動使用者布建的配置資源之前，您必須從 Azure AD 應用程式資源庫將資源庫新增至受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式資源庫新增配置資源，請執行下列步驟：**

1. 在 [ **[Azure 入口網站](https://portal.azure.com)** 的左側導覽面板中，選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要加入新的應用程式，請選取窗格頂端的 [ **新增應用程式** ] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入配置資源，在結果窗格**中選取 [** 配置資源 **]，然後**按一下 [**新增**] 按鈕以新增應用程式。

    ![結果清單中的資源資源](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-robin"></a>設定自動使用者布建至配置資源 

本節將引導您逐步設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和/或群組指派，在配置資源中建立、更新和停用使用者和/或群組。

> [!TIP]
> 您也可以選擇為「配置資源」啟用 SAML 型單一登入，請遵循「配置資源 [單一登入」教學](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial)課程中提供的指示。 您可以獨立設定自動使用者布建的單一登入，雖然這兩個功能彼此補充

### <a name="to-configure-automatic-user-provisioning-for-robin-in-azure-ad"></a>若要在 Azure AD 中設定配置資源的自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Robin]****。

    ![應用程式清單中的配置資源支援連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![已呼叫 [布建] 選項的 [管理選項] 螢幕擷取畫面。](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![[布建模式] 下拉式清單的螢幕擷取畫面，其中已呼叫 [自動] 選項。](common/provisioning-automatic.png)

5. 在 [ **管理員認證** ] 區段下的 [ `https://api.robinpowered.com/v1.0/scim-2` **租使用者 URL**] 中輸入。 輸入稍早在**秘密權杖**中取出的**SCIM Authentication 權杖**值。 按一下 [ **測試連接** ]，以確保 Azure AD 可以連線至配置資源。 如果連接失敗，請確定您的配置資源帳戶具有系統管理員許可權，然後再試一次。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 **[儲存]** 。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者至**配置資源]。

    ![配置資源支援的使用者對應](media/robin-provisioning-tutorial/robin-user-mapping.png)

9. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理至配置資源的使用者屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對 [配置資源] 中的使用者帳戶，以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![配置資源支援的使用者屬性](media/robin-provisioning-tutorial/robin-user-attribute-mapping.png)

10. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 群組至**配置資源]。

    ![配置資源支援的群組對應](media/robin-provisioning-tutorial/robin-group-mapping.png)

11. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理至配置資源的群組屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對更新作業的 [配置資源] 群組中的群組。 選取 [儲存] 按鈕以認可所有變更。

    ![配置資源支援的群組屬性](media/robin-provisioning-tutorial/robin-group-attribute-mapping.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要為配置資源啟用 Azure AD 布建服務，請在 [**設定**] 區段中，將布建**狀態**變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建至配置資源的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [ **同步處理詳細資料** ] 區段來監視進度，並遵循連結來布建活動報告，該報告會描述配置資源上的 Azure AD 布建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。



## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

