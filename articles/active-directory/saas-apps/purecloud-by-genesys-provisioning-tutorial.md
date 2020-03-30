---
title: 教程：按 Genesys 配置 PureCloud，以便使用 Azure 活動目錄進行自動使用者預配 |微軟文檔
description: 瞭解如何通過 Genesys 自動預配和取消將使用者帳戶從 Azure AD 預配到 PureCloud。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 5f04b88b-117e-40da-a15c-e3732b240d5d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2020
ms.author: Zhchia
ms.openlocfilehash: 119690b9046821ab538d879e1209c6ef77277370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370676"
---
# <a name="tutorial-configure-purecloud-by-genesys-for-automatic-user-provisioning"></a>教程：按 Genesys 配置 PureCloud 以進行自動使用者預配

本教程介紹需要在 Genesys 和 Azure 活動目錄 （Azure AD） 的 PureCloud 中執行的步驟來配置自動使用者預配。 配置後，Azure AD 使用 Azure AD 預配服務，[通過 Genesys](https://www.genesys.com)自動預配和取消向 PureCloud 預配使用者和組。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支援功能
> [!div class="checklist"]
> * 通過創世紀在 PureCloud 中創建使用者
> * 在 PureCloud 中刪除使用者，當他們不再需要訪問時，按 Genesys 將其刪除
> * 按 Genesys 使使用者屬性在 Azure AD 和 PureCloud 之間保持同步
> * 按 Genesys 在 PureCloud 中預配組和組成員身份
> * Genesys 單[次登錄](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial)PureCloud（推薦）

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租戶](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中具有配置預配[許可權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的使用者帳戶（例如應用程式管理員、雲應用程式管理員、應用程式擁有者或全域管理員）。 
* 純雲[組織](https://help.mypurecloud.com/?p=81984)。
* 具有創建 Oauth 用戶端[的許可權](https://help.mypurecloud.com/?p=24360)的使用者。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃預配部署
1. 瞭解[預配服務的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 確定誰將在[預配範圍內](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。
3. [通過 Genesys 確定要在 Azure AD 和 PureCloud 之間映射哪些資料](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)。 

## <a name="step-2-configure-purecloud-by-genesys-to-support-provisioning-with-azure-ad"></a>步驟 2： 按 Genesys 配置純雲以支援使用 Azure AD 進行預配

1. 創建在純雲組織中配置的[Oauth 用戶端](https://help.mypurecloud.com/?p=188023)。
2. [使用您的用戶端](https://developer.mypurecloud.com/api/rest/authorization/use-client-credentials.html)生成權杖。
3. 如果要在 PureCloud 中自動預配組成員身份，則必須在 PureCloud 中創建與 Azure AD 中的組同名的[組](https://help.mypurecloud.com/?p=52397)。

## <a name="step-3-add-purecloud-by-genesys-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式庫按 Genesys 添加 PureCloud

從 Azure AD 應用程式庫添加"Genesys"的 PureCloud，以開始管理 Genesys 對 PureCloud 的預配。 如果您以前為 SSO 設置了 PureCloud，則可以使用相同的應用程式。 但是，建議在最初測試集成時創建單獨的應用。 在此處瞭解有關從庫中添加應用程式[的更多詳細資訊](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義誰將處於預配範圍 

Azure AD 預配服務允許您根據對應用程式的分配以及或基於使用者/組的屬性來限定誰將預配的範圍。 如果選擇根據分配將預配到應用的範圍，[則可以使用以下步驟將](../manage-apps/assign-user-or-group-access-portal.md)使用者和組分配給應用程式。 如果選擇僅根據使用者或組的屬性預配誰的範圍，則可以使用[此處](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的範圍篩選器。 

* 當通過 Genesys 將使用者和組分配給 PureCloud 時，您必須選擇**預設訪問**以外的角色。 具有預設存取權限角色的使用者從預配中排除，並且將在預配日誌中標記為無效許可權。 如果應用程式上唯一可用的角色是預設訪問角色，則可以[更新應用程式清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 從小開始。 在向所有人推出之前，先與一小部分使用者和組進行測試。 將預配範圍設置為分配的使用者和組時，可以通過為應用分配一個或兩個使用者或組來控制這種情況。 當作用域設置為所有使用者和組時，可以指定[基於屬性的範圍篩選器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-purecloud-by-genesys"></a>步驟 5。 通過 Genesys 配置自動使用者預配到 PureCloud 

本節將指導您完成將 Azure AD 預配服務配置為根據 Azure AD 中的使用者和/或組分配在 TestApp 中創建、更新和禁用使用者和/或組的步驟。

### <a name="to-configure-automatic-user-provisioning-for-purecloud-by-genesys-in-azure-ad"></a>要在 Azure AD 中按 Genesys 配置純雲的自動使用者預配：

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式**，然後選擇**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [PureCloud by Genesys]****。

    ![應用程式清單中的 PureCloud by Genesys 連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![預配選項卡](common/provisioning.png)

4. 將**預配模式**設置為 **"自動**"。

    ![預配選項卡](common/provisioning-automatic.png)

5. 在 **"管理員憑據"** 部分下，通過 Genesys API URL 和 Oauth 權杖分別在**租戶 URL**和**秘密權杖**欄位中輸入純雲。 API URL 的結構將為`{{API Url}}/api/v2/scim/v2`，使用[純雲開發人員中心的](https://developer.mypurecloud.com/api/rest/index.html)PureCloud 區域的 API URL。 按一下 **"測試連接**"以確保 Azure AD 可以按 Genesys 連接到 PureCloud。 如果連接失敗，請確保您的 PureCloud 通過 Genesys 帳戶具有管理員許可權，然後重試。

    ![佈建](./media/purecloud-by-genesys-provisioning-tutorial/provisioning.png)

6. 在 **"通知電子郵件"** 欄位中，輸入應接收預配錯誤通知的個人或組的電子郵件地址，然後選中"**在發生故障時發送電子郵件通知**"核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]****。

8. 在 **"映射"** 部分下，選擇**按 Genesy 將 Azure 活動目錄使用者同步到純雲**。

9. 在**屬性對應**部分中查看按 Genesys 從 Azure AD 同步到純雲的使用者屬性。 選擇為 **"匹配屬性"** 的屬性用於與 Genesys 在 PureCloud 中的使用者帳戶匹配以進行更新操作。 如果選擇更改[匹配的目標屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，則需要確保 Genesys 的 PureCloud 支援根據該屬性篩選使用者。 選取 [儲存]**** 按鈕以認可所有變更。

     |屬性|類型|
     |---|---|
     |userName|String|
     |作用中|Boolean|
     |displayName|String|
     |emails[type eq "work"].value|String|
     |title|String|
     |phoneNumbers[type eq "mobile"].value|String|
     |phoneNumbers[type eq "work"].value|String|
     |urn：ietf：參數：scim：架構：擴展：企業：2.0：使用者：部門|String|
     |urn：ietf：參數：scim：架構：擴展：企業：2.0：使用者：經理|參考資料|

10. 在 **"映射"** 部分下，選擇**按 Genesy 將 Azure 活動目錄組同步到純雲**。

11. 在**屬性對應**部分中查看按 Genesys 從 Azure AD 同步到純雲的組屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配"Genesys 在 PureCloud 中匹配組以進行更新操作"。 選取 [儲存]**** 按鈕以認可所有變更。 Genesys 的 PureCloud 不支援組創建或刪除，僅支援組更新。

      |屬性|類型|
      |---|---|
      |displayName|String|
      |externalId|String|
      |members|參考資料|

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 要啟用按 Genesys 為 PureCloud 啟用 Azure AD 預配服務，在 **"設置"** 部分將**預配狀態**更改為 **"打開**"。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 通過在 **"設置"** 部分中選擇"**範圍"** 中所需的值，定義您希望按 Genesys 預配到 PureCloud 的使用者和/或組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此操作將開始 **"設置"** 部分中 **"範圍**"中定義的所有使用者和組的初始同步週期。 初始週期執行的時間比後續週期長，只要 Azure AD 預配服務運行，則大約每 40 分鐘執行一次。 

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
配置預配後，請使用以下資源監視部署：

* 使用[預配日誌](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)確定已成功預配或未成功預配哪些使用者
* 檢查[進度列](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)以查看預配週期的狀態以及預配週期與完成有多近
* 如果預配配置似乎處於不正常狀態，則應用程式將進入隔離狀態。 在此處瞭解有關隔離狀態的更多[。](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)
