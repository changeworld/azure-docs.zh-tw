---
title: 教學課程：Slack 的使用者佈建 - Azure AD
description: 了解如何設定 Azure Active Directory 來自動佈建並取消佈建使用者帳戶至 Slack。
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: arvinh
ms.openlocfilehash: 58fe99164c390d8a9435e5aa65e55fe7fca5d6db
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359471"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>教學課程︰設定 Slack 來自動佈建使用者

本教學課程旨在說明您需要在 Slack 和 Azure AD 中執行的步驟，以將使用者帳戶從 Azure AD 自動佈建和取消佈建至 Slack。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支援的功能
> [!div class="checklist"]
> * 在 Slack 中建立使用者
> * 當使用者不再需要存取權時，將其從 Slack 中移除
> * 讓 Azure AD 與 Slack 之間的使用者屬性保持同步
> * 在 Slack 中佈建群組和群組成員資格
> * [單一登入](./slack-tutorial.md)至 Slack (建議)


## <a name="prerequisites"></a>Prerequisites

本教學課程中說明的案例假設您已經具有下列項目：

* [Azure AD 租用戶](../develop/quickstart-create-new-tenant.md)。
* Azure AD 中具有設定佈建[權限](../users-groups-roles/directory-assign-admin-roles.md)的使用者帳戶 (例如，應用程式管理員、雲端應用程式管理員、應用程式擁有者或全域管理員)。
* 已啟用具有 [Plus 方案](https://aadsyncfabric.slack.com/pricing)或更高方案的 Slack。
* 具有小組系統管理員權限的 Slack 使用者帳戶。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃佈建部署
1. 了解[佈建服務的運作方式](../app-provisioning/user-provisioning.md) \(部分機器翻譯\)。
2. 判斷誰會在[佈建範圍](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)內。
3. 判斷哪些資料要[在 Azure AD 與 Slack 之間對應](../app-provisioning/customize-application-attributes.md)。 

## <a name="step-2-add-slack-from-the-azure-ad-application-gallery"></a>步驟 2： 從 Azure AD 應用程式庫新增 Slack

從 Azure AD 應用程式庫新增 Slack，以開始管理對 Slack 的佈建。 如果您先前已針對 SSO 設定 Slack，則可使用相同的應用程式。 不過，建議您在一開始測試整合時，建立個別的應用程式。 [在此](../manage-apps/add-application-portal.md)深入了解從資源庫新增應用程式。 

## <a name="step-3-define-who-will-be-in-scope-for-provisioning"></a>步驟 3： 定義將在佈建範圍內的人員 

Azure AD 佈建服務可供根據對應用程式的指派，或根據使用者/群組的屬性，界定將要佈建的人員。 如果您選擇根據指派來界定將佈建至應用程式的人員，您可以使用下列[步驟](../manage-apps/assign-user-or-group-access-portal.md)將使用者和群組指派給應用程式。 如果您選擇僅根據使用者或群組的屬性來界定將要佈建的人員，可以使用如[這裡](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的範圍篩選條件。 

* 將使用者和群組指派給 Slack 時，您必須選取 [預設存取] 以外的角色。 具有預設存取角色的使用者會從佈建中排除，而且會在佈建記錄中被標示為沒有效率。 如果應用程式上唯一可用的角色是 [預設存取] 角色，您可以[更新應用程式資訊清單](../develop/howto-add-app-roles-in-azure-ad-apps.md) \(部分機器翻譯\) 以新增其他角色。 

* 從小規模開始。 在推出給所有人之前，先使用一小部分的使用者和群組進行測試。 當佈建範圍設為已指派的使用者和群組時，您可將一或兩個使用者或群組指派給應用程式來控制這點。 當範圍設為所有使用者和群組時，您可指定[以屬性為基礎的範圍篩選條件](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。

## <a name="step-4-configure-automatic-user-provisioning-to-slack"></a>步驟 4： 設定使用者自動佈建至 Slack 

本節會引導您將 Azure AD 連接至 Slack 的使用者帳戶佈建 API，以及根據 Azure AD 中的使用者和群組指派，設定佈建服務以在 Slack 中建立、更新和停用指派的使用者帳戶。

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>若要在 Azure AD 中設定自動使用者帳戶佈建至 Slack︰

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Slack]。

    ![應用程式清單中的 Slack 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![[管理] 選項的螢幕擷取畫面，並已指出 [佈建] 選項。](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![[佈建模式] 下拉式清單的螢幕擷取畫面，並已指出 [自動] 選項。](common/provisioning-automatic.png)

5. 在 [系統管理員認證] 區段下，按一下 [授權]。 這會在新的瀏覽器視窗中開啟 Slack 授權對話方塊。

    ![螢幕擷取畫面，顯示 [授權管理員認證] 按鈕。](media/slack-provisioning-tutorial/authorization.png)


6. 在新視窗中，使用您的小組系統管理帳戶登入 Slack。 在產生的授權對話方塊中，選取您想要啟用佈建的 Slack 小組，然後選取 [授權]。 一旦完成後，回到 Azure 入口網站以完成佈建組態。

    ![授權對話方塊](./media/slack-provisioning-tutorial/slackauthorize.png)

7. 在 Azure 入口網站中，按一下 [測試連接]以確保 Azure AD 可以連接到您的 Slack 應用程式。 如果連接失敗，請確定您的 Slack 帳戶具有小組系統管理員權限，並再試一次「授權」步驟。

8. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

9. 選取 [儲存]。

10. 在 [對應] 區段中，選取 [同步處理 Azure Active Directory 使用者至 Slack]。

11. 在 [屬性對應] 區段中，檢閱將從 Azure AD 同步至 Slack 的使用者屬性。 請注意，選取為 [比對] 屬性的屬性會用來比對 Slack 中的使用者帳戶以進行更新作業。 選取 [儲存] 按鈕以認可任何變更。

   |屬性|類型|
   |---|---|
   |作用中|Boolean|
   |externalId|String|
   |displayName|String|
   |name.familyName|String|
   |name.givenName|String|
   |title|String|
   |emails[type eq "work"].value|String|
   |userName|String|
   |nickName|String|
   |addresses[type eq "untyped"].streetAddress|String|
   |addresses[type eq "untyped"].locality|String|
   |addresses[type eq "untyped"].region|String|
   |addresses[type eq "untyped"].postalCode|String|
   |addresses[type eq "untyped"].country|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "work"].value|String|
   |roles[primary eq "True"].value|String|
   |地區設定|String|
   |name.honorificPrefix|String|
   |photos[type eq "photo"].value|String|
   |profileUrl|String|
   |timezone|String|
   |userType|String|
   |urn:scim:schemas:extension:enterprise:1.0.department|String|
   |urn:scim:schemas:extension:enterprise:1.0.manager|參考|
   |urn:scim:schemas:extension:enterprise:1.0.employeeNumber|String|
   |urn:scim:schemas:extension:enterprise:1.0.costCenter|String|
   |urn:scim:schemas:extension:enterprise:1.0.organization|String|
   |urn:scim:schemas:extension:enterprise:1.0.division|String|

12. 在 [對應] 區段中，選取 [同步處理 Azure Active Directory 群組至 Slack]。

13. 在 [屬性對應] 區段中，檢閱將從 Azure AD 同步至 Slack 的群組屬性。 請注意，選取為 [比對] 屬性的屬性會用來比對 Slack 中的群組以進行更新作業。 選取 [儲存] 按鈕以認可任何變更。

      |屬性|類型|
      |---|---|
      |displayName|String|
      |members|參考|

14. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

15. 若要啟用 Slack 的 Azure AD 佈建服務，在 [設定]區段中，將 [佈建狀態] 變更為 [開啟]

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

16. 透過在 [設定] 區段的 [範圍] 中選擇需要的值，可定義要佈建到 Slack 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

17. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中所定義所有使用者和群組啟動首次同步處理週期。 初始週期會比後續週期花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 

## <a name="step-5-monitor-your-deployment"></a>步驟 5。 監視您的部署
設定佈建後，請使用下列資源來監視您的部署：

1. 使用[佈建記錄](../reports-monitoring/concept-provisioning-logs.md) \(部分機器翻譯\) 來判斷哪些使用者已佈建成功或失敗
2. 檢查[進度列](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) \(部分機器翻譯\) 來查看佈建週期的狀態，以及其接近完成的程度
3. 如果佈建設定似乎處於狀況不良的狀態，應用程式將會進入隔離狀態。 [在此](../app-provisioning/application-provisioning-quarantine-status.md)深入了解隔離狀態。

## <a name="troubleshooting-tips"></a>疑難排解秘訣

* 設定 Slack 的 **displayName** 屬性時，請留意下列行為：

  * 值並非完全唯一 (例如，2 位使用者可以具有相同的顯示名稱)

  * 支援非英文的字元、空格、大小寫。 
  
  * 允許的標點符號包括句號、底線、連字號、所有格號、括弧 (例如 **( [ { } ] )**) 和分隔符號 (例如 **, / ;**)。
  
  * displayName 屬性不能含有 '@' 字元。 如果包含 '@'，您可能會在佈建記錄中找到具有 "AttributeValidationFailed" 說明的已略過事件。

  * 只有在 Slack 的工作場所/組織中設定了下列這兩個設定時才會更新：**已啟用設定檔同步處理** 及 **使用者無法變更其顯示名稱**。

* Slack 的 **userName** 屬性必須少於 21 個字元且具備唯一值。

* Slack 只能與 **userName** 和 **email** 屬性相符。  
  
* 常見的錯誤碼記載於官方的 Slack 文件中 - https://api.slack.com/scim#errors

## <a name="change-log"></a>變更記錄

* 2020/06/16 - 已將 DisplayName 屬性修改為只會在新使用者建立期間進行更新。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)