---
title: 教程:使用 Azure 活動目錄配置朱諾旅程,以便自動預配使用者 |微軟文件
description: 瞭解如何自動預配和取消預配使用者帳戶從 Azure AD 到朱諾旅程。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 79813b19-c96e-4459-a4e5-636a6b6f5041
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2020
ms.author: Zhchia
ms.openlocfilehash: 08de07a52d1e43dea91e6684d33027d8bcad61fc
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641845"
---
# <a name="tutorial-configure-juno-journey-for-automatic-user-provisioning"></a>教程:為自動用戶預配配置朱諾旅程

本教程介紹在 Juno 旅程和 Azure 活動目錄 (Azure AD) 中設定自動使用者預配所需的步驟。 配置後,Azure AD 使用 Azure AD 預配服務自動預配和取消向[Juno 旅程](https://www.junojourney.com/)預配和取消預配使用者和組。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支援功能
> [!div class="checklist"]
> * 在朱諾旅程中建立使用者
> * 在朱諾旅程中刪除使用者,當他們不再需要訪問許可權時
> * 使用使用者屬性在 Azure AD 和 Juno 旅程之間保持同步
> * [朱諾之旅的單一登錄](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial)(推薦)

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租戶](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中具有配置預配[許可權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的使用者帳戶(例如應用程式管理員、雲應用程式管理員、應用程式擁有者或全域管理員)。 
*  [朱諾之旅租戶](https://www.junojourney.com/getstartedwithjuno)。
*  具有管理員許可權的 Juno 旅程中的使用者帳戶。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃預先配置
1. 瞭解[預先服務的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 確定誰將在[預先設定 。](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. 確定[要在 Azure AD 和 朱諾行程之間映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)哪些數據。 

## <a name="step-2-configure-juno-journey-to-support-provisioning-with-azure-ad"></a>步驟 2： 將朱諾旅程設定為支援使用 Azure AD 進行預配

1. 有關**秘密權和****租戶網址,** 請聯繫support@the-juno.comJuno 旅程支援團隊。 此值將分別在 Azure 門戶中的 Juno 旅程應用程式的"預配"選項卡中輸入 **。"** **Tenant URL** 

## <a name="step-3-add-juno-journey-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式庫新增朱諾旅程

從 Azure AD 應用程式庫添加朱諾旅程,開始管理朱諾旅程的預配。 如果您以前為 SSO 設置了 Juno 旅程,則可以使用相同的應用程式。 但是,建議在最初測試集成時創建單獨的應用。 此處瞭解有關從函式庫中新增應用程式[的詳細資訊](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義誰將處於預配範圍 

Azure AD 預配服務允許您根據對應用程式的分配以及或基於使用者/組的屬性來限定誰將預配的範圍。 如果選擇根據分配將預配到應用的範圍,[則可以使用以下步驟將](../manage-apps/assign-user-or-group-access-portal.md)使用者和組分配給應用程式。 如果選擇僅根據使用者或組的屬性預配誰的範圍,則可以使用[此處](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的範圍篩選器。 

* 將使用者和組分配給 Juno 旅程時,必須選擇**默認訪問**以外的角色。 具有預設訪問許可權角色的使用者從預配中排除,並且將在預配日誌中標記為無效許可權。 如果應用程式上唯一可用的角色是默認訪問角色,則可以[更新應用程式清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 從小開始。 在向所有人推出之前,先與一小部分使用者和組進行測試。 將預配範圍設置為分配的使用者和組時,可以通過為應用分配一個或兩個使用者或組來控制這種情況。 當作用網域設定為所有使用者和群組時,可以指定[根據屬性的範圍篩選器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-juno-journey"></a>步驟 5。 設定到朱諾旅程的自動使用者預配 

本節將指導您完成將 Azure AD 預配服務配置為根據 Azure AD 中的使用者和/或組分配在 TestApp 中創建、更新和禁用使用者和/或組的步驟。

### <a name="to-configure-automatic-user-provisioning-for-juno-journey-in-azure-ad"></a>在 Azure AD 中為朱諾旅程設定自動使用者預配:

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選擇**企業應用程式**,然後選擇**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Juno Journey]****。

    ![應用程式「清單中的朱諾旅程連結](common/all-applications.png)

3. 選擇 **「預配」** 選項卡。

    ![預配選項卡](common/provisioning.png)

4. 將**預配模式**設定為 **「自動**」。

    ![預配選項卡](common/provisioning-automatic.png)

5. 在 **"管理認證"** 部分下,輸入在**租戶網址**中檢索的租戶網址值。 輸入在**秘密權杖**中檢索到的秘密權杖值。 按下 **「測試連接**」以確保 Azure AD 可以連接到朱諾旅程。 如果連接失敗,請確保您的 Juno 旅程帳戶具有管理員許可權,然後重試。

    ![佈建](./media/juno-journey-provisioning-tutorial/provisioning.png)

6. 在 **「通知電子郵件」** 欄位中,輸入應接收預配錯誤通知的個人或群組的電子郵件地址,然後選中「**在發生故障時發送電子郵件通知**」複選框。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]  。

8. 在**映射「 映射」** 部份下,選擇**將 Azure 活動目錄使用者同步到朱諾旅程**。

9. 在**屬性映射**部分中查看從 Azure AD 同步到朱諾旅程的用戶屬性。 選擇為 **「匹配屬性」** 的屬性用於匹配 Juno 旅程中的使用者帳戶以進行更新操作。 如果選擇更改[匹配的目標屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes),則需要確保 Juno 旅程 API 支援基於該屬性篩選使用者。 選取 [儲存]**** 按鈕以認可所有變更。

   |變數|類型|
   |---|---|
   |userName|String|
   |externalId|String|
   |displayName|String|
   |title|String|
   |作用中|Boolean|
   |preferredLanguage|String|
   |emails[type eq "work"].value|String|
   |位址[類型 eq"工作"\國家/地區|String|
   |位址_類型 eq"工作"\區域|String|
   |位址_類型 eq"工作"\局部性|String|
   |addresses[type eq "work"].postalCode|String|
   |位址[類型 eq"工作"]格式化|String|
   |addresses[type eq "work"].streetAddress|String|
   |name.givenName|String|
   |name.familyName|String|
   |名稱.中間名稱|String|
   |名稱.格式化|String|
   |phoneNumbers[type eq "fax"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "work"].value|String|
   |urn:ietf:參數:scim:架構:擴展:企業:2.0:使用者:部門|String|
   |urn:ietf:參數:scim:架構:擴展:企業:2.0:使用者:員工編號|String|
   |urn:ietf:參數:scim:架構:擴展:企業:2.0:使用者:成本中心|String|
   urn:ietf:參數:scim:架構:擴展:企業:2.0:使用者:部門|String|
   urn:ietf:參數:scim:架構:擴展:企業:2.0:用戶:經理|String|
   urn:ietf:參數:scim:架構:擴展:企業:2.0:使用者:組織|String|


10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 要啟用朱諾行程的 Azure AD 預配服務,在 **「設置」** 部分將**預配狀態**更改為 **「打開**」。。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 通過在 **「設置」** 部分中選擇「**範圍」** 中所需的值,定義要預配到 Juno 旅程的使用者和 /或組。

    ![佈建範圍](common/provisioning-scope.png)

13. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此操作將開始 **「設置」** 部分中 **「範圍**」中定義的所有使用者和組的初始同步週期。 初始週期執行的時間比後續週期長,只要 Azure AD 預配服務運行,則大約每 40 分鐘執行一次。 

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
設定預先接後,請使用以下資源監視部署:

* 使用[預先設定紀錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)決定已成功預先設定或未成功預先配哪些使用者
* 檢查[進度列](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)以檢視預先週期的狀態以及預配週期與完成有多近
* 如果預配配置似乎處於不正常狀態,則應用程式將進入隔離狀態。 此處瞭解有關隔離狀態的更多[。](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)
