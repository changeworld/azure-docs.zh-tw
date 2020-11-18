---
title: 教學課程：以 Azure Active Directory 設定 ServiceNow 來自動佈建使用者 | Microsoft Docs
description: 了解如何將使用者帳戶從 Azure AD 自動佈建和取消佈建至 ServiceNow。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: 1d6213d49c98f5e09f22e7310183315800d0c6f6
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359777"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>教學課程：設定 ServiceNow 來自動佈建使用者

本教學課程說明您需要在 ServiceNow 與 Azure Active Directory (Azure AD) 中執行的步驟，以設定自動使用者佈建。 設定後，Azure AD 就會使用 Azure AD 佈建服務，自動對 [ServiceNow](https://www.servicenow.com/) 佈建及取消佈建使用者和群組。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支援的功能
> [!div class="checklist"]
> * 在 ServiceNow 中建立使用者
> * 當使用者不再需要存取權時，將其從 ServiceNow 中移除
> * 讓 Azure AD 與 ServiceNow 之間的使用者屬性保持同步
> * 在 ServiceNow 中佈建群組和群組成員資格
> * [單一登入](servicenow-tutorial.md) 到 ServiceNow (建議)

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租用戶](../develop/quickstart-create-new-tenant.md) 
* Azure AD 中具有設定佈建[權限](../users-groups-roles/directory-assign-admin-roles.md)的使用者帳戶 (例如，應用程式管理員、雲端應用程式管理員、應用程式擁有者或全域管理員)。 
* Calgary 或更高版本的 [ServiceNow 執行個體](https://www.servicenow.com/)
* Helsinki 或更高版本的 [ServiceNow Express 執行個體](https://www.servicenow.com/)
* ServiceNow 中具有管理員角色的使用者帳戶

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃您的佈建部署
1. 了解[佈建服務的運作方式](../app-provisioning/user-provisioning.md)。
2. 判斷誰會在[佈建範圍](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)內。
3. 判斷哪些資料要[在 Azure AD 與 ServiceNow 之間對應](../app-provisioning/customize-application-attributes.md)。 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定 ServiceNow 以支援使用 Azure AD 進行佈建

1. 識別您的 ServiceNow 執行個體名稱。 您可在用來存取 ServiceNow 的 URL 中找到執行個體名稱。 在下面的範例中，執行個體名稱是 dev35214。

   ![ServiceNow 執行個體](media/servicenow-provisioning-tutorial/servicenow_instance.png)

2. 取得 ServiceNow 中管理員的認證。 瀏覽至 ServiceNow 中的使用者設定檔，並確認使用者具有管理員角色。 

   ![ServiceNow 管理員角色](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

3. 請檢查確定 ServiceNow 中 **已停用** 下列設定：

   1. 選取 [系統安全性] > [高安全性設定] > [要求傳入的 SCHEMA 要求進行基本驗證]。
   2. 選取 [系統屬性] > [Web 服務] > [要求傳入的 SOAP 要求進行基本授權]。
     
   > [!IMPORTANT]
   > 如果 *已啟用* 這些設定，佈建引擎將無法與 ServiceNow 通訊。

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式庫新增 ServiceNow

從 Azure AD 應用程式庫新增 ServiceNow，以開始管理對 ServiceNow 的佈建。 如果您先前已針對 SSO 設定 ServiceNow，則可使用相同的應用程式。 不過，建議您在一開始測試整合時，建立個別的應用程式。 [在此](../manage-apps/add-application-portal.md)深入了解從資源庫新增應用程式。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義將在佈建範圍內的人員 

Azure AD 佈建服務可供根據對應用程式的指派，或根據使用者/群組的屬性，界定將要佈建的人員。 如果您選擇根據指派來界定將佈建至應用程式的人員，您可以使用下列[步驟](../manage-apps/assign-user-or-group-access-portal.md)將使用者和群組指派給應用程式。 如果您選擇僅根據使用者或群組的屬性來界定將要佈建的人員，可以使用如[這裡](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的範圍篩選條件。 

* 將使用者和群組指派給 ServiceNow 時，您必須選取 [預設存取] 以外的角色。 具有預設存取角色的使用者會從佈建中排除，而且會在佈建記錄中被標示為沒有效率。 如果應用程式上唯一可用的角色是 [預設存取] 角色，您可以[更新應用程式資訊清單](../develop/howto-add-app-roles-in-azure-ad-apps.md) \(部分機器翻譯\) 以新增其他角色。 

* 從小規模開始。 在推出給所有人之前，先使用一小部分的使用者和群組進行測試。 當佈建範圍設為已指派的使用者和群組時，您可將一或兩個使用者或群組指派給應用程式來控制這點。 當範圍設為所有使用者和群組時，您可指定[以屬性為基礎的範圍篩選條件](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>步驟 5。 設定使用者自動佈建至 ServiceNow 

本節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 TestApp 中建立、更新和停用使用者和/或群組。

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>若要在 Azure AD 中為 ServiceNow 設定自動使用者佈建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [ServiceNow]。

    ![應用程式清單中的 ServiceNow 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![[管理] 選項的螢幕擷取畫面，並已指出 [佈建] 選項。](common/provisioning.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![[佈建模式] 下拉式清單的螢幕擷取畫面，並已指出 [自動] 選項。](common/provisioning-automatic.png)

5. 在 [管理員認證] 區段下，輸入您的 ServiceNow 管理員認證和使用者名稱。 按一下 [測試連線]，以確保 Azure AD 可以連線至 ServiceNow。 如果連線失敗，請確定您的 ServiceNow 帳戶具有管理員權限，然後再試一次。

    ![螢幕擷取畫面：顯示 [服務佈建] 頁面，您可以在其中輸入管理員認證。](./media/servicenow-provisioning-tutorial/provisioning.png)

6. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]。

8. 在 [對應] 區段下，選取 [將 Azure Active Directory 使用者同步處理至 ServiceNow]。

9. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步處理至 ServiceNow 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 ServiceNow 中的使用者帳戶，以進行更新作業。 如果您選擇變更[比對目標屬性](../app-provisioning/customize-application-attributes.md)，則必須確保 ServiceNow API 支援根據該屬性來篩選使用者。 選取 [儲存] 按鈕以認可所有變更。

10. 在 [對應] 區段中，選取 [將 Azure Active Directory 群組同步處理至 ServiceNow]。

11. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步處理至 ServiceNow 的群組屬性。 選取為 [比對] 屬性的屬性會用來比對 ServiceNow 中的群組以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要對 ServiceNow 啟用 Azure AD 佈建服務，請在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 透過在 [設定] 區段的 [範圍] 中選擇需要的值，可定義要佈建到 ServiceNow 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]  。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中所定義所有使用者和群組啟動首次同步處理週期。 初始週期會比後續週期花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
設定佈建後，請使用下列資源來監視您的部署：

1. 使用[佈建記錄](../reports-monitoring/concept-provisioning-logs.md) \(部分機器翻譯\) 來判斷哪些使用者已佈建成功或失敗
2. 檢查[進度列](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) \(部分機器翻譯\) 來查看佈建週期的狀態，以及其接近完成的程度
3. 如果佈建設定似乎處於狀況不良的狀態，應用程式將會進入隔離狀態。 [在此](../app-provisioning/application-provisioning-quarantine-status.md)深入了解隔離狀態。  

## <a name="troubleshooting-tips"></a>疑難排解秘訣
* **InvalidLookupReference：** 在 ServiceNow 中佈建某些屬性 (例如部門和位置) 時，這些值必須已經存在於 ServiceNow 的參考資料表中。 例如，在 ServiceNow 的 **插入資料表名稱** 資料表中，您可能有兩個位置 (西雅圖、洛杉磯) 和三個部門 (銷售、財務、行銷)。 如果您嘗試佈建屬於「銷售」部門且位於「西雅圖」的使用者，該使用者將會佈建成功。 如果您嘗試佈建屬於「銷售」部門且位於「LA」的使用者，則不會佈建該使用者。 位置 LA 必須新增至 ServiceNow 中的參考資料表，或者必須更新 Azure AD 中的使用者屬性，以符合 ServiceNow 中的格式。 
* **EntryJoiningPropertyValueIsMissing：** 檢閱 [屬性對應](../app-provisioning/customize-application-attributes.md)以找出相符的屬性。 這個值必須存在於您嘗試佈建的使用者或群組上。 
* 請參閱 [ServiceNow SOAP API](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) 以了解任何需求或限制 (例如，用以為使用者指定國家/地區代碼的格式)
* 佈建要求預設會傳送至 https://{your-instance-name}.service-now.com/{table-name}。 如果您需要自訂租用戶 URL，則可在執行個體名稱欄位中提供整個 URL。
* **ServiceNowInstanceInvalid** 
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

   此錯誤表示與 ServiceNow 執行個體通訊時發生問題。 請再次檢查以確定 ServiceNow 中 *已停用* 下列設定：
   
   1. 選取 [系統安全性] > [高安全性設定] > [要求傳入的 SCHEMA 要求進行基本驗證]。
   2. 選取 [系統屬性] > [Web 服務] > [要求傳入的 SOAP 要求進行基本授權]。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)