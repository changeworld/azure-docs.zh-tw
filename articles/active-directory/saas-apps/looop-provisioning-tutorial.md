---
title: 教學課程：以 Azure Active Directory 設定 Looop 來自動佈建使用者 | Microsoft Docs
description: 了解如何將 Azure Active Directory 設定為可對 Looop 自動佈建及取消佈建使用者帳戶。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: 528003ac482da6f254bf437321c70c389d23844b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835009"
---
# <a name="tutorial-configure-looop-for-automatic-user-provisioning"></a>教學課程：設定 Looop 來自動佈建使用者

本教學課程旨在示範將 Azure AD 設定為可對 Looop 自動佈建及取消佈建使用者和/或群組時，Looop 與 Azure Active Directory (Azure AD) 中須執行的步驟。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [Looop 租用戶](https://www.looop.co/pricing/)
* Looop 上具有系統管理員權限的使用者帳戶。

## <a name="assign-users-to-looop"></a>將使用者指派給 Looop

Azure Active Directory 使用所謂指派的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動使用者佈建的內容中，只有已指派至 Azure AD 中應用程式的使用者和/或群組會進行同步處理。

設定並啟用自動使用者佈建之前，您應先決定 Azure AD 中的哪些使用者和/或群組需要存取 Looop。 一旦決定後，您可以依照此處的指示，將這些使用者和/或群組指派給 Looop：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-looop"></a>將使用者指派給 Looop 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 Looop，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 Looop 時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特有角色 (如果有的話)。 具有 **預設存取** 角色的使用者會從佈建中排除。

## <a name="set-up-looop-for-provisioning"></a>設定 Looop 以進行佈建

將 Looop 設定為可使用 Azure AD 自動佈建使用者之前，您必須從 Looop 擷取一些佈建資訊。

1. 登入您的 [Looop 管理主控台](https://app.looop.co/#/login)，然後選取 [帳戶]。 在 [帳戶設定] 底下選取 [驗證]。

    :::image type="content" source="media/looop-provisioning-tutorial/admin.png" alt-text="Looop 管理主控台的螢幕擷取畫面。[帳戶] 索引標籤會醒目提示並開啟。在 [帳戶設定] 底下，醒目提示 [驗證]。" border="false":::

2. 按一下 [SCIM 整合] 底下的 [重設權杖] 來產生新權杖。

    :::image type="content" source="media/looop-provisioning-tutorial/resettoken.png" alt-text="Looop 管理主控台頁面上的 [SCIM 整合] 區段螢幕擷取畫面。[重設權杖] 按鈕會醒目提示。" border="false":::

3. 複製 **SCIM 端點** 和 **權杖**。 在 Azure 入口網站中，這些值會輸入至 Looop 應用程式的 [佈建] 索引標籤中的 [租用戶 URL] 和 [祕密權杖] 欄位。 

    ![Looop 建立權杖](media/looop-provisioning-tutorial/token.png)

## <a name="add-looop-from-the-gallery"></a>從資源庫新增 Looop

若要將 Looop 設定為可使用 Azure AD 自動佈建使用者，您必須從 Azure AD 應用程式庫將 Looop 新增至您的受控 SaaS 應用程式清單。

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，選取 [Azure Active Directory]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取窗格頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Looop**，然後在結果面板中選取 [Looop]。 

    ![結果清單中的 Looop](common/search-new-app.png)

5. 選取 [註冊 Looop] 按鈕，這會將您重新導向至 Looop 的登入頁面。 

    ![Looop OIDC 新增](media/looop-provisioning-tutorial/signup.png)

6. 當 Looop 是 OpenIDConnect 應用程式時，請選擇使用您的 Microsoft 公司帳戶登入 Looop。

    ![Looop OIDC 登入](media/looop-provisioning-tutorial/msftlogin.png)

7. 驗證成功之後，請接受同意頁面的同意提示。 然後，應用程式會自動新增至您的租用戶，並將您重新導向至您的 Looop 帳戶。

    ![Looop OIDc 同意](media/looop-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-looop"></a>對 Looop 設定自動使用者佈建 

本節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 Looop 中建立、更新和停用使用者和/或群組。

### <a name="to-configure-automatic-user-provisioning-for-looop-in-azure-ad"></a>在 Azure AD 中為 Looop 設定自動使用者佈建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Looop]。

    ![應用程式清單中的 Looop 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![[管理] 選項的螢幕擷取畫面，並已指出 [佈建] 選項。](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![[佈建模式] 下拉式清單的螢幕擷取畫面，並已指出 [自動] 選項。](common/provisioning-automatic.png)

5. 在 [管理員認證] 區段下的 [租用戶 URL] 中輸入 `https://<organisation_domain>.looop.co/scim/v2`。 例如： `https://demo.looop.co/scim/v2` 。 在 [祕密權杖] 中輸入您先前從 Looop 擷取並儲存的值。 按一下 [測試連線]，以確保 Azure AD 可以連線至 Looop。 如果連線失敗，請確定您的 Looop 帳戶具有管理員權限並再試一次。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]  欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]  核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [檔案]  。

8. 在 [對應] 區段中，選取 [將 Azure Active Directory 使用者同步至 Looop]。

    ![Looop 使用者對應](media/looop-provisioning-tutorial/usermappings.png)

9. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Looop 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 Looop 中的使用者帳戶，以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

   |屬性|類型|支援篩選|
   |---|---|---|
   |userName|String|&check;|
   |作用中|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:area|字串|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:custom_1|字串|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:custom_2|字串|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:custom_3|字串|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:department|字串|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:employee_id|字串|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:location|字串|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:position|字串|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:startAt|字串|

10. 在 [對應] 區段下，選取 [將 Azure Active Directory 群組同步至 Meta Networks Connector]。

    ![Looop 群組對應](media/looop-provisioning-tutorial/groupmappings.png)

11. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Meta Networks Connector 的群組屬性。 選取為 [比對] 屬性的屬性會用來比對 Meta Networks Connector 中的群組，以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    |屬性|類型|支援篩選|
    |---|---|---|
    |displayName|String|&check;|
    |members|參考|
    |externalId|String|


10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 若要啟用 Looop 的 Azure AD 佈建服務，在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 透過在 [設定] 區段的 [範圍] 中選擇需要的值，可定義要佈建到 Looop 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

13. 當您準備好要佈建時，按一下 [儲存]  。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]  區段的 [範圍]  中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [同步處理詳細資料] 區段來監視進度，並遵循連結來佈建活動報告，當中會描述 Looop 上的 Azure AD 佈建服務所執行之所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)


