---
title: 教學課程：使用 Azure Active Directory 設定 Oracle 雲端基礎結構主控台來自動布建使用者 |Microsoft Docs
description: 瞭解如何從 Azure AD 將使用者帳戶自動布建和取消布建至 Oracle 雲端基礎結構主控台。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 0c3d68698621fe963074c7216e4636208d93deca
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543863"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>教學課程：設定 Oracle 雲端基礎結構主控台來自動布建使用者

本教學課程說明在 Oracle 雲端基礎結構主控台和 Azure Active Directory (Azure AD) 設定自動使用者布建時所需執行的步驟。 當設定時，Azure AD 會使用 Azure AD 布建服務，自動將使用者和群組布建並取消布建至 [Oracle 雲端基礎結構主控台](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) 。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支援的功能
> [!div class="checklist"]
> * 在 Oracle 雲端基礎結構主控台中建立使用者
> * 當 Oracle Cloud 基礎結構主控台中的使用者不再需要存取權時，請將其移除
> * 在 Azure AD 和 Oracle 雲端基礎結構主控台之間保持使用者屬性同步
> * 在 Oracle 雲端基礎結構主控台中布建群組和群組成員資格
> * [單一登入](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial) Oracle 雲端基礎結構主控台 (建議的) 

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租用戶](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中具有設定佈建[權限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的使用者帳戶 (例如，應用程式管理員、雲端應用程式管理員、應用程式擁有者或全域管理員)。 
* Oracle 雲端基礎結構控制 [租](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton)使用者。
* 具有系統管理員許可權的 Oracle 雲端基礎結構控制中的使用者帳戶。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃佈建部署
1. 了解[佈建服務的運作方式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) \(部分機器翻譯\)。
2. 判斷誰會在[佈建範圍](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)內。
3. 判斷要 [在 Azure AD 與 Oracle 雲端基礎結構主控台之間對應](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的資料。 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定 Oracle 雲端基礎結構主控台，以支援 Azure AD 的布建

1. 登入 Oracle Cloud 基礎結構主控台的系統管理員入口網站。 在畫面的左上角，流覽至 [ **Identity > 同盟**]。

    ![Oracle 系統管理員](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. 按一下 [Oracle 身分識別雲端服務主控台] 旁邊頁面上顯示的 URL。

    ![Oracle URL](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. 按一下 [ **新增識別提供者** ]，以建立新的身分識別提供者。 儲存 IdP 識別碼，以作為租使用者 URL 的一部分。按一下 [ **應用程式** ] 索引標籤旁邊的加號圖示，以建立 OAuth 用戶端，並授與 IDCS Identity Domain Administrator AppRole。

    ![Oracle Cloud 圖示](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. 請依照下列螢幕擷取畫面來設定您的應用程式。 完成設定後，按一下 [ **儲存**]。

    ![Oracle 設定](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Oracle 權杖發行原則](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. 在應用程式的 [設定] 索引標籤下，展開 [ **一般資訊** ] 選項以取得用戶端識別碼和用戶端密碼。

    ![產生 Oracle 權杖](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. 若要產生秘密權杖，以 Base64 編碼用戶端識別碼和用戶端密碼，格式為 **用戶端識別碼：用戶端密碼**。 儲存秘密權杖。 在 Azure 入口網站中，您的 Oracle 雲端基礎結構主控台應用程式的 [布建] 索引標籤中，會在 [ **秘密權杖** ] 欄位中輸入此值。

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式資源庫新增 Oracle 雲端基礎結構主控台

從 Azure AD 應用程式資源庫新增 Oracle 雲端基礎結構主控台，以開始管理布建至 Oracle 雲端基礎結構主控台。 如果您先前已設定適用于 SSO 的 Oracle 雲端基礎結構主控台，您可以使用相同的應用程式。 不過，建議您在一開始測試整合時，建立個別的應用程式。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)深入了解從資源庫新增應用程式。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義將在佈建範圍內的人員 

Azure AD 佈建服務可供根據對應用程式的指派，或根據使用者/群組的屬性，界定將要佈建的人員。 如果您選擇根據指派來界定將佈建至應用程式的人員，您可以使用下列[步驟](../manage-apps/assign-user-or-group-access-portal.md)將使用者和群組指派給應用程式。 如果您選擇僅根據使用者或群組的屬性來界定將要佈建的人員，可以使用如[這裡](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的範圍篩選條件。 

* 將使用者和群組指派給 Oracle 雲端基礎結構主控台時，您必須選取 **預設存取**以外的角色。 具有預設存取角色的使用者會從佈建中排除，而且會在佈建記錄中被標示為沒有效率。 如果應用程式上唯一可用的角色是 [預設存取] 角色，您可以[更新應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) \(部分機器翻譯\) 以新增其他角色。 

* 從小規模開始。 在推出給所有人之前，先使用一小部分的使用者和群組進行測試。 當佈建範圍設為已指派的使用者和群組時，您可將一或兩個使用者或群組指派給應用程式來控制這點。 當範圍設為所有使用者和群組時，您可指定[以屬性為基礎的範圍篩選條件](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>步驟 5。 設定自動使用者布建至 Oracle 雲端基礎結構主控台 

此節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 TestApp 中建立、更新和停用使用者和/或群組。

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>若要在 Azure AD 中設定 Oracle 雲端基礎結構主控台的自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Oracle Cloud Infrastructure Console]****。

    ![應用程式清單中的 Oracle 雲端基礎結構主控台連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![佈建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![佈建索引標籤](common/provisioning-automatic.png)

5. 在 [系統 **管理員認證** ] 區段下，以格式輸入 **租使用者 URL** `https://<IdP ID>.identity.oraclecloud.com/admin/v1` 。 例如 `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`。 輸入稍早在 [祕密權杖] 中所擷取的祕密權杖值。 按一下 [ **測試連接** ] 以確保 Azure AD 可以連線至 Oracle 雲端基礎結構主控台。 如果連接失敗，請確定您的 Oracle 雲端基礎結構主控台帳戶具有系統管理員許可權，然後再試一次。

    ![佈建](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者至 Oracle 雲端基礎結構主控台**]。

9. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理到 Oracle 雲端基礎結構主控台的使用者屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對 Oracle 雲端基礎結構主控台中的使用者帳戶，以進行更新作業。 如果您選擇變更相符的 [目標屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，您將需要確定 Oracle 雲端基礎結構主控台 API 支援根據該屬性篩選使用者。 選取 [儲存] 按鈕以認可所有變更。

      |屬性|類型|
      |---|---|
      |displayName|String|
      |userName|String|
      |作用中|Boolean|
      |title|String|
      |emails[type eq "work"].value|String|
      |preferredLanguage|String|
      |name.givenName|String|
      |name.familyName|String|
      |addresses[type eq "work"].formatted|String|
      |addresses[type eq "work"].locality|String|
      |addresses[type eq "work"].region|String|
      |addresses[type eq "work"].postalCode|String|
      |addresses[type eq "work"].country|String|
      |addresses[type eq "work"].streetAddress|String|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|參考|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
      |urn： ietf： params： scim：架構： oracle： idcs： extension： user： User： bypassNotification|布林值|
      |urn： ietf： params： scim：架構： oracle： idcs： extension： user： User： isFederatedUser|布林值|

10. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 群組至 Oracle 雲端基礎結構主控台**]。

11. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理到 Oracle 雲端基礎結構主控台的群組屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對 Oracle 雲端基礎結構主控台中的群組，以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

      |屬性|類型|
      |---|---|
      |displayName|String|
      |externalId|String|
      |members|參考|

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要啟用 Oracle 雲端基礎結構主控台的 Azure AD 布建服務，請在 [**設定**] 區段中，將布建**狀態**變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建至 Oracle 雲端基礎結構主控台的使用者和/或群組。

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
