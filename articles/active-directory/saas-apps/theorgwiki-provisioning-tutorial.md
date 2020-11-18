---
title: 教學課程：以 Azure Active Directory 設定 TheOrgWiki 來自動佈建使用者 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 將使用者帳戶自動佈建及取消佈建至 TheOrgWiki。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 8238b9902aafcabc079c551a0eabc7170042209a
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357618"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>教學課程：設定 TheOrgWiki 來自動佈建使用者

本教學課程旨在示範將 Azure AD 設定為可對 TheOrgWiki 自動佈建及取消佈建使用者和/或群組時，TheOrgWiki 與 Azure Active Directory (Azure AD) 中須執行的步驟。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶。
* [OrgWiki 租用戶](https://www.theorgwiki.com/welcome/)。
* TheOrgWiki 中具有管理員權限的使用者帳戶。

## <a name="assign-users-to-theorgwiki"></a>將使用者指派給 TheOrgWiki

Azure Active Directory 使用所謂指派的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動使用者佈建的內容中，只有已指派至 Azure AD 中應用程式的使用者和/或群組會進行同步處理。

設定並啟用自動使用者佈建之前，您應先決定 Azure AD 中的哪些使用者和/或群組需要存取 TheOrgWiki。 一旦決定後，您可以依照此處的指示，將這些使用者和/或群組指派給 TheOrgWiki：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>將使用者指派給 TheOrgWiki 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 TheOrgWiki，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 TheOrgWiki 時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特有角色 (如果有的話)。 具有 **預設存取** 角色的使用者會從佈建中排除。

## <a name="set-up-theorgwiki-for-provisioning"></a>設定 TheOrgWiki 以進行佈建

以 Azure AD 設定 TheOrgWiki 來自動佈建使用者之前，您必須在 TheOrgWiki 上啟用 SCIM 佈建。

1. 登入您的 [TheOrgWiki 管理主控台](https://www.theorgwiki.com/login/)。 按一下 [管理主控台]。

    ![TheOrgWiki 的螢幕擷取畫面，其中已指出使用者頭像和管理主控台。](media/theorgwiki-provisioning-tutorial/login.png)

2. 在 [管理主控台] 中，按一下 [設定] 索引標籤。 

    ![TheOrgWiki 管理主控台的螢幕擷取畫面，其中已指出 [設定] 索引標籤。](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. 瀏覽至 [服務帳戶]。

    ![TheOrgWiki 管理主控台中 [服務帳戶] 頁面的螢幕擷取畫面。](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. 按一下 [+服務帳戶]。 在 [服務帳戶類型] 下，選取 [權杖型]。 按一下 **[儲存]** 。

    ![[新增服務帳戶] 對話方塊的螢幕擷取畫面，其中已指出 [服務帳戶類型]、[權杖型] 和 [儲存] 選項。](media/theorgwiki-provisioning-tutorial/auth.png)

5.  複製 [作用中的權杖]。 此值會輸入到 Azure 入口網站中 TheOrgWiki 應用程式 [佈建] 索引標籤中的 [祕密權杖] 欄位。
     
    ![[管理 SCIM 佈建的權杖] 對話方塊的螢幕擷取畫面。](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>從資源庫新增 TheOrgWiki

若要將 TheOrgWiki 設定為可使用 Azure AD 自動佈建使用者，您必須從 Azure AD 應用程式庫將 TheOrgWiki 新增至您的受控 SaaS 應用程式清單。

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，選取 [Azure Active Directory]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取窗格頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **TheOrgWiki**，然後在結果面板中選取 [TheOrgWiki]。 

    ![結果清單中的 TheOrgWiki](common/search-new-app.png)

5. 選取 [註冊 TheOrgWiki] 按鈕，這會將您重新導向至 TheOrgWiki 的登入頁面。 

    ![TheOrgWiki 登入頁面的螢幕擷取畫面，其中已指出 URL](media/theorgwiki-provisioning-tutorial/image00.png)

6.  選取右上角的 [登入]。

    ![登入頁面右上角的螢幕擷取畫面，其中已指出 [登入] 選項。](media/theorgwiki-provisioning-tutorial/image02.png)

7. 當 TheOrgWiki 是 OpenIDConnect 應用程式時，請選擇使用您的 Microsoft 公司帳戶登入 TheOrgWiki。

    ![TheOrgWiki 登入頁面的螢幕擷取畫面，其中已指出 [使用 Microsoft 帳戶登入] 選項。](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. 成功驗證之後，應用程式會自動新增至您的租用戶，並將您重新導向至 TheOrgWiki 帳戶。

    ![TheOrgWiki 新增 SCIM](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>設定將使用者自動佈建至 TheOrgWiki 

本節將引導逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 TheOrgWiki 中建立、更新和停用使用者和/或群組。


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>若要在 Azure AD 中為 TheOrgWiki 設定自動使用者佈建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [TheOrgWiki]。

    ![應用程式清單中的 TheOrgWiki 連結](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![[管理] 選項的螢幕擷取畫面，並已指出 [佈建] 選項。](common/provisioning.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![[佈建模式] 下拉式清單的螢幕擷取畫面，並已指出 [自動] 選項。](common/provisioning-automatic.png)

5. 在 [管理員認證] 區段下的 [租用戶 URL] 中輸入 `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/`。 

    範例： `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> 只有在 TheOrgWiki 的初始註冊程序進行期間才能設定 [子網域值]。
 
6. 在 [秘密權杖] 欄位中輸入您稍早從 TheOrgWiki 擷取到的權杖值。 按一下 [測試連線]，以確保 Azure AD 可以連線至 TheOrgWiki。 如果連線失敗，請確定您的 TheOrgWiki 帳戶具有管理員權限，然後再試一次。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

7. 在 [通知電子郵件]  欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]  核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

8. 按一下 [檔案]  。

9. 在 [對應] 區段中，選取 [將 Azure Active Directory 使用者同步至 TheOrgWiki]。

    ![TheOrgWiki 使用者對應](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步處理至 TheOrgWiki 的使用者屬性。 選取為 [比對] 屬性 (Property) 的屬性 (Attribute) 會用來比對 TheOrgWiki 中的使用者帳戶，以進行更新作業。 選取 [儲存]  按鈕以認可所有變更。

    ![TheOrgWiki 使用者屬性](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

12. 若要對 TheOrgWiki 啟用 Azure AD 佈建服務，請在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

13. 透過在 [設定] 區段的 [範圍] 中選擇需要的值，可定義要佈建到 TheOrgWiki 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

14. 當您準備好要佈建時，按一下 [儲存]  。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]  區段的 [範圍]  中定義的所有使用者和/或群組，啟動首次同步處理。 首次同步所花的時間會比後續同步更久。 如需佈建使用者和/或群組所需時間長度的詳細資訊，請參閱[佈建使用者需要多久時間](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)。

您可以使用 [目前狀態] 區段來監視進度，並進入佈建活動報告的連結，以透過報告了解 TheOrgWiki 上的 Azure AD 佈建服務所執行的所有動作。 如需詳細資訊，請參閱[檢查使用者佈建的狀態](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 若要讀取 Azure AD 佈建記錄，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)。
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [了解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)。