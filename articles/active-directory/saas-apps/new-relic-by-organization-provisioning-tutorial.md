---
title: 教學課程：使用 Azure Active Directory 設定組織的新 Relic 來自動布建使用者 |Microsoft Docs
description: 瞭解如何從 Azure AD 將使用者帳戶自動布建和取消布建至新的 Relic 組織。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 04/14/2020
ms.author: Zhchia
ms.openlocfilehash: c7061be06f8a87c2304b678790a2b1eb63ea81f7
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554415"
---
# <a name="tutorial-configure-new-relic-by-organization-for-automatic-user-provisioning"></a>教學課程：為組織設定新的 Relic 以自動布建使用者

本教學課程說明您需要在新的 Relic 中執行的步驟，以組織和 Azure Active Directory (Azure AD) 設定自動使用者布建。 當設定時，Azure AD 會使用 Azure AD 布建服務，自動將使用者和群組布建並取消布建至 [組織的新 Relic](https://newrelic.com/) 。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支援的功能
> [!div class="checklist"]
> * 依組織在新的 Relic 中建立使用者
> * 當新 Relic 中的使用者不再需要存取權時，請將其移除
> * 在組織 Azure AD 與新的 Relic 之間保持使用者屬性同步
> * 依組織在新的 Relic 中布建群組和群組成員資格
> * [單一登入](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial) 依組織的新 Relic (建議的) 

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租用戶](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中具有設定佈建[權限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的使用者帳戶 (例如，應用程式管理員、雲端應用程式管理員、應用程式擁有者或全域管理員)。 
* 您希望使用者擁有存取權的一或多個位於新 Relic 的帳戶。 

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃佈建部署
1. 了解[佈建服務的運作方式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) \(部分機器翻譯\)。
2. 判斷誰會在[佈建範圍](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)內。
3. 決定 [組織 Azure AD 與新 Relic 之間對應的](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)資料。 

## <a name="step-2-configure-new-relic-by-organization-to-support-provisioning-with-azure-ad"></a>步驟 2： 依組織設定新的 Relic，以支援使用 Azure AD 布建

請與您的帳戶代表合作，或在 support.newrelic.com 上取得支援，為您的組織設定 SCIM 和 SSO。 您將需要提供您的帳戶代表：

- 您的組織名稱
- 與組織相關聯的新 Relic 帳戶識別碼清單

透過此資訊，您的帳戶代表會在新的系統中為您建立組織記錄，並將您的帳戶與組織建立關聯。

您的帳戶代表會提供下列資訊，您將需要為您的身分識別提供者設定新的 Relic SCIM/SSO 應用程式：

- SCIM 端點 (租使用者 URL) 
-  (秘密權杖) SCIM 持有人權杖

SCIM 持有人權杖可讓您在新的 Relic 布建您的使用者，因此請將此值保持安全。 您的帳戶代表會以安全的方式將 SCIM 持有人權杖傳送給您。

## <a name="step-3-add-new-relic-by-organization-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式庫新增依組織的新 Relic

從 Azure AD 應用程式資源庫新增依組織的新 Relic，開始管理依組織提供的新 Relic。 如果您先前已為 SSO 設定新的 Relic，您可以使用相同的應用程式。 不過，建議您在一開始測試整合時，建立個別的應用程式。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)深入了解從資源庫新增應用程式。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義將在佈建範圍內的人員 

Azure AD 佈建服務可供根據對應用程式的指派，或根據使用者/群組的屬性，界定將要佈建的人員。 如果您選擇根據指派來界定將佈建至應用程式的人員，您可以使用下列[步驟](../manage-apps/assign-user-or-group-access-portal.md)將使用者和群組指派給應用程式。 如果您選擇僅根據使用者或群組的屬性來界定將要佈建的人員，可以使用如[這裡](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的範圍篩選條件。 

* 將使用者和群組指派給組織的新 Relic 時，您必須選取 **預設存取**以外的角色。 具有預設存取角色的使用者會從佈建中排除，而且會在佈建記錄中被標示為沒有效率。 如果應用程式上唯一可用的角色是 [預設存取] 角色，您可以[更新應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) \(部分機器翻譯\) 以新增其他角色。 

* 從小規模開始。 在推出給所有人之前，先使用一小部分的使用者和群組進行測試。 當佈建範圍設為已指派的使用者和群組時，您可將一或兩個使用者或群組指派給應用程式來控制這點。 當範圍設為所有使用者和群組時，您可指定[以屬性為基礎的範圍篩選條件](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-new-relic-by-organization"></a>步驟 5。 設定自動使用者布建至新的 Relic 組織 

此節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 TestApp 中建立、更新和停用使用者和/或群組。

### <a name="to-configure-automatic-user-provisioning-for-new-relic-by-organization-in-azure-ad"></a>若要為 Azure AD 中的組織設定新 Relic 的自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [ **依組織新增 Relic**]。

    ![應用程式清單中的 New Relic 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![佈建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![[佈建] 索引標籤](common/provisioning-automatic.png)

5. 在 [管理員認證] 區段下的 [租用戶 URL] 中輸入 `https://scim-provisioning.service.newrelic.com/scim/v2`。 輸入稍早在 **秘密權杖**中取出的 SCIM authentication 權杖值。 按一下 [ **測試連接** ] 以確保 Azure AD 可以連線至新的 Relic。 如果連接失敗，請確定您的新 Relic 帳戶具有系統管理員許可權，然後再試一次。

    ![佈建](./media/new-relic-by-organization-provisioning-tutorial/provisioning.png)

6. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者到組織的新 Relic**。

9. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理到新 Relic 的使用者屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對組織中新 Relic 的使用者帳戶，以進行更新作業。 如果您選擇變更相符的 [目標屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，您將需要確保依組織 API 的新 Relic 支援根據該屬性篩選使用者。 選取 [儲存] 按鈕以認可所有變更。

   |屬性|類型|
   |---|---|
   |userName|String|
   |externalId|String|
   |作用中|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.formatted|String|
   |timezone|String|

10. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 群組至組織的新 Relic**]。

11. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理至新 Relic 的群組屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對組織中新 Relic 的群組以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

      |屬性|類型|
      |---|---|
      |displayName|String|
      |externalId|String|
      |members|參考|

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要為組織的新 Relic 啟用 Azure AD 布建服務，請在 [**設定**] 區段中，將 [布建**狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要依組織布建到新 Relic 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中所定義所有使用者和群組啟動首次同步處理週期。 初始週期會比後續週期花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
設定佈建後，請使用下列資源來監視您的部署：

* 使用[佈建記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) \(部分機器翻譯\) 來判斷哪些使用者已佈建成功或失敗
* 檢查[進度列](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) \(部分機器翻譯\) 來查看佈建週期的狀態，以及其接近完成的程度
* 如果佈建設定似乎處於狀況不良的狀態，應用程式將會進入隔離狀態。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) \(部分機器翻譯\) 深入了解隔離狀態。  


## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)
