---
title: 教學課程：使用 Azure Active Directory 設定 Adobe 身分識別管理來自動布建使用者 |Microsoft Docs
description: 瞭解如何將使用者帳戶從 Azure AD 自動布建和取消布建至 Adobe Identity Management。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 6ae05dc7-1265-44b4-a20c-512b5218b9d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2020
ms.author: Zhchia
ms.openlocfilehash: 00febcfa33751214a97e45f9178d8c0eb1eedc05
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428608"
---
# <a name="tutorial-configure-adobe-identity-management-for-automatic-user-provisioning"></a>教學課程：設定自動使用者布建的 Adobe Identity Management

本教學課程說明在 Adobe 身分識別管理和 Azure Active Directory (Azure AD) 設定自動使用者布建時所需執行的步驟。 當設定時，Azure AD 會使用 Azure AD 布建服務，自動將使用者和群組布建和取消布建至 Adobe 身分識別管理。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支援的功能
> [!div class="checklist"]
> * 在 Adobe Identity Management 中建立使用者
> * 當 Adobe Identity Management 不再需要存取權時，請將使用者移除
> * 在 Azure AD 與 Adobe Identity Management 之間保持使用者屬性同步
> * 在 Adobe 身分識別管理中布建群組和群組成員資格
> * 單一登入 Adobe Identity Management (建議的) 

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租用戶](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)。
* Azure AD 中具有設定佈建[權限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的使用者帳戶 (例如，應用程式管理員、雲端應用程式管理員、應用程式擁有者或全域管理員)。 
* [Adobe 管理主控台](https://adminconsole.adobe.com/)中具有已驗證網域的同盟目錄。

> [!NOTE]
> 如果您的組織使用使用者同步工具或 UMAPI 整合，您必須先暫停整合。 然後，新增 Azure AD 自動布建，以從 Azure 入口網站自動進行使用者管理。 一旦 Azure AD 自動布建完成設定並執行，您就可以完全移除使用者同步工具或 UMAPI 整合。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃佈建部署
1. 了解[佈建服務的運作方式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) \(部分機器翻譯\)。
2. 判斷誰會在[佈建範圍](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)內。
3. 判斷要 [在 Azure AD 與 Adobe Identity Management 之間對應](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的資料。 

## <a name="step-2-configure-adobe-identity-management-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定 Adobe 身分識別管理以支援使用 Azure AD 布建

1. 登入 [Adobe 管理主控台](https://adminconsole.adobe.com/)。 流覽至 [ **設定] > 目錄詳細資料 > 同步** ]。 

2. 按一下 [ **加入同步** ]。

      ![加](media/adobe-identity-management-provisioning-tutorial/add-sync.png)

3. **從 Microsoft Azure** 選取 [同步處理使用者]，然後按 **[下一步]** 。

      ![同步](media/adobe-identity-management-provisioning-tutorial/sync-users.png)

4. 複製並儲存 **租使用者 URL** 和 **秘密權杖** 。 這些值將會在 [ **租使用者 URL** ] 和 [ **秘密權杖** ] 欄位中輸入 Azure 入口網站中 Adobe Identity Management 應用程式的 [布建] 索引標籤中。

      ![同步](media/adobe-identity-management-provisioning-tutorial/token.png)

## <a name="step-3-add-adobe-identity-management-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式資源庫新增 Adobe 身分識別管理

從 Azure AD 應用程式資源庫新增 Adobe Identity Management，以開始管理布建至 Adobe 身分識別管理。 如果您先前已設定 SSO 的 Adobe Identity Management，您可以使用相同的應用程式。 不過，建議您在一開始測試整合時，建立個別的應用程式。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)深入了解從資源庫新增應用程式。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義將在佈建範圍內的人員 

Azure AD 佈建服務可供根據對應用程式的指派，或根據使用者/群組的屬性，界定將要佈建的人員。 如果您選擇根據指派來界定將佈建至應用程式的人員，您可以使用下列[步驟](../manage-apps/assign-user-or-group-access-portal.md)將使用者和群組指派給應用程式。 如果您選擇僅根據使用者或群組的屬性來界定將要佈建的人員，可以使用如[這裡](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的範圍篩選條件。 

* 將使用者和群組指派給 Adobe Identity Management 時，您必須選取 **預設存取** 以外的角色。 具有預設存取角色的使用者會從佈建中排除，而且會在佈建記錄中被標示為沒有效率。 如果應用程式上唯一可用的角色是 [預設存取] 角色，您可以[更新應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) \(部分機器翻譯\) 以新增其他角色。 

* 從小規模開始。 在推出給所有人之前，先使用一小部分的使用者和群組進行測試。 當佈建範圍設為已指派的使用者和群組時，您可將一或兩個使用者或群組指派給應用程式來控制這點。 當範圍設為所有使用者和群組時，您可指定[以屬性為基礎的範圍篩選條件](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-adobe-identity-management"></a>步驟 5。 設定自動使用者布建至 Adobe Identity Management 

此節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 TestApp 中建立、更新和停用使用者和/或群組。

### <a name="to-configure-automatic-user-provisioning-for-adobe-identity-management-in-azure-ad"></a>若要在 Azure AD 中設定 Adobe 身分識別管理的自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Adobe 身分識別管理]。

    ![應用程式清單中的 Adobe 身分識別管理連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![佈建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![自動布建索引標籤](common/provisioning-automatic.png)

5. 在 [系統 **管理員認證** ] 區段下，輸入您稍早在步驟2中抓取的 Adobe Identity Management 租使用者 URL 和秘密權杖。 按一下 [ **測試連接** ]，以確保 Azure AD 可以連線至 Adobe 身分識別管理。 如果連接失敗，請確定您的 Adobe 身分識別管理帳戶具有系統管理員許可權，然後再試一次。

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]。

8. **在 [對應** ] 區段下，選取 [ **同步處理 Azure Active Directory 使用者至 Adobe 身分識別管理** ]。

9. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理至 Adobe Identity Management 的使用者屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對 Adobe Identity Management 中的使用者帳戶，以進行更新作業。 如果您選擇變更相符的 [目標屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，您將必須確定 Adobe Identity 管理 API 支援根據該屬性篩選使用者。 選取 [儲存] 按鈕以認可所有變更。

   |屬性|類型|
   |---|---|
   |userName|String|
   |emails[type eq "work"].value|String|
   |作用中|Boolean|
   |addresses[type eq "work"].country|String|
   |name.givenName|String|
   |name.familyName|String|
   |urn： ietf： params： scim：架構： extension： Adobe：2.0： User： emailAliases|String|

10. **在 [對應** ] 區段下，選取 [ **同步處理 Azure Active Directory 群組至 Adobe 身分識別管理** ]。

11. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理至 Adobe Identity Management 的群組屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對 Adobe Identity Management 中的群組以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

      |屬性|類型|
      |---|---|
      |displayName|String|
      |members|參考|

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要啟用 Adobe Identity Management 的 Azure AD 布建服務，請在 [ **設定** ] 區段中，將 [布建 **狀態** ] 變更為 [ **開啟** ]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 在 [ **設定** ] 區段的 [ **範圍** ] 中選擇所需的值，以定義您想要布建至 Adobe 身分識別管理的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]  。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中所定義所有使用者和群組啟動首次同步處理週期。 初始週期會比後續週期花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
設定佈建後，請使用下列資源來監視您的部署：

1. 使用[佈建記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) \(部分機器翻譯\) 來判斷哪些使用者已佈建成功或失敗
2. 檢查[進度列](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) \(部分機器翻譯\) 來查看佈建週期的狀態，以及其接近完成的程度
3. 如果佈建設定似乎處於狀況不良的狀態，應用程式將會進入隔離狀態。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)深入了解隔離狀態。  

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)
