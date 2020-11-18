---
title: 教學課程：以 Azure Active Directory 設定 SAP Analytics Cloud 來自動佈建使用者 | Microsoft Docs
description: 了解如何從 Azure AD 對 SAP Analytics Cloud 自動佈建和取消佈建使用者帳戶。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 27d12989-efa8-4254-a4ad-8cb6bf09d839
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2020
ms.author: Zhchia
ms.openlocfilehash: b87b9d9e7cab0334f1b1996feb99dc69396527b7
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94352943"
---
# <a name="tutorial-configure-sap-analytics-cloud-for-automatic-user-provisioning"></a>教學課程：設定 SAP Analytics Cloud 來自動佈建使用者

本教學課程說明您需要在 SAP Analytics Cloud 與 Azure Active Directory (Azure AD) 中執行的步驟，以設定自動使用者佈建。 設定後，Azure AD 就會使用 Azure AD 佈建服務，自動對 [SAP Analytics Cloud](https://www.sapanalytics.cloud/) 佈建及取消佈建使用者和群組。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支援的功能
> [!div class="checklist"]
> * 在 SAP Analytics Cloud 中建立使用者
> * 當使用者不再需要存取權時，將其從 SAP Analytics Cloud 中移除
> * 讓 Azure AD 與 SAP Analytics Cloud 之間的使用者屬性保持同步
> * 對 SAP Analytics Cloud 使用[單一登入](sapboc-tutorial.md) (建議)

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租用戶](../develop/quickstart-create-new-tenant.md) 
* Azure AD 中具有設定佈建[權限](../users-groups-roles/directory-assign-admin-roles.md)的使用者帳戶 (例如，應用程式管理員、雲端應用程式管理員、應用程式擁有者或全域管理員)。 
* SAP Analytics Cloud 租用戶
* SAP 身分識別佈建管理主控台上具有管理員權限的使用者帳戶。 請確定您可以存取身分識別佈建管理主控台中的 Proxy 系統。 如果您沒有看到 [Proxy 系統] 磚，請為 **BC-IAM-IPS** 元件建立事件，以要求此磚的存取權。
* 在 SAP Analytics Cloud 中具有授權授與用戶端認證的 OAuth 用戶端。 若要了解如何進行，請參閱︰[管理 OAuth 用戶端和信任的識別提供者](https://help.sap.com/viewer/00f68c2e08b941f081002fd3691d86a7/release/en-US/4f43b54398fc4acaa5efa32badfe3df6.html)

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃佈建部署

1. 了解[佈建服務的運作方式](../app-provisioning/user-provisioning.md) \(部分機器翻譯\)。
2. 判斷誰會在[佈建範圍](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)內。
3. 判斷哪些資料要[在 Azure AD 與 SAP Analytics Cloud 之間對應](../app-provisioning/customize-application-attributes.md)。 

## <a name="step-2-configure-sap-analytics-cloud-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定 SAP Analytics Cloud 以支援使用 Azure AD 進行佈建

1. 使用您的系統管理員帳戶登入 [SAP 身分識別佈建管理主控台](https://ips-xlnk9v890j.dispatcher.us1.hana.ondemand.com/)，然後選取 [Proxy 系統]。

   ![SAP Proxy 系統](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems.png.png)

2. 選取 [屬性] 。

   ![SAP Proxy 系統屬性](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-properties.png)

3. 複製 **URL** 並將 `/api/v1/scim` 附加至 URL。 將其儲存，以便稍後用於 [租用戶 URL] 欄位中。

   ![SAP Proxy 系統屬性 URL](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-details-url.png)

4. 使用 [POSTMAN](https://www.postman.com/) 來對此位址執行 POST HTTPS 呼叫：`<Token URL>?grant_type=client_credentials`，其中 `Token URL` 是 **OAuth2TokenServiceURL** 欄位中的 URL。 若要產生在設定自動佈建時，要在 [祕密權杖] 欄位中使用的存取權杖，就需要執行此步驟。

   ![SAP IP Proxy 系統屬性 OAuth](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-details-oauth.png)

5. 在 Postman 中使用 **基本驗證**，並將 OAuth 用戶端識別碼設定為使用者，將祕密設為密碼。 此呼叫會傳回存取權杖。 將其複製並保存，以供稍後用於 [祕密權杖] 欄位中。

   ![Postman POST 要求](./media/sap-analytics-cloud-provisioning-tutorial/postman-post-request.png)

## <a name="step-3-add-sap-analytics-cloud-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式庫新增 SAP Analytics Cloud

從 Azure AD 應用程式庫新增 SAP Analytics Cloud，以開始管理對 SAP Analytics Cloud 的佈建。 如果您先前已針對 SSO 設定 SAP Analytics Cloud，則可使用相同的應用程式。 不過，建議您在一開始測試整合時，建立個別的應用程式。 [在此](../manage-apps/add-application-portal.md)深入了解從資源庫新增應用程式。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義將在佈建範圍內的人員 

Azure AD 佈建服務可供根據對應用程式的指派，或根據使用者/群組的屬性，界定將要佈建的人員。 如果您選擇根據指派來界定將佈建至應用程式的人員，您可以使用下列[步驟](../manage-apps/assign-user-or-group-access-portal.md)將使用者和群組指派給應用程式。 如果您選擇僅根據使用者或群組的屬性來界定將要佈建的人員，可以使用如[這裡](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的範圍篩選條件。 

* 將使用者和群組指派給 SAP Analytics Cloud 時，您必須選取 [預設存取] 以外的角色。 具有預設存取角色的使用者會從佈建中排除，而且會在佈建記錄中被標示為沒有效率。 如果應用程式上唯一可用的角色是 [預設存取] 角色，您可以[更新應用程式資訊清單](../develop/howto-add-app-roles-in-azure-ad-apps.md) \(部分機器翻譯\) 以新增其他角色。 

* 從小規模開始。 在推出給所有人之前，先使用一小部分的使用者和群組進行測試。 當佈建範圍設為已指派的使用者和群組時，您可將一或兩個使用者或群組指派給應用程式來控制這點。 當範圍設為所有使用者和群組時，您可指定[以屬性為基礎的範圍篩選條件](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 


## <a name="step-5-configure-automatic-user-provisioning-to-sap-analytics-cloud"></a>步驟 5。 對 SAP Analytics Cloud 設定自動使用者佈建 

此節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 TestApp 中建立、更新和停用使用者和/或群組。

### <a name="to-configure-automatic-user-provisioning-for-sap-analytics-cloud-in-azure-ad"></a>若要在 Azure AD 中對 SAP Analytics Cloud 設定自動使用者佈建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [SAP Analytics Cloud]。

    ![應用程式清單中的 SAP Analytics Cloud](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![[管理] 選項的螢幕擷取畫面，並已指出 [佈建] 選項。](common/provisioning.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![[佈建模式] 下拉式清單的螢幕擷取畫面，並已指出 [自動] 選項。](common/provisioning-automatic.png)

5. 在 [管理員認證] 區段下，輸入稍早在 [租用戶 URL] 中擷取的租用戶 URL 值。 輸入稍早在 [祕密權杖] 中所擷取的存取權杖值。 按一下 [測試連線]，以確保 Azure AD 可以連線至 InVision。 如果連線失敗，請確定您的 SAP Analytics Cloud 帳戶具有管理員權限，然後再試一次。

    ![顯示 [管理員認證] 對話方塊的螢幕擷取畫面，您可以在其中輸入您的租用戶 URL 和祕密權杖。](./media/sap-analytics-cloud-provisioning-tutorial/provisioning.png)

6. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]。

8. 在 [對應] 區段底下，選取 [佈建 Azure Active Directory 使用者]。

9. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 SAP Analytics Cloud 的使用者屬性。 選取為 [比對] 屬性 (Property) 的屬性 (Attribute) 會用來比對 SAP Analytics Cloud 中的使用者帳戶以進行更新作業。 如果您選擇變更[比對目標屬性](../app-provisioning/customize-application-attributes.md)，則必須確保 SAP Analytics Cloud API 支援根據該屬性來篩選使用者。 選取 [儲存] 按鈕以認可所有變更。

   |屬性|類型|支援篩選|
   |---|---|---|
   |userName|String|&check;|
   |name.givenName|String|
   |name.familyName|String|
   |作用中|Boolean|
   |emails[type eq "work"].value|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|String|

10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 若要啟用 SAP Analytics Cloud 的 Azure AD 佈建服務，在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 透過在 [設定] 區段的 [範圍] 中選擇需要的值，可定義要佈建到 SAP Analytics Cloud 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

13. 當您準備好要佈建時，按一下 [儲存]  。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中所定義所有使用者和群組啟動首次同步處理週期。 初始週期會比後續週期花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
設定佈建後，請使用下列資源來監視您的部署：

1. 使用[佈建記錄](../reports-monitoring/concept-provisioning-logs.md) \(部分機器翻譯\) 來判斷哪些使用者已佈建成功或失敗
2. 檢查[進度列](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) \(部分機器翻譯\) 來查看佈建週期的狀態，以及其接近完成的程度
3. 如果佈建設定似乎處於狀況不良的狀態，應用程式將會進入隔離狀態。 [在此](../app-provisioning/application-provisioning-quarantine-status.md)深入了解隔離狀態。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)