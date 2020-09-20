---
title: 教學課程：使用 Azure Active Directory 設定 Cofense 來自動布建使用者 |Microsoft Docs
description: 瞭解如何從 Azure AD 將使用者帳戶自動布建和取消布建至 Cofense。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 84fe20ef-0de0-4f7c-9b42-6385f3d834db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2020
ms.author: Zhchia
ms.openlocfilehash: d1ef09e34e44a8a4f39fb5e9c140f138d3da8d86
ms.sourcegitcommit: 4ce82b6df65ebd81157b6168d3aa4e7323355022
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2020
ms.locfileid: "90761473"
---
# <a name="tutorial-configure-cofense-for-automatic-user-provisioning"></a>教學課程：設定 Cofense 來自動布建使用者

本教學課程說明在 Cofense 和 Azure Active Directory (Azure AD) 設定自動使用者布建時所需執行的步驟。 當設定時，Azure AD 會使用 Azure AD 布建服務，自動將使用者布建和取消布建至 [Cofense](https://cofense.com/) 。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支援的功能
> [!div class="checklist"]
> * 在 Cofense 中建立使用者
> * 當使用者不再需要存取權時，請移除 Cofense 中的使用者
> * Azure AD 與 Cofense 之間保持使用者屬性同步

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租用戶](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中具有設定佈建[權限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的使用者帳戶 (例如，應用程式管理員、雲端應用程式管理員、應用程式擁有者或全域管理員)。 
* Cofense PhishMe 中的標準操作員帳戶。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃佈建部署
1. 了解[佈建服務的運作方式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) \(部分機器翻譯\)。
2. 判斷誰會在[佈建範圍](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)內。
3. 判斷要 [在 Azure AD 與 Cofense 之間對應](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的資料。 

## <a name="step-2-configure-cofense-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定 Cofense 以支援 Azure AD 的布建

1. 登入 Cofense PhishMe。 流覽至收件者 > 收件者同步。 
2. 接受條款及條件，然後按一下 [開始]。

    ![Recepient 同步 >tnc](media/cofense-provisioning-tutorial/recipient-sync-toc.png)

3. 複製 [URL] 和 [權杖] 欄位中的值。

    ![Recepient 同步處理](media/cofense-provisioning-tutorial/recipient-sync-getting-started.png)


## <a name="step-3-add-cofense-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式資源庫新增 Cofense

從 Azure AD 應用程式資源庫新增 Cofense，以開始管理布建至 Cofense。 如果您先前已設定 SSO 的 Cofense，您可以使用相同的應用程式。 不過，建議您在一開始測試整合時，建立個別的應用程式。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)深入了解從資源庫新增應用程式。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義將在佈建範圍內的人員 

Azure AD 佈建服務可供根據對應用程式的指派，或根據使用者/群組的屬性，界定將要佈建的人員。 如果您選擇根據指派來界定將佈建至應用程式的人員，您可以使用下列[步驟](../manage-apps/assign-user-or-group-access-portal.md)將使用者和群組指派給應用程式。 如果您選擇僅根據使用者或群組的屬性來界定將要佈建的人員，可以使用如[這裡](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的範圍篩選條件。 

* 將使用者和群組指派給 Cofense 時，您必須選取 **預設存取**以外的角色。 具有預設存取角色的使用者會從佈建中排除，而且會在佈建記錄中被標示為沒有效率。 如果應用程式上唯一可用的角色是 [預設存取] 角色，您可以[更新應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) \(部分機器翻譯\) 以新增其他角色。 

* 從小規模開始。 在推出給所有人之前，先使用一小部分的使用者和群組進行測試。 當佈建範圍設為已指派的使用者和群組時，您可將一或兩個使用者或群組指派給應用程式來控制這點。 當範圍設為所有使用者和群組時，您可指定[以屬性為基礎的範圍篩選條件](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-cofense"></a>步驟 5。 設定自動使用者布建至 Cofense 

本節將引導您完成設定 Azure AD 布建服務，以根據 Azure AD 的使用者，在 Cofense 中建立、更新及停用使用者的步驟。

### <a name="to-configure-automatic-user-provisioning-for-cofense-in-azure-ad"></a>若要在 Azure AD 中為 Cofense 設定自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [ **Cofense**]。

    ![應用程式清單中的 Cofense 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![佈建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![自動布建索引標籤](common/provisioning-automatic.png)

5. 在 [系統 **管理員認證** ] 區段下，輸入稍早從步驟2取出的 **SCIM 2.0 基底 Url 和 SCIM Authentication 權杖** 值。 按一下 [ **測試連接** ] 以確保 Azure AD 可以連線至 Cofense。 如果連接失敗，請確定您的 Cofense 帳戶具有系統管理員許可權，然後再試一次。

    ![租使用者 URL 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者至 Cofense**]。

9. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理到 Cofense 的使用者屬性。 選取為 [比對 **] 屬性的屬性會** 用來比對 Cofense 中的使用者帳戶以進行更新作業。  選取 [儲存] 按鈕以認可所有變更。

   |屬性|類型|
   |---|---|
   |userName|String|
   |externalId|String|
   |作用中|Boolean|
   |displayName|String|
   |name.formatted|String|
   |name.givenName|String|
   |name.familyName|String|
   |名稱. honorificSuffix|String|
   |phoneNumbers [type eq "work"]。值|String|
   |phoneNumbers [type eq "home"]. 值|String|
   |phoneNumbers [type eq "other"]. 值|String|
   |phoneNumbers [type eq "呼機"]. 值|String|
   |phoneNumbers [type eq "mobile"]. 值|String|
   |phoneNumbers [type eq "fax"]. 值|String|
   |位址 [type eq "other"]。已格式化|String|
   |位址 [type eq "work"]。已格式化|String|
   |位址 [type eq "work"]。 streetAddress|String|
   |位址 [type eq "work"]。位置|String|
   |位址 [type eq "work"]. region|String|
   |位址 [type eq "work"]。郵遞區號|String|
   |位址 [type eq "work"]。國家/地區|String|
   |title|String|
   |emails[type eq "work"].value|String|
   |電子郵件 [type eq "home"]。值|String|
   |電子郵件 [type eq "other"]。值|String|
   |preferredLanguage|String|
   |昵稱|String|
   |userType|String|
   |地區設定|String|
   |timezone|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|參考|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|

10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 若要啟用 Cofense Azure AD 的布建服務，請在 [**設定**] 區段中，將 [布建**狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建到 Cofense 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

13. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中所定義所有使用者和群組啟動首次同步處理週期。 初始週期會比後續週期花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
設定佈建後，請使用下列資源來監視您的部署：

1. 使用[佈建記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) \(部分機器翻譯\) 來判斷哪些使用者已佈建成功或失敗
2. 檢查[進度列](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) \(部分機器翻譯\) 來查看佈建週期的狀態，以及其接近完成的程度
3. 如果佈建設定似乎處於狀況不良的狀態，應用程式將會進入隔離狀態。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) \(部分機器翻譯\) 深入了解隔離狀態。  

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)
