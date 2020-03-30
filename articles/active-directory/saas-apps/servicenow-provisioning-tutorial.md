---
title: 教學課程︰以 Azure Active Directory 設定 ServiceNow 來自動佈建使用者 | Microsoft Docs
description: 了解如何將使用者帳戶從 Azure AD 自動佈建和取消佈建至 ServiceNow。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: 9e93d4b3f1880f2ac56a32a7b85aa6801fb7c14e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205093"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>教程：立即佈建服務以進行自動使用者預配

本教程介紹在 ServiceNow 和 Azure 活動目錄 （Azure AD） 中配置自動使用者預配所需的步驟。 配置後，Azure AD 使用 Azure AD 預配服務自動預配和取消使用者和組服務[Now。](https://www.servicenow.com/) 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支援功能
> [!div class="checklist"]
> * 在 ServiceNow 中創建使用者
> * 當不再需要存取權限時，刪除 ServiceNow 中的使用者
> * 使使用者屬性在 Azure AD 和服務現在之間保持同步
> * 服務現在中的預配組和組成員身份
> * [單次登錄](servicenow-tutorial.md)服務現在（推薦）

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租戶](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中具有配置預配[許可權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的使用者帳戶（例如應用程式管理員、雲應用程式管理員、應用程式擁有者或全域管理員）。 
* 卡爾加里或更高一些的[ServiceNow 實例](https://www.servicenow.com/)
* 赫爾辛基或更高地區的[ServiceNow Express 實例](https://www.servicenow.com/)
* ServiceNow 中的使用者帳戶，具有管理員角色

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃預配部署
1. 瞭解[預配服務的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 確定誰將在[預配範圍內](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。
3. 確定要在[Azure AD 和服務現在 之間映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)哪些資料。 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>步驟 2： 將服務現在配置為支援使用 Azure AD 進行預配

1. 標識服務 Now 實例名稱。 您可以在用於訪問 ServiceNow 的 URL 中找到實例名稱。 在下面的示例中，實例名稱是 dev35214。

![服務現在實例](media/servicenow-provisioning-tutorial/servicenow_instance.png)

    
2. 獲取 ServiceNow 中的管理員的憑據。 導航到 ServiceNow 中的使用者設定檔，並驗證使用者是否具有管理員角色。 

![服務現在管理員角色](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式庫添加服務現在

從 Azure AD 應用程式庫添加 ServiceNow，開始管理對 ServiceNow 的預配。 如果您以前為 SSO 設置了 ServiceNow，則可以使用相同的應用程式。 但是，建議在最初測試集成時創建單獨的應用。 在此處瞭解有關從庫中添加應用程式[的更多詳細資訊](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義誰將處於預配範圍 

Azure AD 預配服務允許您根據對應用程式的分配以及或基於使用者/組的屬性來限定誰將預配的範圍。 如果選擇根據分配將預配到應用的範圍，[則可以使用以下步驟將](../manage-apps/assign-user-or-group-access-portal.md)使用者和組分配給應用程式。 如果選擇僅根據使用者或組的屬性預配誰的範圍，則可以使用[此處](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的範圍篩選器。 

* 將使用者和組分配給 ServiceNow 時，必須選擇**預設訪問**以外的角色。 具有預設存取權限角色的使用者從預配中排除，並且將在預配日誌中標記為無效許可權。 如果應用程式上唯一可用的角色是預設訪問角色，則可以[更新應用程式清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 從小開始。 在向所有人推出之前，先與一小部分使用者和組進行測試。 將預配範圍設置為分配的使用者和組時，可以通過為應用分配一個或兩個使用者或組來控制這種情況。 當作用域設置為所有使用者和組時，可以指定[基於屬性的範圍篩選器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>步驟 5。 配置自動使用者預配到服務現在 

本節將指導您完成將 Azure AD 預配服務配置為根據 Azure AD 中的使用者和/或組分配在 TestApp 中創建、更新和禁用使用者和/或組的步驟。

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>要在 Azure AD 中佈建服務現在的自動使用者預配：

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式**，然後選擇**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [ServiceNow]****。

    ![應用程式清單中的 ServiceNow 連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![預配選項卡](common/provisioning.png)

4. 將**預配模式**設置為 **"自動**"。

    ![預配選項卡](common/provisioning-automatic.png)

5. 在 **"管理員憑據"** 部分下，輸入您的 ServiceNow 管理員憑據和使用者名。 按一下 **"測試連接**"以確保 Azure AD 可以連接到服務現在。 如果連接失敗，請確保您的服務 Now 帳戶具有管理員許可權，然後重試。

    ![佈建](./media/servicenow-provisioning-tutorial/provisioning.png)

6. 在 **"通知電子郵件"** 欄位中，輸入應接收預配錯誤通知的個人或組的電子郵件地址，然後選中"**在發生故障時發送電子郵件通知**"核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]****。

8. 在 **"映射"** 部分下，選擇**將 Azure 活動目錄使用者同步到服務現在**。

9. 在**屬性對應**部分中查看從 Azure AD 同步到"服務現在"的使用者屬性。 選取為 [比對]**** 屬性的屬性會用來比對 ServiceNow 中的使用者帳戶，以進行更新作業。 如果選擇更改[匹配的目標屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，則需要確保 ServiceNow API 支援基於該屬性篩選使用者。 選取 [儲存]**** 按鈕以認可所有變更。

10. 在 **"映射**"部分下，選擇**將 Azure 活動目錄組同步到服務現在**。

11. 在**屬性對應**部分中查看從 Azure AD 同步到"服務現在"的組屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 ServiceNow 中的組以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 要啟用"立即服務"的 Azure AD 預配服務，在 **"設置"** 部分將**預配狀態**更改為 **"打開**"。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 通過在 **"設置"** 部分中選擇"**範圍"** 中所需的值，定義要預配到 ServiceNow 的使用者和/或組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此操作將開始 **"設置"** 部分中 **"範圍**"中定義的所有使用者和組的初始同步週期。 初始週期執行的時間比後續週期長，只要 Azure AD 預配服務運行，則大約每 40 分鐘執行一次。 

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
配置預配後，請使用以下資源監視部署：

1. 使用[預配日誌](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)確定已成功預配或未成功預配哪些使用者
2. 檢查[進度列](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)以查看預配週期的狀態以及預配週期與完成有多近
3. 如果預配配置似乎處於不正常狀態，則應用程式將進入隔離狀態。 在此處瞭解有關隔離狀態的更多[。](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)  

## <a name="troubleshooting-tips"></a>疑難排解秘訣
* **無效查找參考：** 在 ServiceNow 中預配某些屬性（如"部門"和"位置"）時，這些值必須已存在於 ServiceNow 中的引用表中。 例如，在 ServiceNow 中的**插入表名稱**表中，您可能有兩個位置（西雅圖、洛杉磯）和三個部門（銷售、財務、市場行銷）。 如果您嘗試預配其部門為"銷售"且位置為"西雅圖"的使用者，則已成功預配使用者。 如果您嘗試向使用者預配部門"銷售"和位置"LA"，則不會預配該使用者。 必須將位置 LA 添加到 ServiceNow 中的引用表，或者必須更新 Azure AD 中的使用者屬性以匹配 ServiceNow 中的格式。 
* **條目聯接屬性值缺失：** 查看[屬性對應](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)以標識匹配屬性。 此值必須存在於您嘗試預配的使用者或組中。 
* 查看[ServiceNow SOAP API](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html)以瞭解任何要求或限制（例如，為使用者指定國家/地區代碼的格式）
* 某些 ServiceNow 部署需要允許 Azure AD 預配服務的 IP 範圍。 Azure AD 預配服務的預留 IP 範圍可[在此處](https://www.microsoft.com/download/details.aspx?id=56519)找到"AzureActiveDirectoryDomain 服務"。
* 預設情況下，預配要求傳送到 HTTPs：//[您的實例名稱].服務-now.com/[表名]。 如果需要自訂租戶 URL，可以在實例名稱欄位中提供整個 URL。

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
