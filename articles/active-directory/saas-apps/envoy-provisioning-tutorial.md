---
title: 教程:使用 Azure 活動目錄配置自動使用者預配特使 |微軟文件
description: 瞭解如何從 Azure AD 自動預配和取消預配使用者帳戶。
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
ms.openlocfilehash: 68e17ba1dd5981e565e56d6c8137f77d33ad755b
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393503"
---
# <a name="tutorial-configure-envoy-for-automatic-user-provisioning"></a>教學:自動使用者預先設定特使

本教程介紹需要在特使和 Azure 活動目錄 (Azure AD) 中執行以配置自動使用者預配的步驟。 設定後,Azure AD 會自動使用 Azure AD 預先用服務預配和取消預配使用者和群組[以特使](https://envoy.com/pricing/)。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支援功能
> [!div class="checklist"]
> * 在特使中建立使用者
> * 當不再需要存取權限時,刪除特使中的使用者
> * 使用使用者屬性在 Azure AD 和特使之間保持同步
> * 特使中的提供群組與群組成員
> * [向特使單一簽字](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-tutorial)(建議)

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租戶](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中具有配置預配[許可權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的使用者帳戶(例如應用程式管理員、雲應用程式管理員、應用程式擁有者或全域管理員)。 
* [特使租戶](https://envoy.com/pricing/)。
* 具有管理員許可權的特使中的使用者帳戶。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃預先配置
1. 瞭解[預先服務的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 確定誰將在[預先設定 。](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. 確定要在[Azure AD 和特使之間映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)哪些數據。 

## <a name="step-2-configure-envoy-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定特使以支援 Azure AD 的預先

1. 登入您的[特使管理主控台](https://dashboard.envoy.com/login)。 點選**整合**。

    ![特使整合](media/envoy-provisioning-tutorial/envoy01.png)

2. 點選「**安裝**」 以進行**Microsoft Azure SCIM 整合**。

    ![特使安裝](media/envoy-provisioning-tutorial/envoy02.png)

3. 按下「**儲存**」 以**同步所有使用者**。 

    ![特使儲存](media/envoy-provisioning-tutorial/envoy03.png)

4. 複製**非統組織的標籤**。 此值將在 Azure 門戶中的 Envoy 應用程式的預配選項卡中的 **「機密權杖」** 欄位中輸入。
    
    ![烏阿特特使](media/envoy-provisioning-tutorial/envoy04.png)

## <a name="step-3-add-envoy-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式庫加入特使

從 Azure AD 應用程式庫添加特使,開始管理對特使的預配。 如果您以前為 SSO 設置了特使,則可以使用相同的應用程式。 但是,建議在最初測試集成時創建單獨的應用。 此處瞭解有關從函式庫中新增應用程式[的詳細資訊](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義誰將處於預配範圍 

Azure AD 預配服務允許您根據對應用程式的分配以及或基於使用者/組的屬性來限定誰將預配的範圍。 如果選擇根據分配將預配到應用的範圍,[則可以使用以下步驟將](../manage-apps/assign-user-or-group-access-portal.md)使用者和組分配給應用程式。 如果選擇僅根據使用者或組的屬性預配誰的範圍,則可以使用[此處](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的範圍篩選器。 

* 將使用者和組分配給特使時,必須選擇**默認訪問**以外的角色。 具有預設訪問許可權角色的使用者從預配中排除,並且將在預配日誌中標記為無效許可權。 如果應用程式上唯一可用的角色是默認訪問角色,則可以[更新應用程式清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 從小開始。 在向所有人推出之前,先與一小部分使用者和組進行測試。 將預配範圍設置為分配的使用者和組時,可以通過為應用分配一個或兩個使用者或組來控制這種情況。 當作用網域設定為所有使用者和群組時,可以指定[根據屬性的範圍篩選器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-envoy"></a>步驟 5。 將自動使用者預先設定為特使 

本節將指導您完成將 Azure AD 預配服務配置為根據 Azure AD 中的使用者和/或組分配在 TestApp 中創建、更新和禁用使用者和/或組的步驟。

### <a name="to-configure-automatic-user-provisioning-for-envoy-in-azure-ad"></a>在 Azure AD 中為特使設定自動使用者預配:

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選擇**企業應用程式**,然後選擇**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Envoy]****。

    ![應用程式清單中的 Envoy 連結](common/all-applications.png)

3. 選擇 **「預配」** 選項卡。

    ![預配選項卡](common/provisioning.png)

4. 將**預配模式**設定為 **「自動**」。

    ![預配選項卡](common/provisioning-automatic.png)

5. 在 **"管理認證為「**`https://app.envoy.com/scim/v2`部份下 」,在**租戶網址**中輸入 。 輸入在**秘密權杖**中檢索到的**OAUTH BEARER TOKEN**值。 按下 **「測試連線**」以確保 Azure AD 可以連接到特使。 如果連接失敗,請確保您的特使帳戶具有管理員許可權,然後重試。

   ![佈建](./media/envoy-tutorial/provisioning.png)

6. 在 **「通知電子郵件」** 欄位中,輸入應接收預配錯誤通知的個人或群組的電子郵件地址,然後選中「**在發生故障時發送電子郵件通知**」複選框。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]  。

8. 在 **'映射**' 部份下,選擇**將 Azure 的目錄使用者同步到特使**。

9. 在**屬性映射**部分中查看從 Azure AD 同步到特使的使用者屬性。 選擇為 **「匹配屬性」** 的屬性用於匹配特使中的使用者帳戶以進行更新操作。 如果選擇更改[匹配的目標屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes),則需要確保 Envoy API 支援基於該屬性篩選使用者。 選取 [儲存]**** 按鈕以認可所有變更。

   |屬性|類型|
   |---|---|
   |userName|String|
   |externalId|String|
   |displayName|String|
   |title|String|
   |emails[type eq "work"].value|String|
   |preferredLanguage|String|
   |department|String|
   |位址[類型 eq"工作"\國家/地區|String|
   |位址_類型 eq"工作"\局部性|String|
   |位址_類型 eq"工作"\區域|String|
   |addresses[type eq "work"].postalCode|String|
   |位址[類型 eq"工作"]格式化|String|
   |addresses[type eq "work"].streetAddress|String|
   |name.givenName|String|
   |name.familyName|String|
   |名稱.格式化|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "work"].value|String|
   |地區設定|String|

10. 在 **'映射**' 部份下,選擇**將 Azure 的目錄群組同步到特使**。

11. 在**屬性映射**部分中查看從 Azure AD 同步到特使的組屬性。 選擇為 **「匹配屬性」** 的屬性用於匹配「特使」 中的組以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

      |屬性|類型|
      |---|---|
      |displayName|String|
      |externalId|String|
      |members|參考|

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 要啟用特使的 Azure AD 預配服務,在 **「設定」** 部分將**預先狀態**更改為 **「打開**」 。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 通過在 **「設定」** 部分中選擇「**範圍」** 中所需的值,定義要預配給特使的使用者和 /或組。

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