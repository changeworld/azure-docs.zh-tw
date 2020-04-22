---
title: 教程:配置新的遺物(限量版本),以便使用 Azure 活動目錄自動預配使用者 |微軟文件
description: 瞭解如何自動預配和取消將使用者帳戶從 Azure AD 預配到新遺物(限量版本)。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 7fd0d976-4f70-4ce5-992e-3ea4ed8e5d60
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2020
ms.author: Zhchia
ms.openlocfilehash: 363b50a4835ead52b3a3a85978d4ea8210add7b2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727342"
---
# <a name="tutorial-configure-new-reliclimited-release-for-automatic-user-provisioning"></a>教學:自動使用者預配設定新遺物(有限版本)

本教程介紹需要在新遺物(有限版本)和 Azure 活動目錄 (Azure AD) 中執行以配置自動使用者預配的步驟。 配置後,Azure AD 使用 Azure AD 預配服務自動預配和取消向[新遺物](https://newrelic.com/)預配和取消預配。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支援功能
> [!div class="checklist"]
> * 建立新遺物中建立使用者(限量發行)
> * 刪除新遺物(有限版本)中的使用者,當他們不再需要存取權限時
> * 使用使用者屬性在 Azure AD 與新遺物(有限版本)之間保持同步
> * 在新遺物(限量發行)中預配組和組成員身份
> * [單次登錄](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial)新遺物(限量發行)(推薦)

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租戶](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中具有配置預配[許可權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的使用者帳戶(例如應用程式管理員、雲應用程式管理員、應用程式擁有者或全域管理員)。 
* 您希望使用者有權訪問的新遺物(有限版本)的一個或多個帳戶。 

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃預先配置
1. 瞭解[預先服務的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 確定誰將在[預先設定 。](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. 確定要在[Azure AD 和新遺物(有限版本)之間映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)哪些數據。 

## <a name="step-2-configure-new-reliclimited-release-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定新的遺物(有限版本)以支援使用 Azure AD 進行預配

與您的客戶代表合作,或在support.newrelic.com獲得支援,為您的組織配置 SCIM 和 SSO。 您需要提供您的客戶代表提供:

- 您的組織名稱
- 要與組織關聯的新遺物帳戶帳戶指示清單

通過此資訊,您的客戶代表在我們的新系統中為您創建組織記錄,並將您的帳戶關聯到組織。

您的帳號代表為您提供以下資訊,您需要為您的身份提供者設定新遺物 SCIM/SSO 應用程式:

- SCIM 終結點(租戶 URL)
- SCIM 無記名權杖(秘密令牌)

SCIM 承載令牌允許在新遺物中預配您的使用者,因此請保持該值的安全。 您的帳戶代表將以安全的方式將 SCIM 無記名令牌轉讓給您。

## <a name="step-3-add-new-reliclimited-release-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式庫新增新的遺物(限量版本)

從 Azure AD 應用程式庫添加新遺物(有限版本),以開始管理新遺物(有限版本)的預配。 如果您以前為 SSO 設置了新遺物(限量版本),則可以使用相同的應用程式。 但是,建議在最初測試集成時創建單獨的應用。 此處瞭解有關從函式庫中新增應用程式[的詳細資訊](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義誰將處於預配範圍 

Azure AD 預配服務允許您根據對應用程式的分配以及或基於使用者/組的屬性來限定誰將預配的範圍。 如果選擇根據分配將預配到應用的範圍,[則可以使用以下步驟將](../manage-apps/assign-user-or-group-access-portal.md)使用者和組分配給應用程式。 如果選擇僅根據使用者或組的屬性預配誰的範圍,則可以使用[此處](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的範圍篩選器。 

* 將使用者和組分配給「新遺物」(有限版本)時,必須選擇**默認訪問**以外的角色。 具有預設訪問許可權角色的使用者從預配中排除,並且將在預配日誌中標記為無效許可權。 如果應用程式上唯一可用的角色是默認訪問角色,則可以[更新應用程式清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 從小開始。 在向所有人推出之前,先與一小部分使用者和組進行測試。 將預配範圍設置為分配的使用者和組時,可以通過為應用分配一個或兩個使用者或組來控制這種情況。 當作用網域設定為所有使用者和群組時,可以指定[根據屬性的範圍篩選器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-new-reliclimited-release"></a>步驟 5。 設定自動使用者預配到新遺物(有限版本) 

本節將指導您完成將 Azure AD 預配服務配置為根據 Azure AD 中的使用者和/或組分配在 TestApp 中創建、更新和禁用使用者和/或組的步驟。

### <a name="to-configure-automatic-user-provisioning-for-new-reliclimited-release-in-azure-ad"></a>在 Azure AD 中設定新的遺物(有限版本)的自動使用者預配:

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選擇**企業應用程式**,然後選擇**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中,選擇 **「新遺物(限量發行)」。。**

    ![應用程式清單中的 New Relic 連結](common/all-applications.png)

3. 選擇 **「預配」** 選項卡。

    ![預配選項卡](common/provisioning.png)

4. 將**預配模式**設定為 **「自動**」。

    ![預配選項卡](common/provisioning-automatic.png)

5. 在 **"管理認證"** 部分`https://scim-provisioning.service.newrelic.com/scim/v2`下,輸入租戶網址。 輸入在**秘密權杖**中檢索到的 SCIM 身份驗證權杖值。 按下 **「測試連接**」以確保 Azure AD 可以連接到新遺物。 如果連接失敗,請確保您的新遺物帳戶具有管理員許可權,然後重試。

    ![佈建](./media/new-relic-limited-release-provisioning-tutorial/provisioning.png)

6. 在 **「通知電子郵件」** 欄位中,輸入應接收預配錯誤通知的個人或群組的電子郵件地址,然後選中「**在發生故障時發送電子郵件通知**」複選框。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]  。

8. 在 **「映射」** 部分下,選擇**將 Azure 活動目錄使用者同步到新遺物(限量版本)。**

9. 在**屬性映射**部分中查看從 Azure AD 同步到新遺物(有限版本)的用戶屬性。 選擇為 **「匹配**屬性」的屬性用於匹配更新操作的新 Relic(有限版本)中的使用者帳戶。 如果選擇更改[匹配的目標屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes),則需要確保新遺物(限釋放)API 支援基於該屬性篩選使用者。 選取 [儲存]**** 按鈕以認可所有變更。

   |屬性|類型|
   |---|---|
   |userName|String|
   |externalId|String|
   |作用中|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |名稱.格式化|String|
   |timezone|String|

10. 在 **「映射」** 部分下,選擇**將 Azure 活動目錄組同步到新遺物(限量版本)。**

11. 查看**屬性映射**部分中從 Azure AD 同步到新遺物(有限版本)的組屬性。 選擇為 **「匹配**屬性」的屬性用於匹配更新操作的新遺物(有限版本)中的組。 選取 [儲存]**** 按鈕以認可所有變更。

      |屬性|類型|
      |---|---|
      |displayName|String|
      |externalId|String|
      |members|參考|

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 要啟用新遺物的 Azure AD 預配服務(受限版本),在 **「設定」** 部分將**預配狀態**更改為 **「打開**」。。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 通過在 **「設定」** 部分中選擇「**範圍**」 中所需的值,定義要預配到新遺物(有限版本)的使用者和/或組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]****。

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
