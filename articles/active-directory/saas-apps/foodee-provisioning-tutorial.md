---
title: 教學課程：使用 Azure Active Directory 設定 Foodee 來自動佈建使用者 | Microsoft Docs
description: 了解如何將 Azure Active Directory 設定為可對 Foodee 自動佈建及取消佈建使用者帳戶。
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
ms.openlocfilehash: 8b4bfa7e9bf457d79c6c4a0b5255bce4fe36dff4
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358672"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>教學課程：設定 Foodee 來自動佈建使用者

本文說明如何設定 Foodee 中的 Azure Active Directory (Azure AD) 和 Azure AD，以自動對 Foodee 佈建或取消佈建使用者或群組。

> [!NOTE]
> 本文將描述以 Azure AD 使用者佈建服務為基礎的連接器。 若要了解此服務的用途、運作方式和常見問題的答案，請參閱[使用 Azure Active Directory 自動對 SaaS 應用程式進行和取消使用者的佈建](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為預覽版。 如需有關預覽功能的 Azure 使用規定功能詳細資訊，請移至 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

本教學課程假設您已符合下列必要條件：

* Azure AD 租用戶
* [Foodee 租用戶](https://www.food.ee/about/)
* Foodee 中具有管理員權限的使用者帳戶

## <a name="assign-users-to-foodee"></a>將使用者指派給 Foodee 

Azure AD 會使用稱為「指派」的概念，來判斷哪些使用者應接收指定應用程式的存取權。 在自動使用者佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者或群組會進行同步處理。

設定並啟用自動使用者佈建之前，您應該先決定 Azure AD 中的哪些使用者或群組需要存取 Foodee。 做好決定之後，即可依照[將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)中的指示，將這些使用者或群組指派給 Foodee。

## <a name="important-tips-for-assigning-users-to-foodee"></a>將使用者指派給 Foodee 的重要秘訣 

當您要指派使用者時，請記住下列秘訣：

* 建議您只將單一 Azure AD 使用者指派給 Foodee，以測試自動使用者佈建的設定。 您可以稍後再指派其他使用者或群組。

* 當您將使用者指派給 Foodee 時，您必須在 [指派] 窗格中選取任何有效的應用程式特有角色 (如果有的話)。 具有「預設存取」角色的使用者會從佈建中排除。

## <a name="set-up-foodee-for-provisioning"></a>設定 Foodee 以進行佈建

使用 Azure AD 設定 Foodee 來自動佈建使用者之前，您必須在 Foodee 中啟用跨網域身分識別管理系統 (SCIM) 的佈建。

1. 登入 [Foodee](https://www.food.ee/login/)，然後選取您的租用戶識別碼。

    :::image type="content" source="media/Foodee-provisioning-tutorial/tenant.png" alt-text="Foodee 企業入口網站主功能表的螢幕擷取畫面。租用戶識別碼預留位置會顯示在功能表中。" border="false":::

1. 在 [企業入口網站] 底下，選取 [單一登入]。

    ![Foodee 企業入口網站的左窗格功能表](media/Foodee-provisioning-tutorial/scim.png)

1. 複製 [API 權杖] 方塊中的值，以供稍後使用。 您會在 Azure 入口網站中 Foodee 應用程式的 [佈建] 索引標籤中，將其輸入 [祕密權杖] 方塊中。

    :::image type="content" source="media/Foodee-provisioning-tutorial/token.png" alt-text="Foodee 企業入口網站中頁面的螢幕擷取畫面。醒目提示 API 權杖值。" border="false":::

## <a name="add-foodee-from-the-gallery"></a>從資源庫新增 Foodee

若要將 Foodee 設定為可使用 Azure AD 自動佈建使用者，您必須從 Azure AD 應用程式庫將 Foodee 新增至您的受控 SaaS 應用程式清單。

若要從 Azure AD 應用程式庫新增 Foodee，請執行下列動作：

1. 在 [Azure 入口網站](https://portal.azure.com)的左側窗格中，選取 [Azure Active Directory]  。

    ![Azure Active Directory 命令](common/select-azuread.png)

1. 選取 [企業應用程式]   > [所有應用程式]  。

    ![[企業應用程式] 窗格](common/enterprise-applications.png)

1. 若要新增應用程式，請選取窗格頂端的 [新增應用程式]。

    ![新增應用程式按鈕](common/add-new-app.png)

1. 在搜尋方塊中，輸入 **Foodee**，在結果窗格中選取 [Foodee]，然後選取 [新增] 以新增應用程式。

    ![結果清單中的 Foodee](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>對 Foodee 設定自動使用者佈建 

在本節中，您會設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者或群組指派，在 Foodee 中建立、更新和停用使用者或群組。

> [!TIP]
> 您也可以遵循 [Foodee 單一登入教學課程](Foodee-tutorial.md)中的指示，啟用 Foodee 的 SAML 型單一登入。 雖然單一登入和自動使用者佈建這兩個功能互相補充，您還是可以將這兩者分開設定。

若要在 Azure AD 中對 Foodee 設定自動使用者佈建，請執行下列動作：

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [企業應用程式] > [所有應用程式]。

    ![企業應用程式窗格](common/enterprise-applications.png)

1. 在 **應用程式** 清單中，選取 [Foodee]。

    ![應用程式清單中的 Foodee 連結](common/all-applications.png)

1. 選取 [佈建]  索引標籤。

    ![[管理] 選項的螢幕擷取畫面，並已指出 [佈建] 選項。](common/provisioning.png)

1. 在 [佈建模式] 下拉式清單中，選取 [自動]。

    ![[佈建模式] 下拉式清單的螢幕擷取畫面，並已指出 [自動] 選項。](common/provisioning-automatic.png)

1. 在 [管理員認證] 底下，執行下列動作：

   a. 在 [租用戶 URL] 方塊中，輸入您稍早取得的 **https:\//concierge.food.ee/scim/v2** 值。

   b. 在 [祕密權杖] 方塊中，輸入您稍早取得的 **API 權杖** 值。
   
   c. 若要確保 Azure AD 可以連線到 Foodee，請選取 [測試連線]。 如果連線失敗，請確定您的 Foodee 帳戶具有管理員權限，然後再試一次。

    ![測試連線的連結](common/provisioning-testconnection-tenanturltoken.png)

1. 在 [通知電子郵件] 方塊中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![[通知電子郵件] 文字方塊](common/provisioning-notification-email.png)

1. 選取 [儲存]。

1. 在 [對應] 底下，選取 [將 Azure Active Directory 使用者同步至 Foodee]。

    :::image type="content" source="media/Foodee-provisioning-tutorial/usermapping.png" alt-text="[對應] 區段的螢幕擷取畫面。醒目提示 [名稱] 底下的 [將 Azure Active Directory 使用者同步至 Foodee]。" border="false":::

1. 在 [屬性對應] 底下，檢閱從 Azure AD 同步至 Foodee 的使用者屬性。 選取為 [比對] 屬性 (Property) 的屬性 (Attribute) 會用來比對 Foodee 中的「使用者帳戶」以進行更新作業。 

    :::image type="content" source="media/Foodee-provisioning-tutorial/userattribute.png" alt-text="[屬性對應] 頁面的螢幕擷取畫面。資料表會列出 Azure Active Directory 和 Foodee 的屬性，以及相符的優先順序。" border="false":::

1. 若要認可變更，請選取 [儲存]。
1. 在 [對應] 底下，選取 [將 Azure Active Directory 群組同步至 Foodee]。

    :::image type="content" source="media/Foodee-provisioning-tutorial/groupmapping.png" alt-text="[對應] 區段的螢幕擷取畫面。醒目提示 [名稱] 底下的 [將 Azure Active Directory 群組同步至 Foodee]。" border="false":::

1. 在 [屬性對應] 底下，檢閱從 Azure AD 同步至 Foodee 的使用者屬性。 選取為 [比對] 屬性 (Property) 的屬性 (Attribute) 會用來比對 Foodee 中的「群組帳戶」以進行更新作業。

    :::image type="content" source="media/Foodee-provisioning-tutorial/groupattribute.png" alt-text="[屬性對應] 頁面的螢幕擷取畫面。資料表會列出 Azure Active Directory 屬性、Foodee 屬性，以及比對的優先順序。" border="false":::

1. 若要認可變更，請選取 [儲存]。
1. 設定範圍篩選條件。 若要了解作法，請參閱[範圍篩選條件教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的指示。

1. 若要啟用 Foodee 的 Azure AD 佈建服務，請在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

    ![佈建狀態切換](common/provisioning-toggle-on.png)

1. 在 [設定] 底下的 [範圍] 下拉式清單中，定義您想要佈建到 Foodee 的使用者或群組。

    ![[佈建範圍] 下拉式清單](common/provisioning-scope.png)

1. 準備好要佈建時，請選取 [儲存]。

    ![[佈建設定] 的 [儲存] 按鈕](common/provisioning-configuration-save.png)

上述作業會針對您在 [範圍] 下拉式清單中定義的使用者或群組，啟動首次同步。 首次同步所花的時間會比後續同步更久。 如需詳細資訊，請參閱[佈建使用者需要多久時間？](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)。

您可以使用 [目前狀態] 區段來監視進度，並遵循連結來取得佈建活動報告。 此報告會描述 Azure AD 佈建服務在 Foodee 上所執行的所有動作。 如需詳細資訊，請參閱[檢查使用者佈建的狀態](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 若要讀取 Azure AD 佈建記錄，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
