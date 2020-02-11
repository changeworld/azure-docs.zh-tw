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
ms.openlocfilehash: d7d8a7881c00427023e5f174461b3d8b24d83444
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121456"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>教學課程：設定 Workplace by Facebook 來自動佈建使用者

本教學課程說明您需要在 Workplace by Facebook 和 Azure Active Directory （Azure AD）中執行的步驟，以設定自動使用者布建。 設定之後，Azure AD 會使用 Azure AD 布建服務，自動布建及取消布建使用者和群組到[Workplace By Facebook](https://work.workplace.com/) 。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。

## <a name="migrating-to-the-new-workplace-by-facebook-application"></a>遷移至新的 Workplace by Facebook 應用程式
如果您已經與 Workplace by Facebook 整合，請參閱下一節，瞭解即將推出的變更。 如果您是第一次設定 Workplace by Facebook，可以略過本節並移至支援的功能。 

#### <a name="whats-changing"></a>有哪些變更項目？
* Azure AD 端上的變更：在 Workplace 中布建使用者的授權方法，過去是長期的秘密權杖。 很快地，您會看到授權方法已變更為 OAuth 授權授與。 
* 工作場所端的變更：先前 Azure AD 應用程式是 Workplace by Facebook 中的自訂整合。 現在您會看到工作場所整合目錄中的 Azure AD，做為協力廠商應用程式。 

 

#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>將現有的自訂整合遷移到新的應用程式時，我需要做什麼？
如果您現有的工作地點整合具有有效的權杖，則**不需要採取任何動作**。 我們會每週自動將客戶遷移至新的應用程式。 這會在幕後完全完成。 如果您無法等候，而想要手動移至新的應用程式，您可以從資源庫新增 Workplace 的新實例，然後重新設定布建。 Workplace 的所有新實例將會自動使用新的應用程式版本。 

 
如果您的工作地點整合是隔離的，您必須再次提供有效的權杖，才能讓我們遷移您。 [系統管理員認證] 區段會呈現灰色，但是您可以附加下列**專案（？Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride = true**）至您的 URL，以再次儲存認證。 

https://portal.azure.com/?Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true

#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>如何判斷我的應用程式是否已遷移？ 
當您的應用程式遷移時，將會移除 [授權] 區段中有關 upcomming 變更的橫幅，而 [秘密權杖] 欄位將會取代為藍色授權按鈕。 

#### <a name="the-admin-credentials-section-is-greyed-out-on-my-application-and-i-cant-save-why"></a>我的應用程式上的 [管理員認證] 區段呈現灰色，因此無法儲存。 為什麼？
我們已鎖定現有工作場所客戶的 [系統管理員認證] 區段。 當您的租使用者已遷移至新的 Workplace 應用程式時，您將能夠再次更新 [管理員認證] 區段。 如果您無法等待，您可以使用上述 URL 來編輯您的應用程式。 

 
#### <a name="when-will-these-changes-happen"></a>這些變更何時會發生？
Workplace 的所有新實例都已使用新的整合/授權方法。 現有的整合將于二月逐步遷移。 所有租使用者的遷移將會在該月結束後完成。 

## <a name="capabilities-supported"></a>支援的功能
> [!div class="checklist"]
> * 在 Workplace by Facebook 中建立使用者
> * 當使用者不再需要存取權時，移除 Workplace by Facebook 中的使用者
> * 在 Azure AD 與 Workplace by Facebook 之間保持使用者屬性同步
> * [單一登入](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial)至 Workplace by Facebook （建議選項）

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租使用者](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中的使用者帳戶，具有設定布建的[許可權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)（例如，應用程式系統管理員、雲端應用程式系統管理員、應用程式擁有者或全域管理員）
* 已啟用 Workplace by Facebook 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃您的布建部署
1. 深入瞭解布建[服務的運作方式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 判斷誰會在布建[範圍](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)內。
3. 決定要[在 Azure AD 與 Workplace By Facebook 之間對應的](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)資料。

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定 Workplace by Facebook 以支援以 Azure AD 進行布建

在設定並啟用佈建服務之前，您必須決定 Azure AD 中的哪些使用者及/或群組代表需要 Workplace by Facebook 應用程式存取權的使用者。 一旦決定後，您可以依照此處的指示，將這些使用者指派給 Workplace by Facebook 應用程式︰

*   建議將單一 Azure AD 使用者指派給 Workplace by Facebook，以測試佈建設定。 其他使用者及/或群組可能會稍後再指派。

*   將使用者指派給 Workplace by Facebook 時，必須選取有效的使用者角色。 「預設存取」角色不適用於佈建。

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式庫新增 Workplace by Facebook

從 Azure AD 應用程式庫新增 Workplace by Facebook，以開始管理布建至 Workplace by Facebook。 如果您先前已針對 SSO 設定 Workplace by Facebook，則可以使用相同的應用程式。 不過，建議您在一開始測試整合時，建立個別的應用程式。 在[這裡](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)深入瞭解如何從資源庫新增應用程式。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義將在布建範圍內的人員 

Azure AD 布建服務可讓您根據指派給應用程式的人員，或根據使用者/群組的屬性，來界定將布建的物件。 如果您選擇根據指派將布建到您的應用程式的範圍，您可以使用下列[步驟](../manage-apps/assign-user-or-group-access-portal.md)，將使用者和群組指派給應用程式。 如果您選擇將僅根據使用者或群組的屬性布建的使用者範圍，您可以使用範圍篩選器，如[這裡](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述。 

* 將使用者和群組指派給 Workplace by Facebook 時，您必須選取 [**預設存取**] 以外的角色。 具有預設存取角色的使用者會從布建中排除，且在布建記錄中會被標示為不有效率。 如果應用程式上唯一可用的角色是預設存取角色，您可以[更新應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)來新增其他角色。 

* 從小規模開始。 在推出給所有人之前，先使用一小組的使用者和群組進行測試。 當布建的範圍設定為 [已指派的使用者和群組] 時，您可以藉由將一或兩個使用者或群組指派給應用程式來控制此項。 當 [範圍] 設定為 [所有使用者和群組] 時，您可以指定以[屬性為基礎的範圍篩選器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [**企業應用程式**]，然後選取 [**所有應用程式**]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Workplace by Facebook]。

    ![應用程式清單中的 Workplace by Facebook 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![布建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![布建索引標籤](common/provisioning-automatic.png)

5. 在 [**管理員認證**] 區段下，按一下 [**授權**]。 系統會將您重新導向至 Workplace by Facebook 的授權頁面。 輸入您的 Workplace by Facebook 使用者名稱，然後按一下 [**繼續**] 按鈕。 按一下 [**測試連接**] 以確保 Azure AD 可以連接到 Workplace by Facebook。 如果連線失敗，請確定您的 Workplace by Facebook 帳戶具有系統管理員許可權，然後再試一次。

    ![佈建](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![授權](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. 在 [**通知電子郵件**] 欄位中，輸入應收到布建錯誤通知之個人或群組的電子郵件地址，然後選取 [**發生失敗時傳送電子郵件通知**] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者至 Workplace by Facebook**]。

9. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步到 Workplace by Facebook 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 Workplace by Facebook 中的使用者帳戶以進行更新作業。 如果您選擇變更相符的[目標屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，您必須確定 Workplace BY Facebook API 支援根據該屬性來篩選使用者。 選取 [儲存] 按鈕以認可所有變更。

   |屬性|類型|
   |---|---|
   |userName|String|
   |displayName|String|
   |作用中|Boolean|
   |title|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |名稱。格式化|String|
   |位址 [類型 eq "work"]。已格式化|String|
   |addresses[type eq "work"].streetAddress|String|
   |位址 [類型 eq "work"]. 位置|String|
   |位址 [類型 eq "work"]. region|String|
   |位址 [類型 eq "work"]. country|String|
   |addresses[type eq "work"].postalCode|String|
   |位址 [類型 eq "other"]。已格式化|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |externalId|String|
   |preferredLanguage|String|
   |urn： ietf： params： scim：架構：擴充功能： enterprise：2.0： User： manager|String|
   |urn： ietf： params： scim：架構：擴充功能： enterprise：2.0： User：部門|String|

10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 若要啟用 Workplace by Facebook 的 Azure AD 布建服務，請在 [**設定**] 區段中，將 [布建**狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建到 Workplace by Facebook 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

13. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會啟動 [**設定**] 區段的 [**範圍**] 中定義的所有使用者和群組的初始同步處理迴圈。 初始週期比後續迴圈花費更多時間執行，只要 Azure AD 布建服務正在執行，這大約每40分鐘就會發生一次。 

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
設定布建之後，請使用下列資源來監視您的部署：

1. 使用布建[記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)來判斷哪些使用者已成功布建或失敗
2. 檢查[進度](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)列以查看布建週期的狀態，以及關閉其完成的方式
3. 如果布建設定似乎處於狀況不良的狀態，應用程式將會進入隔離。 [在這裡](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)深入瞭解隔離狀態。

## <a name="troubleshooting-tips"></a>疑難排解秘訣
*  如果您看到使用者未成功建立，而且有一個具有代碼 "1789003" 的 audit 記錄事件，表示使用者來自未驗證的網域。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)
