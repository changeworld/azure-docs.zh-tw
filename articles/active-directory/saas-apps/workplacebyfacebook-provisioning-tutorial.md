---
title: 教學課程︰以 Azure Active Directory 設定 Workplace by Facebook 來自動佈建使用者 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Workplace by Facebook 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22576be8dec021f0f18a6e2dda16891ce70d4f13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603219"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>教學課程：設定 Workplace by Facebook 來自動佈建使用者

本教程介紹需要通過 Facebook 和 Azure 活動目錄 （Azure AD） 在工作場所中執行以配置自動使用者預配的步驟。 配置後，Azure AD 使用 Azure AD 預配服務，[通過 Facebook](https://work.workplace.com/)自動預配和取消向工作區預配使用者和組。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。

## <a name="migrating-to-the-new-workplace-by-facebook-application"></a>通過 Facebook 應用程式遷移到新的工作場所
如果您通過 Facebook 與工作場所的現有集成，請參閱以下部分，瞭解即將進行的更改。 如果您是首次通過 Facebook 設置工作區，則可以跳過此部分並轉到支援的功能。 

#### <a name="whats-changing"></a>有哪些變更項目？
* Azure AD 端的更改：在工作場所中預配使用者的授權方法歷來是一個長期使用的秘密權杖。 很快，您將看到授權方法更改為 OAuth 授權授予。 
* 工作場所方面的更改：以前，Azure AD 應用是 Facebook 在工作場所中的自訂集成。 現在，您將在工作區集成目錄中看到 Azure AD 作為協力廠商應用程式。 

 

#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>需要執行什麼操作才能將現有自訂集成遷移到新應用程式？
如果您有具有有效權杖的現有工作區集成，**則無需執行任何操作**。 我們每週會自動將客戶遷移到新應用程式。 這是完全在幕後完成的。 如果無法等待並希望手動移動到新應用程式，則可以從庫中添加新的工作區實例，然後重新配置預配。 工作場所的所有新實例將自動使用新的應用程式版本。 

 
如果您的工作場所集成處於隔離狀態，您需要再次提供有效的權杖，以便我們遷移您。 管理員憑據部分將灰顯，但您可以追加以下內容 **（？Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride_true**） 到 URL 以再次保存憑據。 

https://portal.azure.com/?Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true

#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>如何判斷我的應用程式是否已遷移？ 
遷移應用程式時，將刪除有關向上通信更改的授權部分中的橫幅，並將機密權杖欄位替換為藍色授權按鈕。 

#### <a name="the-admin-credentials-section-is-greyed-out-on-my-application-and-i-cant-save-why"></a>我的應用程式上已灰顯了管理員憑據部分，無法保存。 原因為何？
我們已鎖定現有工作場所客戶的管理認證部分。 將租戶遷移到新的工作區應用程式後，您將能夠再次更新管理員憑據部分。 如果無法等待，可以使用上面的 URL 編輯應用程式。 

 
#### <a name="when-will-these-changes-happen"></a>這些變化何時發生？
工作場所的所有新實例都將使用新的集成/授權方法。 現有集成將在 5 月前逐步遷移。 工作場所團隊將截止日期從 2 月 28 日延長至 5 月 1 日。 

## <a name="capabilities-supported"></a>支援功能
> [!div class="checklist"]
> * 通過 Facebook 在工作場所創建使用者
> * 當 Facebook 不再需要存取權限時，通過 Facebook 刪除使用者
> * 通過 Facebook 使 Azure AD 和工作區之間的使用者屬性保持同步
> * Facebook 單[一登錄](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial)工作場所（推薦）

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租戶](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中具有配置預配[許可權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的使用者帳戶（例如應用程式管理員、雲應用程式管理員、應用程式擁有者或全域管理員）
* 已啟用 Workplace by Facebook 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃預配部署
1. 瞭解[預配服務的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 確定誰將在[預配範圍內](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。
3. 通過 Facebook 確定要[在 Azure AD 和工作區之間映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)哪些資料。

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>步驟 2： 按 Facebook 配置工作區以支援使用 Azure AD 進行預配

在設定並啟用佈建服務之前，您必須決定 Azure AD 中的哪些使用者及/或群組代表需要 Workplace by Facebook 應用程式存取權的使用者。 一旦決定後，您可以依照此處的指示，將這些使用者指派給 Workplace by Facebook 應用程式︰

*   建議將單一 Azure AD 使用者指派給 Workplace by Facebook，以測試佈建設定。 其他使用者及/或群組可能會稍後再指派。

*   將使用者指派給 Workplace by Facebook 時，必須選取有效的使用者角色。 「預設存取」角色不適用於佈建。

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式庫通過 Facebook 添加工作區

從 Azure AD 應用程式庫添加 Facebook 的工作區，以開始管理由 Facebook 預配到工作區。 如果您以前在 Facebook 為 SSO 設置工作區，則可以使用相同的應用程式。 但是，建議在最初測試集成時創建單獨的應用。 在此處瞭解有關從庫中添加應用程式[的更多詳細資訊](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義誰將處於預配範圍 

Azure AD 預配服務允許您根據對應用程式的分配以及或基於使用者/組的屬性來限定誰將預配的範圍。 如果選擇根據分配將預配到應用的範圍，[則可以使用以下步驟將](../manage-apps/assign-user-or-group-access-portal.md)使用者和組分配給應用程式。 如果選擇僅根據使用者或組的屬性預配誰的範圍，則可以使用[此處](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的範圍篩選器。 

* 當通過 Facebook 將使用者和組分配給工作區時，您必須選擇**預設訪問**以外的角色。 具有預設存取權限角色的使用者從預配中排除，並且將在預配日誌中標記為無效許可權。 如果應用程式上唯一可用的角色是預設訪問角色，則可以[更新應用程式清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 從小開始。 在向所有人推出之前，先與一小部分使用者和組進行測試。 將預配範圍設置為分配的使用者和組時，可以通過為應用分配一個或兩個使用者或組來控制這種情況。 當作用域設置為所有使用者和組時，可以指定[基於屬性的範圍篩選器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式**，然後選擇**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Workplace by Facebook]****。

    ![應用程式清單中的 Workplace by Facebook 連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![預配選項卡](common/provisioning.png)

4. 將**預配模式**設置為 **"自動**"。

    ![預配選項卡](common/provisioning-automatic.png)

5. 在 **"管理認證"** 部分下，按一下 **"授權**"。 您將通過 Facebook 的授權頁面重定向到工作場所。 通過 Facebook 使用者名輸入您的工作場所，然後按一下"**繼續"** 按鈕。 按一下 **"測試連接**"以確保 Azure AD 可以通過 Facebook 連接到工作場所。 如果連接失敗，請確保您的"Facebook"帳戶的工作區具有管理員許可權，然後重試。

    ![佈建](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![授權](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. 在 **"通知電子郵件"** 欄位中，輸入應接收預配錯誤通知的個人或組的電子郵件地址，然後選中"**在發生故障時發送電子郵件通知**"核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]****。

8. 在 **"映射"** 部分下，選擇**通過 Facebook 將 Azure 活動目錄使用者同步到工作區**。

9. 在**屬性對應**部分中查看 Facebook 從 Azure AD 同步到工作區的使用者屬性。 選取為 [比對]**** 屬性的屬性會用來比對 Workplace by Facebook 中的使用者帳戶以進行更新作業。 如果您選擇更改[匹配的目標屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，則需要確保 Facebook API 的工作區支援根據該屬性篩選使用者。 選取 [儲存]**** 按鈕以認可所有變更。

   |屬性|類型|
   |---|---|
   |userName|String|
   |displayName|String|
   |作用中|Boolean|
   |title|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |名稱.格式化|String|
   |位址[類型 eq"工作"]格式化|String|
   |addresses[type eq "work"].streetAddress|String|
   |位址_類型 eq"工作"\局部性|String|
   |位址_類型 eq"工作"\區域|String|
   |位址[類型 eq"工作"\國家/地區|String|
   |addresses[type eq "work"].postalCode|String|
   |位址[類型 eq"其他"]格式化|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |externalId|String|
   |preferredLanguage|String|
   |urn：ietf：參數：scim：架構：擴展：企業：2.0：使用者：經理|String|
   |urn：ietf：參數：scim：架構：擴展：企業：2.0：使用者：部門|String|

10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 要啟用 Facebook 為工作區啟用 Azure AD 預配服務，在 **"設置"** 部分將**預配狀態**更改為 **"打開**"。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 通過在 **"設置"** 部分中選擇"**範圍"** 中所需的值，定義您希望通過 Facebook 預配到工作區的使用者和/或組。

    ![佈建範圍](common/provisioning-scope.png)

13. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此操作將開始 **"設置"** 部分中 **"範圍**"中定義的所有使用者和組的初始同步週期。 初始週期執行的時間比後續週期長，只要 Azure AD 預配服務運行，則大約每 40 分鐘執行一次。 

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
配置預配後，請使用以下資源監視部署：

1. 使用[預配日誌](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)確定已成功預配或未成功預配哪些使用者
2. 檢查[進度列](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)以查看預配週期的狀態以及預配週期與完成有多近
3. 如果預配配置似乎處於不正常狀態，則應用程式將進入隔離狀態。 在此處瞭解有關隔離狀態的更多[。](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="troubleshooting-tips"></a>疑難排解秘訣
*  如果您看到使用者未成功創建，並且存在代碼為"1789003"的稽核記錄事件，則表示使用者來自未驗證的域。

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)
