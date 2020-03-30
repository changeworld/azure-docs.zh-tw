---
title: 教程：使用 Azure 活動目錄配置 Zapier 以自動預配使用者 |微軟文檔
description: 瞭解如何從 Azure AD 自動預配和取消預配使用者帳戶到 Zapier。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 66e224f9-2311-4564-bb84-99fce59a398f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: Zhchia
ms.openlocfilehash: 4091e4fd544dbc6450bc14bd0e0731c4d3024592
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76992153"
---
# <a name="tutorial-configure-zapier-for-automatic-user-provisioning"></a>教程：為自動使用者預配配置 Zapier

本教程介紹在 Zapier 和 Azure 活動目錄 （Azure AD） 中配置自動使用者預配所需的步驟。 配置後，Azure AD 使用 Azure AD 預配服務自動預配和取消向[Zapier](https://zapier.com/pricing)預配使用者和組。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支援功能
> [!div class="checklist"]
> * 在紮皮爾創建使用者
> * 在 Zapier 中不再需要存取權限時刪除使用者
> * 使使用者屬性在 Azure AD 和 Zapier 之間保持同步
> * 紮皮爾的預配組和組成員身份
> * 紮皮爾的單次登錄（推薦）

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租戶](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中具有配置預配[許可權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的使用者帳戶（例如應用程式管理員、雲應用程式管理員、應用程式擁有者或全域管理員）。 
* Zapier 中具有管理員許可權的使用者帳戶。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃預配部署
1. 瞭解[預配服務的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 確定誰將在[預配範圍內](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。
3. 確定要在[Azure AD 和 Zapier 之間映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)哪些資料。 

## <a name="step-2-configure-zapier-to-support-provisioning-with-azure-ad"></a>步驟 2： 配置 Zapier 以支援使用 Azure AD 進行預配

1. 登錄到您的[紮皮爾管理主控台](https://zapier.com/app/login/)。 導航到租戶 ID 下的 **"設置**"。

    ![紮皮爾管理主控台](media/zapier-provisioning-tutorial/admin.png)

2. 在 **"公司設置"下**，選擇**使用者預配**。

    ![紮皮爾添加SCIM](media/zapier-provisioning-tutorial/user.png)

3. 複製**SCIM 基本 URL**和**SCIM 承載權杖**。 這些值將分別在 Azure 門戶中的 Zapier 應用程式的預配選項卡中輸入租戶 URL 和秘密權杖欄位。

    ![紮皮爾創建權杖](media/zapier-provisioning-tutorial/token.png)

## <a name="step-3-add-zapier-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式庫添加紮皮爾

從 Azure AD 應用程式庫添加 Zapier，開始管理對 Zapier 的預配。 如果您以前為 SSO 設置了 Zapier，則可以使用相同的應用程式。 但是，建議在最初測試集成時創建單獨的應用。 在此處瞭解有關從庫中添加應用程式[的更多詳細資訊](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義誰將處於預配範圍 

Azure AD 預配服務允許您根據對應用程式的分配以及或基於使用者/組的屬性來限定誰將預配的範圍。 如果選擇根據分配將預配到應用的範圍，[則可以使用以下步驟將](../manage-apps/assign-user-or-group-access-portal.md)使用者和組分配給應用程式。 如果選擇僅根據使用者或組的屬性預配誰的範圍，則可以使用[此處](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的範圍篩選器。 

* 將使用者和組分配給 Zapier 時，必須選擇**預設訪問**以外的角色。 具有預設存取權限角色的使用者從預配中排除，並且將在預配日誌中標記為無效許可權。 如果應用程式上唯一可用的角色是預設訪問角色，則可以[更新應用程式清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 從小開始。 在向所有人推出之前，先與一小部分使用者和組進行測試。 將預配範圍設置為分配的使用者和組時，可以通過為應用分配一個或兩個使用者或組來控制這種情況。 當作用域設置為所有使用者和組時，可以指定[基於屬性的範圍篩選器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-zapier"></a>步驟 5。 配置自動使用者預配到紮皮爾 

本節將指導您完成將 Azure AD 預配服務配置為根據 Azure AD 中的使用者和/或組分配在 TestApp 中創建、更新和禁用使用者和/或組的步驟。

### <a name="to-configure-automatic-user-provisioning-for-zapier-in-azure-ad"></a>要在 Azure AD 中配置 Zapier 的自動使用者預配：

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式**，然後選擇**所有應用程式**。

   ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選擇**紮皮爾**。

   !["應用程式"清單中的紮皮爾連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

   ![預配選項卡](common/provisioning.png)

4. 將**預配模式**設置為 **"自動**"。

   ![預配選項卡](common/provisioning-automatic.png)

5. 在 **"管理員憑據"** 部分下，輸入您的 Zapier**租戶 URL**和**秘密權杖**。 按一下 **"測試連接**"以確保 Azure AD 可以連接到紮皮爾。 如果連接失敗，請確保您的 Zapier 帳戶具有管理員許可權，然後重試。

   ![佈建](./media/zapier-provisioning-tutorial/provisioning.png)

6. 在 **"通知電子郵件"** 欄位中，輸入應接收預配錯誤通知的個人或組的電子郵件地址，然後選中"**在發生故障時發送電子郵件通知**"核取方塊。

   ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]****。

8. 在 **"映射"** 部分下，選擇**將 Azure 活動目錄使用者同步到紮皮爾**。

9. 在**屬性對應**部分中查看從 Azure AD 同步到 Zapier 的使用者屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 Zapier 中的使用者帳戶以進行更新操作。 如果選擇更改[匹配的目標屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，則需要確保 Zapier API 支援基於該屬性篩選使用者。 選取 [儲存]**** 按鈕以認可所有變更。

   |變數|類型|
   |---|---|
   |userName|String|
   |作用中|Boolean|
   |externalId|String|
   |name.givenName|String|
   |name.familyName|String|
   |emails[type eq "work"].value|String|

10. 在 **"映射"** 部分下，選擇**將 Azure 活動目錄組同步到紮皮爾**。

11. 在**屬性對應**部分中查看從 Azure AD 同步到 Zapier 的組屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 Zapier 中的組以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    |變數|類型|
    |---|---|
    |displayName|String|
    |members|參考資料|

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 要為 Zapier 啟用 Azure AD 預配服務，在 **"設置"** 部分將**預配狀態**更改為 **"打開**"。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 通過在 **"設置"** 部分中選擇"**範圍"** 中所需的值，定義要預配到 Zapier 的使用者和/或組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此操作將開始 **"設置"** 部分中 **"範圍**"中定義的所有使用者和組的初始同步週期。 初始週期執行的時間比後續週期長，只要 Azure AD 預配服務運行，則大約每 40 分鐘執行一次。 

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
配置預配後，請使用以下資源監視部署：

- 使用[預配日誌](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)確定已成功預配或未成功預配哪些使用者
- 檢查[進度列](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)以查看預配週期的狀態以及預配週期與完成有多近
- 如果預配配置似乎處於不正常狀態，則應用程式將進入隔離狀態。 在此處瞭解有關隔離狀態的更多[。](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)
