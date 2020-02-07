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
ms.openlocfilehash: 5e954de97e6573e330c42915b44bbc0c40abc54d
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063238"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>教學課程：設定 ServiceNow 來自動布建使用者

本教學課程說明您需要在 ServiceNow 和 Azure Active Directory （Azure AD）中執行的步驟，以設定自動使用者布建。 設定之後，Azure AD 會使用 Azure AD 布建服務，將使用者和群組自動布建和取消布建至[ServiceNow](https://www.servicenow.com/) 。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支援的功能
> [!div class="checklist"]
> * 在 ServiceNow 中建立使用者
> * 當 ServiceNow 中的使用者不再需要存取權時，將其移除
> * 在 Azure AD 和 ServiceNow 之間保持使用者屬性同步處理
> * 在 ServiceNow 中布建群組和群組成員資格
> * ServiceNow 的[單一登入](servicenow-tutorial.md)（建議）

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租使用者](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中的使用者帳戶，具有設定布建的[許可權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)（例如，應用程式系統管理員、雲端應用程式系統管理員、應用程式擁有者或全域管理員）。 
* Calgary 或更高版本的[ServiceNow 實例](https://www.servicenow.com/)
* 赫爾辛基或更高版本的[ServiceNow Express 實例](https://www.servicenow.com/)
* ServiceNow 中具有系統管理員角色的使用者帳戶

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃您的布建部署
1. 深入瞭解布建[服務的運作方式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 判斷誰會在布建[範圍](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)內。
3. 判斷要[在 Azure AD 和 ServiceNow 之間對應](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的資料。 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定 ServiceNow 以支援以 Azure AD 布建

1. 識別您的 ServiceNow 實例名稱。 您可以在用來存取 ServiceNow 的 URL 中找到實例名稱。 在下列範例中，實例名稱是 dev35214。

![ServiceNow 實例](media/servicenow-provisioning-tutorial/servicenow_instance.png)

    
2. 取得 ServiceNow 中系統管理員的認證。 流覽至 ServiceNow 中的使用者設定檔，並確認使用者具有系統管理員角色。 

![ServiceNow 管理員角色](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式庫新增 ServiceNow

從 Azure AD 應用程式庫新增 ServiceNow，以開始管理對 ServiceNow 的布建。 如果您先前已設定適用于 SSO 的 ServiceNow，您可以使用相同的應用程式。 不過，建議您在一開始測試整合時，建立個別的應用程式。 在[這裡](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)深入瞭解如何從資源庫新增應用程式。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義將在布建範圍內的人員 

Azure AD 布建服務可讓您根據指派給應用程式的人員，或根據使用者/群組的屬性，來界定將布建的物件。 如果您選擇根據指派將布建到您的應用程式的範圍，您可以使用下列[步驟](../manage-apps/assign-user-or-group-access-portal.md)，將使用者和群組指派給應用程式。 如果您選擇將僅根據使用者或群組的屬性布建的使用者範圍，您可以使用範圍篩選器，如[這裡](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述。 

* 將使用者和群組指派給 ServiceNow 時，您必須選取 [**預設存取**] 以外的角色。 具有預設存取角色的使用者會從布建中排除，且在布建記錄中會被標示為不有效率。 如果應用程式上唯一可用的角色是預設存取角色，您可以[更新應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)來新增其他角色。 

* 從小規模開始。 在推出給所有人之前，先使用一小組的使用者和群組進行測試。 當布建的範圍設定為 [已指派的使用者和群組] 時，您可以藉由將一或兩個使用者或群組指派給應用程式來控制此項。 當 [範圍] 設定為 [所有使用者和群組] 時，您可以指定以[屬性為基礎的範圍篩選器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>步驟 5。 設定 ServiceNow 的自動使用者布建 

本節將引導您逐步設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和/或群組指派，在 TestApp 中建立、更新和停用使用者和/或群組。

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>若要在 Azure AD 中設定 ServiceNow 的自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [**企業應用程式**]，然後選取 [**所有應用程式**]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [ServiceNow]。

    ![應用程式清單中的 ServiceNow 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![布建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![布建索引標籤](common/provisioning-automatic.png)

5. 在 [**管理員認證**] 區段下，輸入您的 ServiceNow 管理員認證和使用者名稱。 按一下 [**測試連接**] 以確保 Azure AD 可以連接到 ServiceNow。 如果連線失敗，請確定您的 ServiceNow 帳戶具有系統管理員許可權，然後再試一次。

    ![佈建](./media/servicenow-provisioning-tutorial/provisioning.png)

6. 在 [**通知電子郵件**] 欄位中，輸入應收到布建錯誤通知之個人或群組的電子郵件地址，然後選取 [**發生失敗時傳送電子郵件通知**] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者到 ServiceNow**]。

9. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步至 ServiceNow 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 ServiceNow 中的使用者帳戶，以進行更新作業。 如果您選擇變更相符的[目標屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，則必須確定 ServiceNow API 支援根據該屬性來篩選使用者。 選取 [儲存] 按鈕以認可所有變更。

10. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 群組到 ServiceNow**]。

11. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步至 ServiceNow 的群組屬性。 選取為 [比對] 屬性**的屬性會**用來比對 ServiceNow 中的群組以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要啟用 ServiceNow 的 Azure AD 布建服務，請在 [**設定**] 區段中將 [布建**狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建至 ServiceNow 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會啟動 [**設定**] 區段的 [**範圍**] 中定義的所有使用者和群組的初始同步處理迴圈。 初始週期比後續迴圈花費更多時間執行，只要 Azure AD 布建服務正在執行，這大約每40分鐘就會發生一次。 

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
設定布建之後，請使用下列資源來監視您的部署：

1. 使用布建[記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)來判斷哪些使用者已成功布建或失敗
2. 檢查[進度](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)列以查看布建週期的狀態，以及關閉其完成的方式
3. 如果布建設定似乎處於狀況不良的狀態，應用程式將會進入隔離。 [在這裡](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)深入瞭解隔離狀態。  

## <a name="troubleshooting-tips"></a>疑難排解秘訣
* **InvalidLookupReference：** 在 ServiceNow 中布建某些屬性（例如部門和位置）時，這些值必須已經存在於 ServiceNow 的參考資料表中。 例如，您可能在 ServiceNow 的 [**插入資料表名稱**] 資料表中有兩個位置（西雅圖、洛杉磯）和三個部門（銷售、財務、行銷）。 如果您嘗試布建其部門為「銷售」且位置為「西雅圖」的使用者，他將會成功布建。 如果您嘗試布建具有部門「銷售」和位置「LA」的使用者，則不會布建使用者。 位置 LA 必須加入至 ServiceNow 中的參考資料表，或者必須更新 Azure AD 中的使用者屬性，以符合 ServiceNow 中的格式。 
* **EntryJoiningPropertyValueIsMissing：** 請檢查您的[屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)對應，以識別相符的屬性。 這個值必須存在於您嘗試布建的使用者或群組上。 
* 查看[SERVICENOW SOAP API](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html)以瞭解任何需求或限制（例如，指定使用者的國家/地區代碼的格式）
* 有些 ServiceNow 部署需要允許 Azure AD 布建服務的 IP 範圍。 Azure AD 布建服務的保留 IP 範圍可在[這裡](https://www.microsoft.com/download/details.aspx?id=56519)的 "AzureActiveDirectoryDomainServices" 底下找到。
* 目前不支援將使用者布建到 ServiceNow 政府雲端。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
