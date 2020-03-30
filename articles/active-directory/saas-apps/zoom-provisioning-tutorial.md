---
title: 教程：使用 Azure 活動目錄配置自動使用者預配的縮放 |微軟文檔
description: 瞭解如何從 Azure AD 自動預配和取消預配使用者帳戶以縮放。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: Zhchia
ms.openlocfilehash: 94c261da0c935cb7a41dde768069099b4e5ed251
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384070"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>教程：為自動使用者預配配置縮放

本教程介紹在縮放和 Azure 活動目錄 （Azure AD） 中配置自動使用者預配所需的步驟。 配置後，Azure AD 會自動預配和取消預配使用者和組，以便使用 Azure AD 預配服務[進行縮放](https://zoom.us/pricing/)。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支援功能
> [!div class="checklist"]
> * 在縮放中創建使用者
> * 當使用者不再需要存取權限時，請以"縮放"中刪除使用者
> * 使使用者屬性在 Azure AD 和 縮放之間保持同步
> * [單次登錄](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-tutorial)縮放（推薦）

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租戶](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)。
* Azure AD 中具有配置預配[許可權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的使用者帳戶（例如應用程式管理員、雲應用程式管理員、應用程式擁有者或全域管理員）。 
* [縮放租戶](https://zoom.us/pricing)。
* 具有管理員許可權的"縮放"中的使用者帳戶。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃預配部署
1. 瞭解[預配服務的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 確定誰將在[預配範圍內](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。
3. 確定要在[Azure AD 和 縮放 之間映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)哪些資料。 

## <a name="step-2-configure-zoom-to-support-provisioning-with-azure-ad"></a>步驟 2： 將縮放配置為支援使用 Azure AD 進行預配

1. 登錄到[您的縮放管理主控台](https://zoom.us/signin)。 導航到左側功能窗格中**開發人員的高級>縮放**。

    ![縮放集成](media/zoom-provisioning-tutorial/zoom01.png)

2. **導航到**頁面右上角的管理。 

    ![縮放安裝](media/zoom-provisioning-tutorial/zoom02.png)

3. 導航到創建的 Azure AD 應用。 
    
    ![縮放應用程式](media/zoom-provisioning-tutorial/zoom03.png)

4. 在左側功能窗格中選擇**應用憑據**。

    ![縮放應用程式](media/zoom-provisioning-tutorial/zoom04.png)

5. 複製並保存**JWT 權杖**。 此值將在 Azure 門戶中的 Zoom 應用程式的"預配"選項卡中的 **"機密權杖"** 欄位中輸入。 如果需要新的非過期權杖，則需要重新配置過期時間，以便自動生成新權杖。 

    ![縮放安裝](media/zoom-provisioning-tutorial/zoom05.png)

## <a name="step-3-add-zoom-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式庫添加縮放

從 Azure AD 應用程式庫添加縮放，開始管理對縮放的預配。 如果您以前為 SSO 設置了"縮放"，則可以使用相同的應用程式。 但是，建議在最初測試集成時創建單獨的應用。 在此處瞭解有關從庫中添加應用程式[的更多詳細資訊](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義誰將處於預配範圍 

Azure AD 預配服務允許您根據對應用程式的分配以及或基於使用者/組的屬性來限定誰將預配的範圍。 如果選擇根據分配將預配到應用的範圍，[則可以使用以下步驟將](../manage-apps/assign-user-or-group-access-portal.md)使用者和組分配給應用程式。 如果選擇僅根據使用者或組的屬性預配誰的範圍，則可以使用[此處](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的範圍篩選器。 

* 將使用者和組分配給"縮放"時，必須選擇**預設訪問**以外的角色。 具有預設存取權限角色的使用者從預配中排除，並且將在預配日誌中標記為無效許可權。 如果應用程式上唯一可用的角色是預設訪問角色，則可以[更新應用程式清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 從小開始。 在向所有人推出之前，先與一小部分使用者和組進行測試。 將預配範圍設置為分配的使用者和組時，可以通過為應用分配一個或兩個使用者或組來控制這種情況。 當作用域設置為所有使用者和組時，可以指定[基於屬性的範圍篩選器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-zoom"></a>步驟 5。 將自動使用者預配配置為縮放 

本節將指導您完成將 Azure AD 預配服務配置為根據 Azure AD 中的使用者和/或組分配在 TestApp 中創建、更新和禁用使用者和/或組的步驟。

### <a name="to-configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>要為 Azure AD 中縮放配置自動使用者預配：

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式**，然後選擇**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Zoom]****。

    ![應用程式清單中的 Zoom 連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![預配選項卡](common/provisioning.png)

4. 將**預配模式**設置為 **"自動**"。

    ![預配選項卡](common/provisioning-automatic.png)

5. 在 **"管理認證"** 部分下`https://api.zoom.us/scim`，在**租戶 URL**中輸入 。 輸入在**秘密權杖**中檢索到的**JWT 權杖**值。 按一下 **"測試連接**"以確保 Azure AD 可以連接到縮放。 如果連接失敗，請確保您的縮放帳戶具有管理員許可權，然後重試。

    ![縮放預配](./media/zoom-provisioning-tutorial/provisioning.png)

6. 在 **"通知電子郵件"** 欄位中，輸入應接收預配錯誤通知的個人或組的電子郵件地址，然後選中"**在發生故障時發送電子郵件通知**"核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]****。

8. 在 **"映射"** 部分下，選擇 **"同步 Azure 活動目錄使用者以縮放**"。

9. 查看從 Azure AD 同步到"**屬性對應**"部分中同步的使用者屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配"縮放"中的使用者帳戶以進行更新操作。 如果選擇更改[匹配的目標屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，則需要確保 Zoom API 支援基於該屬性篩選使用者。 選取 [儲存]**** 按鈕以認可所有變更。

   |屬性|類型|
   |---|---|
   |userName|String|
   |作用中|Boolean|
   |name.givenName|String|
   |name.familyName|String|
   |電子郵件[類型 eq"工作"]|String|
   |urn：ietf：參數：scim：架構：擴展：企業：2.0：使用者：部門|String|

10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 要啟用"縮放的 Azure AD 預配服務"，在 **"設置"** 部分將**預配狀態**更改為 **"打開**"。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 通過在 **"設置"** 部分中選擇"**範圍"** 中所需的值，定義要預配到縮放的使用者和/或組。

    ![佈建範圍](common/provisioning-scope.png)

13. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此操作將開始 **"設置"** 部分中 **"範圍**"中定義的所有使用者和組的初始同步週期。 初始週期執行的時間比後續週期長，只要 Azure AD 預配服務運行，則大約每 40 分鐘執行一次。 

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
配置預配後，請使用以下資源監視部署：

1. 使用[預配日誌](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)確定已成功預配或未成功預配哪些使用者
2. 檢查[進度列](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)以查看預配週期的狀態以及預配週期與完成有多近
3. 如果預配配置似乎處於不正常狀態，則應用程式將進入隔離狀態。 在此處瞭解有關隔離狀態的更多[。](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)  

## <a name="connector-limitations"></a>連接器限制
* 縮放目前最多隻能允許 9，999 個基本使用者。

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)
