---
title: 教學課程：使用 Azure Active Directory 設定 OpenText 目錄服務來自動布建使用者 |Microsoft Docs
description: 瞭解如何從 Azure AD 將使用者帳戶自動布建和取消布建至 OpenText 目錄服務。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: ad55ba5f-c56c-4ed0-bdfd-163d2883ed80
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: c0858c2e5b1ada866d252e45113f0f90b73caaf8
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92428842"
---
# <a name="tutorial-configure-opentext-directory-services-for-automatic-user-provisioning"></a>教學課程：設定 OpenText 目錄服務來自動布建使用者

本教學課程說明在 OpenText 目錄服務和 Azure Active Directory (Azure AD) 設定自動使用者布建時所需執行的步驟。 當設定時，Azure AD 會自動將使用者和群組布建並取消布建，以使用 Azure AD 布建服務 OpenText 目錄服務。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支援的功能
> [!div class="checklist"]
> * 在 OpenText 目錄服務中建立使用者
> * 當使用者不再需要存取權時，請移除 OpenText 目錄服務中的使用者
> * 在 Azure AD 與 OpenText 目錄服務之間保持使用者屬性同步
> * 在 OpenText 目錄服務中布建群組和群組成員資格
> * [單一登入](https://docs.microsoft.com/azure/active-directory/saas-apps/opentext-directory-services-tutorial) OpenText 目錄服務 (建議的) 

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租用戶](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中具有設定佈建[權限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的使用者帳戶 (例如，應用程式管理員、雲端應用程式管理員、應用程式擁有者或全域管理員)。 
* Azure AD 可存取的 OTDS 安裝。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃佈建部署
1. 了解[佈建服務的運作方式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) \(部分機器翻譯\)。
2. 判斷誰會在[佈建範圍](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)內。
3. 判斷要 [在 Azure AD 與 OpenText 目錄服務之間對應](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的資料。 

## <a name="step-2-configure-opentext-directory-services-to-support-provisioning-with-azure-ad"></a>步驟 2： 使用 Azure AD 設定 OpenText 目錄服務以支援布建

> [!NOTE]
> 下列步驟適用于 OpenText 目錄服務安裝。 不適用於 OpenText CoreShare 或 OpenText OT2 租使用者。

1. 建立專用的機密 **OAuth 用戶端**。
2. 設定長 **存取權杖存留期**。

      ![存取權杖生命](media/open-text-directory-services-provisioning-tutorial/token-life.png)

3. 請勿指定任何重新導向 Url。 這些都不是必要的。 
4. OTDS 將會產生並顯示 **用戶端密碼**。 將 **用戶端識別碼** 和 **用戶端密碼** 儲存在安全的位置。

      ![用戶端密碼](media/open-text-directory-services-provisioning-tutorial/client-secret.png)

5. 針對要從 Azure AD 同步處理的使用者和群組建立磁碟分割。

      ![分割區頁面](media/open-text-directory-services-provisioning-tutorial/partition.png)

6. 將系統管理許可權授與您在磁碟分割上建立的 OAuth 用戶端，您將使用這些資料分割來進行同步處理的 Azure AD 使用者和群組。    
      * 磁碟分割 > 動作-> 編輯系統管理員

      ![管理員頁面](media/open-text-directory-services-provisioning-tutorial/administrator.png)

5. 必須在 Azure AD 中取出和設定秘密權杖。 您可以使用任何 HTTP 用戶端應用程式。 以下是使用 OTDS 中所包含的 Swagger API 應用程式取出的步驟。
      * 在網頁瀏覽器中，移至 {OTDS URL}/otdsws/oauth2
      * 移至/token，然後按一下右上方的鎖定圖示。 分別輸入先前抓取為使用者名稱和密碼的 OAuth 用戶端識別碼和秘密。 按一下 [授權]。

      ![授權按鈕](media/open-text-directory-services-provisioning-tutorial/authorization.png)

6. 選取 grant_type 的 **client_credentials** ，然後按一下 [ **執行**]。

      ![執行檔案名](media/open-text-directory-services-provisioning-tutorial/execute.png)

7. 回應中的存取權杖應使用於 Azure AD 的 [ **秘密權杖** ] 欄位中。

      ![存取權杖](media/open-text-directory-services-provisioning-tutorial/access-token.png)

## <a name="step-3-add-opentext-directory-services-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式資源庫新增 OpenText 目錄服務

從 Azure AD 應用程式資源庫新增 OpenText 目錄服務，以開始管理 OpenText 目錄服務的布建。 如果您先前已設定 SSO 的 OpenText 目錄服務，則可以使用相同的應用程式。 不過，建議您在一開始測試整合時，建立個別的應用程式。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)深入了解從資源庫新增應用程式。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義將在佈建範圍內的人員 

Azure AD 佈建服務可供根據對應用程式的指派，或根據使用者/群組的屬性，界定將要佈建的人員。 如果您選擇根據指派來界定將佈建至應用程式的人員，您可以使用下列[步驟](../manage-apps/assign-user-or-group-access-portal.md)將使用者和群組指派給應用程式。 如果您選擇僅根據使用者或群組的屬性來界定將要佈建的人員，可以使用如[這裡](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的範圍篩選條件。 

* 將使用者和群組指派給 OpenText 目錄服務時，您必須選取 **預設存取**以外的角色。 具有預設存取角色的使用者會從佈建中排除，而且會在佈建記錄中被標示為沒有效率。 如果應用程式上唯一可用的角色是 [預設存取] 角色，您可以[更新應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) \(部分機器翻譯\) 以新增其他角色。 

* 從小規模開始。 在推出給所有人之前，先使用一小部分的使用者和群組進行測試。 當佈建範圍設為已指派的使用者和群組時，您可將一或兩個使用者或群組指派給應用程式來控制這點。 當範圍設為所有使用者和群組時，您可指定[以屬性為基礎的範圍篩選條件](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-opentext-directory-services"></a>步驟 5。 設定自動使用者布建至 OpenText 目錄服務 

此節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 TestApp 中建立、更新和停用使用者和/或群組。

### <a name="to-configure-automatic-user-provisioning-for-opentext-directory-services-in-azure-ad"></a>若要在 Azure AD 中設定 OpenText 目錄服務的自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [OpenText Directory Services]。

    ![應用程式清單中的 OpenText 目錄服務連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![佈建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![自動布建索引標籤](common/provisioning-automatic.png)

5. 在 [系統 **管理員認證** ] 區段下，輸入您的 OpenText 目錄服務租使用者 URL
   * 非專屬租使用者 url： {OTDS URL}/scim/{partitionName}
   * 特定租使用者 url： {OTDS URL}/otdstenant/{tenantID}/scim/{partitionName}

6. 輸入從步驟2取出的秘密權杖。 按一下 [ **測試連接** ] 以確保 Azure AD 可以連線到 OpenText 目錄服務。 如果連接失敗，請確定您的 OpenText 目錄服務帳戶具有系統管理員許可權，然後再試一次。

      ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者以 OpenText 目錄服務**]。

9. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理到 OpenText 目錄服務的使用者屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對 OpenText 目錄服務中的使用者帳戶，以進行更新作業。 如果您選擇變更相符的 [目標屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，您將需要確定 OpenText 目錄服務 API 支援根據該屬性篩選使用者。 選取 [儲存] 按鈕以認可所有變更。

   |屬性|類型|
   |---|---|
   |userName|String|
   |作用中|Boolean|
   |displayName|String|
   |title|String|
   |emails[type eq "work"].value|String|
   |preferredLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |addresses[type eq "work"].formatted|String|
   |addresses[type eq "work"].streetAddress|String|
   |addresses[type eq "work"].locality|String|
   |addresses[type eq "work"].region|String|
   |addresses[type eq "work"].postalCode|String|
   |addresses[type eq "work"].country|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|參考| 

10. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 群組以 OpenText 目錄服務**]。

11. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理到 OpenText 目錄服務的群組屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對 OpenText 目錄服務中的群組以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

      |屬性|類型|
      |---|---|
      |displayName|String|
      |externalId|String|
      |members|參考|

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要啟用 OpenText 目錄服務的 Azure AD 布建服務，請在 [**設定**] 區段中，將 [布建**狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建到 OpenText 目錄服務的使用者和/或群組。

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
