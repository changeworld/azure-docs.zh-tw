---
title: 教學課程：使用 Azure Active Directory 設定 Bizagi Studio 進行數位程式自動化來自動布建使用者 |Microsoft Docs
description: 瞭解如何將使用者帳戶從 Azure AD 自動布建和取消布建至 Bizagi Studio 以進行數位程式自動化。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 2fbff65a-5345-4c08-a6c7-60b80d867a3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2020
ms.author: Zhchia
ms.openlocfilehash: 4eaac716d06b102a07872059af28da4986889caa
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673430"
---
# <a name="tutorial-configure-bizagi-studio-for-digital-process-automation-for-automatic-user-provisioning"></a>教學課程：設定 Bizagi Studio 以進行自動使用者布建的數位流程自動化

本教學課程說明在 Bizagi Studio 中執行數位程式自動化和 Azure Active Directory (Azure AD) 設定自動使用者布建所需的步驟。 當設定為這樣做時，Azure AD 使用 Azure AD 布建服務，自動將使用者和群組布建及取消布建至 [Bizagi Studio 以進行數位程式自動化](https://www.bizagi.com/) 。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支援的功能
> [!div class="checklist"]
> * 在 Bizagi Studio 中建立使用者以進行數位程式自動化
> * 當 Bizagi Studio 中的使用者不再需要存取權時，將其移至數位程式自動化
> * 在 Azure AD 與 Bizagi Studio 之間保持使用者屬性同步處理以進行數位程式自動化
> * [單一登入](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial) Bizagi Studio 以進行數位程式自動化 (建議) 

## <a name="prerequisites"></a>必要條件

本教學課程中說明的案例假設您已經具有下列項目：

* [Azure AD 租用戶](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)。 
* Azure AD 中具有[權限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)可設定佈建的使用者帳戶。 範例包括應用程式系統管理員、雲端應用程式系統管理員、應用程式擁有者或全域管理員。 
* Bizagi Studio for 數位程式自動化版本 11.2.4.2 X 或更新版本。

## <a name="plan-your-provisioning-deployment"></a>規劃佈建部署
請依照下列步驟進行規劃：

1. 了解[佈建服務的運作方式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) \(部分機器翻譯\)。
2. 判斷誰將在布建 [範圍](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)內。
3. 判斷要 [在 Azure AD 與 Bizagi Studio 之間對應以進行數位程式自動化的](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)資料。 

## <a name="configure-to-support-provisioning-with-azure-ad"></a>設定以支援 Azure AD 的布建
若要設定 Bizagi Studio 以進行數位程式自動化，以支援 Azure AD 的布建，請遵循下列步驟：

1. 以具有系統 **管理員許可權** 的使用者身分登入您的工作入口網站。

2. 移至 [**管理員**  >  **安全性**  >  **OAuth 2 應用程式**]。

   ![Bizagi 的螢幕擷取畫面，其中已反白顯示 OAuth 2 應用程式。](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/admin.png)

3. 選取 [新增]。
4. 針對 **[授與類型**]，選取 [ **持有人權杖**]。 針對 **允許的範圍**，選取 [ **API** ] 和 [ **使用者同步**]。 接著，選取 [儲存]。

   ![醒目提示 [授與] 類型和允許範圍的 [註冊應用程式] 的螢幕擷取畫面。](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/token.png)

5. 複製並儲存 **用戶端密碼**。 在 Azure 入口網站中，針對 Bizagi Studio for 數位程式自動化應用程式，在 [布 **建] 索引** 標籤上，在 [ **秘密權杖** ] 欄位中輸入用戶端密碼值。

   ![Oauth 的螢幕擷取畫面，其中包含用戶端密碼 highlighed。](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/secret.png)

## <a name="add-the-application-from-the-azure-ad-gallery"></a>從 Azure AD 資源庫新增應用程式

若要開始管理布建至 Bizagi Studio 以進行數位程式自動化，請從 Azure AD 應用程式資源庫新增應用程式。 如果您先前已設定 Bizagi Studio 進行單一登入的數位流程自動化，您可以使用相同的應用程式。 不過，當您一開始測試整合時，您應該建立個別的應用程式。 如需詳細資訊，請參閱 [快速入門：將應用程式新增至 Azure Active Directory (Azure AD) 租使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)。 

## <a name="define-who-is-in-scope-for-provisioning"></a>定義在布建範圍內的人員 

使用 Azure AD 布建服務時，您可以根據使用者和群組的屬性（或兩者），為根據應用程式指派而布建的使用者進行範圍。 如果您的範圍是根據指派，請參閱使用圖形 API 將使用者和群組指派給應用程式的 [ [指派或取消指派使用者] 和 [群組](../manage-apps/assign-user-or-group-access-portal.md) ] 中的步驟。 如果您僅以使用者或群組的屬性為基礎，您可以使用範圍篩選器。 如需詳細資訊，請參閱 [使用範圍篩選器進行以屬性為基礎的應用程式布建](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 

請注意下列關於範圍設定的重點：

* 當您將使用者和群組指派給 Bizagi Studio 以進行數位程式自動化時，您必須選取 **預設存取** 以外的角色。 具有預設存取角色的使用者會從布建中排除，並在布建記錄中標示為不會有效地標示為無效。 如果應用程式上唯一可用的角色是預設存取角色，您可以[更新應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以新增更多角色。 

* 從小規模開始。 在推出給所有人之前，先使用一小部分的使用者和群組進行測試。 當布建的範圍設定為指派的使用者和群組時，您可以藉由將一或兩個使用者或群組指派給應用程式來控制此設定。 當範圍設定為 [所有使用者和群組] 時，您可以指定以 [屬性為基礎的範圍篩選準則](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="configure-automatic-user-provisioning"></a>設定使用者自動佈建 

本節將引導您完成設定 Azure AD 布建服務，以建立、更新及停用使用者和群組的步驟。 您會根據 Azure AD 中的使用者和群組指派，在測試應用程式中執行此作業。

### <a name="configure-automatic-user-provisioning-for-bizagi-studio-for-digital-process-automation-in-azure-ad"></a>在 Azure AD 中為 Bizagi Studio 設定自動使用者布建以進行數位程式自動化

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式] > [所有應用程式]。

    ![Azure 入口網站的螢幕擷取畫面，其中已醒目提示 [企業應用程式] 和 [所有應用程式]。](common/enterprise-applications.png)

2. 在應用程式清單中，選取 **Bizagi Studio 進行數位程式自動化**。

3. 選取 [佈建] 索引標籤。

    ![[管理] 選項的螢幕擷取畫面，其中已醒目提示 [布建]。](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![Screenshotof 布建模式控制項，並自動反白顯示。](common/provisioning-automatic.png)

5. 在 [ **管理員認證** ] 區段中，輸入適用于 Bizagi Studio 的租使用者 URL 和秘密權杖以進行數位程式自動化。 

      * **租使用者 URL：** 輸入 Bizagi SCIM 端點，其結構如下：  `<Your_Bizagi_Project>/scim/v2/` 。
         例如：`https://my-company.bizagi.com/scim/v2/`。

      * **秘密權杖：** 此值取自本文稍早所討論的步驟。

      若要確保 Azure AD 可以連接至 Bizagi Studio 以進行數位程式自動化，請選取 [ **測試連接**]。 如果連接失敗，請確定您的 Bizagi Studio for 數位程式自動化帳戶具有系統管理員許可權，然後再試一次。

   ![醒目提示 [測試連線] 的系統管理員認證螢幕擷取畫面。](common/provisioning-testconnection-tenanturltoken.png)

6. 針對 [ **通知電子郵件**]，輸入應收到布建錯誤通知之個人或群組的電子郵件地址。 選取在 **發生失敗時傳送電子郵件通知** 的選項。

    ![通知電子郵件選項的螢幕擷取畫面。](common/provisioning-notification-email.png)

7. 選取 [儲存]  。

8. **在 [** 對應] 區段中，選取 [**同步處理 Azure Active Directory 使用者至 Bizagi Studio 進行數位程式自動化**]。

9. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理至 Bizagi Studio 以進行數位程式自動化的使用者屬性。 選取為 [比對] 屬性 **的屬性是** 用來比對 Bizagi Studio 中的使用者帳戶，以進行更新作業的數位流程自動化。 如果您變更 [相符的目標屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，您必須確定 Bizagi Studio for 數位程式自動化 API 支援根據該屬性篩選使用者。 選取 [儲存] 認可任何變更。

   |屬性|類型|支援篩選|
   |---|---|---|
   |userName|String|&check;|
   |作用中|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |phoneNumbers[type eq "mobile"].value|String|

   您可以藉由流覽來 **> 顯示 [Bizagi] 的 [編輯屬性] 清單**，以新增自訂延伸模組屬性。 自訂延伸模組屬性的前面必須加上 **urn： ietf： params： scim：架構： extension： bizagi：2.0： UserProperties：**。 例如，如果自訂延伸模組屬性是 **IdentificationNumber**，則必須將屬性加入為 **urn： ietf： params： scim：架構： extension： bizagi：2.0： UserProperties： IdentificationNumber**。 選取 [儲存] 認可任何變更。
   
    ![編輯屬性清單。](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/edit.png)  

   有關如何新增自訂屬性的詳細資訊，可以在 [自訂應用程式屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)中找到。

> [!NOTE]
> 只支援基本類型屬性 (例如，字串、整數、布林值、日期時間等) 。 尚未支援連結至參數化資料表或多個類型的屬性。

10. 若要設定範圍篩選器，請參閱 [範圍篩選器教學](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)課程。

11. 若要啟用適用于 Bizagi Studio 進行數位程式自動化的 Azure AD 布建服務，請在 [ **設定** ] 區段中，將 [布建 **狀態** ] 變更為 [ **開啟**]。

    ![布建狀態切換的螢幕擷取畫面。](common/provisioning-toggle-on.png)

12. 定義您想要布建至 Bizagi Studio 以進行數位程式自動化的使用者和群組。 在 [ **設定** ] 區段中，選擇 [ **範圍**] 中所需的值。

    ![範圍選項的螢幕擷取畫面。](common/provisioning-scope.png)

13. 準備好要佈建時，請選取 [儲存]。

    ![[儲存控制項] 的螢幕擷取畫面。](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中所定義所有使用者和群組啟動首次同步處理週期。 初始週期會比後續週期花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 

## <a name="monitor-your-deployment"></a>監視您的部署
設定布建之後，請使用下列資源來監視您的部署：

- 使用[佈建記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)來判斷哪些使用者已佈建成功或失敗。
- 檢查 [進度](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) 列以查看布建週期的狀態，以及完成的關閉程度。
- 如果布建設定處於狀況不良的狀態，應用程式將會進入隔離狀態。 如需詳細資訊，請參閱 [隔離狀態中的應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)布建。  

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)
