---
title: 教學課程：使用者布建的時差-Azure AD
description: 了解如何設定 Azure Active Directory 來自動佈建並取消佈建使用者帳戶至 Slack。
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2020
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ca536ddacb0f81459625b733eb79282e145afba
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87016289"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>教學課程︰設定 Slack 來自動佈建使用者

本教學課程旨在說明您需要在 Slack 和 Azure AD 中執行的步驟，以將使用者帳戶從 Azure AD 自動佈建和取消佈建至 Slack。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支援的功能
> [!div class="checklist"]
> * 在時差中建立使用者
> * 當使用者不再需要存取權時，移除其時差
> * 在 Azure AD 和時差之間保持使用者屬性同步處理
> * 在時差中布建群組和群組成員資格
> * [單一登入](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-tutorial)的時差（建議選項）


## <a name="prerequisites"></a>Prerequisites

本教學課程中說明的案例假設您已經具有下列項目：

* [Azure AD 租](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)使用者。
* Azure AD 中具有設定佈建[權限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的使用者帳戶 (例如，應用程式管理員、雲端應用程式管理員、應用程式擁有者或全域管理員)。
* 已啟用[Plus 方案](https://aadsyncfabric.slack.com/pricing)或更高的時差租使用者。
* 具有小組系統管理員許可權之時差的使用者帳戶。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃佈建部署
1. 了解[佈建服務的運作方式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) \(部分機器翻譯\)。
2. 判斷誰會在[佈建範圍](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)內。
3. 決定要[在 Azure AD 和時差之間對應](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的資料。 

## <a name="step-2-add-slack-from-the-azure-ad-application-gallery"></a>步驟 2. 從 Azure AD 應用程式資源庫新增時差

新增 Azure AD 應用程式庫的時差，開始管理布建到時差。 如果您先前已設定 SSO 的時差，則可以使用相同的應用程式。 不過，建議您在一開始測試整合時，建立個別的應用程式。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)深入了解從資源庫新增應用程式。 

## <a name="step-3-define-who-will-be-in-scope-for-provisioning"></a>步驟 3. 定義將在佈建範圍內的人員 

Azure AD 佈建服務可供根據對應用程式的指派，或根據使用者/群組的屬性，界定將要佈建的人員。 如果您選擇根據指派來界定將佈建至應用程式的人員，您可以使用下列[步驟](../manage-apps/assign-user-or-group-access-portal.md)將使用者和群組指派給應用程式。 如果您選擇僅根據使用者或群組的屬性來界定將要佈建的人員，可以使用如[這裡](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的範圍篩選條件。 

* 將使用者和群組指派給「時差」時，您必須選取 [**預設存取**] 以外的角色。 具有預設存取角色的使用者會從佈建中排除，而且會在佈建記錄中被標示為沒有效率。 如果應用程式上唯一可用的角色是 [預設存取] 角色，您可以[更新應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) \(部分機器翻譯\) 以新增其他角色。 

* 從小規模開始。 在推出給所有人之前，先使用一小部分的使用者和群組進行測試。 當佈建範圍設為已指派的使用者和群組時，您可將一或兩個使用者或群組指派給應用程式來控制這點。 當範圍設為所有使用者和群組時，您可指定[以屬性為基礎的範圍篩選條件](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。

## <a name="step-4-configure-automatic-user-provisioning-to-slack"></a>步驟 4. 設定自動使用者布建至時差 

本節會引導您將 Azure AD 連接至 Slack 的使用者帳戶佈建 API，以及根據 Azure AD 中的使用者和群組指派，設定佈建服務以在 Slack 中建立、更新和停用指派的使用者帳戶。

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>若要在 Azure AD 中設定自動使用者帳戶佈建至 Slack︰

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Slack]。

    ![應用程式清單中的 Slack 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![佈建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![[佈建] 索引標籤](common/provisioning-automatic.png)

5. 在 [系統管理員認證]**** 區段下，按一下 [授權]****。 這會在新的瀏覽器視窗中開啟 Slack 授權對話方塊。

    ![授權](media/slack-provisioning-tutorial/authorization.png)


6. 在新視窗中，使用您的小組系統管理帳戶登入 Slack。 在產生的授權對話方塊中，選取您想要啟用佈建的 Slack 小組，然後選取 [授權]****。 一旦完成後，回到 Azure 入口網站以完成佈建組態。

    ![授權對話方塊](./media/slack-provisioning-tutorial/slackauthorize.png)

7. 在 Azure 入口網站中，按一下 [測試連接]**** 以確保 Azure AD 可以連接到您的 Slack 應用程式。 如果連接失敗，請確定您的 Slack 帳戶具有小組系統管理員權限，並再試一次「授權」步驟。

8. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

9. 選取 [儲存]。

10. 在 [對應] 區段中，選取 [同步處理 Azure Active Directory 使用者至 Slack]****。

11. 在 [屬性對應]**** 區段中，檢閱將從 Azure AD 同步至 Slack 的使用者屬性。 請注意，選取為 [比對]**** 屬性的屬性會用來比對 Slack 中的使用者帳戶以進行更新作業。 選取 [儲存] 按鈕以認可任何變更。

   |屬性|類型|
   |---|---|
   |作用中|Boolean|
   |externalId|字串|
   |displayName|字串|
   |name.familyName|String|
   |name.givenName|String|
   |title|字串|
   |emails[type eq "work"].value|字串|
   |userName|字串|
   |昵稱|字串|
   |位址 [類型 eq "不具類型]]。 streetAddress|字串|
   |位址 [類型 eq "不具類型]]。位置|字串|
   |位址 [type eq "不具類型"]. region|字串|
   |位址 [type eq "不具類型"]. 郵遞區號|字串|
   |位址 [type eq "不具類型"]. country|字串|
   |phoneNumbers[type eq "mobile"].value|字串|
   |phoneNumbers[type eq "work"].value|字串|
   |角色 [主要 eq "True"]。值|字串|
   |地區設定|字串|
   |名稱. honorificPrefix|字串|
   |相片 [輸入 eq "photo"]。值|字串|
   |profileUrl|String|
   |timezone|字串|
   |userType|String|
   |urn： scim：架構：延伸模組： enterprise： 1.0. 部門|字串|
   |urn： scim：架構：延伸模組： enterprise： 1.0. manager|參考|
   |urn： scim：架構：延伸模組： enterprise： 1.0. employeeNumber|字串|
   |urn： scim：架構：延伸模組： enterprise： 1.0. costCenter|字串|
   |urn： scim：架構：延伸模組： enterprise： 1.0. 組織|字串|
   |urn： scim：架構：延伸模組： enterprise： 1.0. 除法|字串|

12. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 群組至時差**]。

13. 在 [屬性對應]**** 區段中，檢閱將從 Azure AD 同步至 Slack 的群組屬性。 請注意，選取為 [比對]**** 屬性的屬性會用來比對 Slack 中的群組以進行更新作業。 選取 [儲存] 按鈕以認可任何變更。

      |屬性|類型|
      |---|---|
      |displayName|字串|
      |members|參考|

14. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

15. 若要啟用 Slack 的 Azure AD 佈建服務，在 [設定]**** 區段中，將 [佈建狀態]**** 變更為 [開啟]****

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

16. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建到時差的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

17. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中所定義所有使用者和群組啟動首次同步處理週期。 初始週期會比後續週期花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 

## <a name="step-5-monitor-your-deployment"></a>步驟 5。 監視您的部署
設定佈建後，請使用下列資源來監視您的部署：

1. 使用[佈建記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) \(部分機器翻譯\) 來判斷哪些使用者已佈建成功或失敗
2. 檢查[進度列](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) \(部分機器翻譯\) 來查看佈建週期的狀態，以及其接近完成的程度
3. 如果佈建設定似乎處於狀況不良的狀態，應用程式將會進入隔離狀態。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)深入了解隔離狀態。

## <a name="troubleshooting-tips"></a>疑難排解秘訣

* 設定 Slack 的 **displayName** 屬性時，請留意下列行為：

  * 值並非完全唯一 (例如，2 位使用者可以具有相同的顯示名稱)

  * 支援非英文的字元、空格、大小寫。 
  
  * 允許的標點符號包括句號、底線、連字號、所有格號、括弧 (例如 **( [ { } ] )**) 和分隔符號 (例如 **, / ;**)。
  
  * displayName 屬性不能有 ' @ ' 字元。 如果包含 ' @ '，您可能會在布建記錄中找到已略過的事件，其描述為 "AttributeValidationFailed"。

  * 只有在 Slack 的工作場所/組織中設定了下列這兩個設定時才會更新：**已啟用設定檔同步處理**及**使用者無法變更其顯示名稱**。

* Slack 的 **userName** 屬性必須少於 21 個字元且具備唯一值。

* 時差只允許與屬性使用者**名稱**和**電子郵件**相符。  
  
* 一般的 erorr 碼記載于官方的時差檔中-https://api.slack.com/scim#errors

## <a name="change-log"></a>變更記錄

* 06/16/2020-只有在建立新的使用者時，才會更新已修改的 DisplayName 屬性。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)