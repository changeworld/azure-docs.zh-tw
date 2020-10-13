---
title: 教學課程：使用 Azure Active Directory 設定 TheOrgWiki 來自動布建使用者 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory，以將使用者帳戶自動布建和取消布建至 TheOrgWiki。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 0a72ea413e7298c8e6cb622cb985e6516435b451
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91255772"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>教學課程：設定 TheOrgWiki 來自動布建使用者

本教學課程的目的是要示範在 TheOrgWiki 中執行的步驟，以及 Azure Active Directory (Azure AD) 將 Azure AD 設定為自動布建和解除布建使用者和/或群組至 TheOrgWiki。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶。
* [OrgWiki 租](https://www.theorgwiki.com/welcome/)使用者。
* TheOrgWiki 中具有系統管理員許可權的使用者帳戶。

## <a name="assign-users-to-theorgwiki"></a>將使用者指派給 TheOrgWiki

Azure Active Directory 使用所謂「指派」的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動使用者布建的內容中，只會同步處理在 Azure AD 中指派給應用程式的使用者和/或群組。

在設定並啟用自動使用者布建之前，您應該決定 Azure AD 中的哪些使用者和/或群組需要存取 TheOrgWiki。 一旦決定後，您可以遵循此處的指示，將這些使用者和/或群組指派給 TheOrgWiki：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>將使用者指派給 TheOrgWiki 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 TheOrgWiki，以測試自動使用者布建設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 TheOrgWiki 時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特定角色 (如果有的話) 。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="set-up-theorgwiki-for-provisioning"></a>設定布建的 TheOrgWiki

使用 Azure AD 設定 TheOrgWiki 來自動布建使用者之前，您必須在 TheOrgWiki 上啟用 SCIM 布建。

1. 登入您的 [TheOrgWiki 管理主控台](https://www.theorgwiki.com/login/)。 按一下 [ **管理主控台**]。

    ![已呼叫使用者頭像和管理主控台的組織 Wiki 螢幕擷取畫面。](media/theorgwiki-provisioning-tutorial/login.png)

2. 在 [管理主控台] 中，按一下 [ **設定]** 索引標籤。 

    ![組織 Wiki 管理主控台的螢幕擷取畫面，其中已呼叫 [設定] 索引標籤。](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. 流覽至 [ **服務帳戶**]。

    ![組織 Wiki 管理主控台中 [服務帳戶] 頁面的螢幕擷取畫面。](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. 按一下 [ **+ 服務帳戶**]。 在 [ **服務帳戶類型**] 下，選取 [ **權杖依據**]。 按一下 **[儲存]** 。

    ![[新增服務帳戶] 對話方塊的螢幕擷取畫面，其中包含服務帳戶類型、以權杖為基礎的儲存選項，以及已呼叫的儲存選項。](media/theorgwiki-provisioning-tutorial/auth.png)

5.  複製使用中的 **權杖**。 在 Azure 入口網站的 TheOrgWiki 應用程式的 [布建] 索引標籤中，將會在 [秘密權杖] 欄位中輸入此值。
     
    ![[管理 C I M 布建的權杖] 對話方塊的螢幕擷取畫面。](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>從資源庫新增 TheOrgWiki

若要使用 Azure AD 設定 TheOrgWiki 來自動布建使用者，您需要從 Azure AD 應用程式資源庫將 TheOrgWiki 新增至受控 SaaS 應用程式清單。

1. 在 [ **[Azure 入口網站](https://portal.azure.com)** 的左側導覽面板中，選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要加入新的應用程式，請選取窗格頂端的 [ **新增應用程式** ] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **TheOrgWiki**，在 [結果] 面板中選取 [ **TheOrgWiki** ]。 

    ![結果清單中的 TheOrgWiki](common/search-new-app.png)

5. 選取 [ **註冊 TheOrgWiki** ] 按鈕，這會將您重新導向至 TheOrgWiki 的登入頁面。 

    ![[組織 Wiki 登入] 頁面的螢幕擷取畫面，其中 URL 稱為 out](media/theorgwiki-provisioning-tutorial/image00.png)

6.  選取右上角的 **[登**入]。

    ![登入頁面右上角的螢幕擷取畫面，其中已呼叫 [登入] 選項。](media/theorgwiki-provisioning-tutorial/image02.png)

7. 由於 TheOrgWiki 是 OpenIDConnect 應用程式，請選擇使用您的 Microsoft 公司帳戶登入 OrgWiki。

    ![[組織 Wiki 登入] 頁面的螢幕擷取畫面，其中已呼叫使用 Microsoft 帳戶登入選項。](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. 成功驗證之後，應用程式會自動新增至您的租使用者，而系統會將您重新導向至您的 TheOrgWiki 帳戶。

    ![OrgWiki 新增 SCIM](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>設定自動使用者布建至 TheOrgWiki 

本節將引導您逐步設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和/或群組指派，在 TheOrgWiki 中建立、更新及停用使用者和/或群組。


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>若要在 Azure AD 中為 TheOrgWiki 設定自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [ **TheOrgWiki**]。

    ![應用程式清單中的 OrgWiki 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![已呼叫 [布建] 選項的 [管理選項] 螢幕擷取畫面。](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![[布建模式] 下拉式清單的螢幕擷取畫面，其中已呼叫 [自動] 選項。](common/provisioning-automatic.png)

5. 在 [ **管理員認證** ] 區段下的 [ `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` **租使用者 URL**] 中輸入。 

    範例： `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> **子域值**只能在 TheOrgWiki 的初始註冊程式期間設定。
 
6. 在 [ **秘密權杖** ] 欄位中輸入您先前從 TheOrgWiki 取出的權杖值。 按一下 [ **測試連接** ] 以確保 Azure AD 可以連線至 TheOrgWiki。 如果連接失敗，請確定您的 TheOrgWiki 帳戶具有系統管理員許可權，然後再試一次。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

7. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

8. 按一下 **[儲存]** 。

9. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者至 TheOrgWiki**]。

    ![TheOrgWiki 使用者對應](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理到 TheOrgWiki 的使用者屬性。 選取為 [比對 **] 屬性的屬性會** 用來比對 TheOrgWiki 中的使用者帳戶以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![TheOrgWiki 使用者屬性](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

12. 若要啟用 TheOrgWiki Azure AD 的布建服務，請在 [**設定**] 區段中，將 [布建**狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

13. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建到 OrgWiki 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

14. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行。 如需布建使用者和/或群組所需時間長度的詳細資訊，請參閱布建 [使用者](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)需要多久的時間。

您可以使用 [ **目前狀態** ] 區段來監視進度，並遵循布建活動報告的連結，其中描述 TheOrgWiki 上的 Azure AD 布建服務所執行的所有動作。 如需詳細資訊，請參閱[檢查使用者佈建的狀態](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 若要讀取 Azure AD 布建記錄，請參閱 [有關自動布建使用者帳戶的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶](../app-provisioning/configure-automatic-user-provisioning-portal.md)布建。
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何查看記錄並取得布建活動的報告](../app-provisioning/check-status-user-account-provisioning.md)。