---
title: 教學課程：設定 SolarWinds 服務台 (先前 Samanage 的) ，以 Azure Active Directory 的自動使用者布建 |Microsoft Docs
description: 瞭解如何從 Azure AD 將使用者帳戶自動布建和取消布建至 SolarWinds 服務台 (先前 Samanage 的) 。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: 0941c95ee6215a710ebb1bbc2fba9fae09ccf16d
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707261"
---
# <a name="tutorial-configure-solarwinds-service-desk-previously-samanage-for-automatic-user-provisioning"></a>教學課程：設定 SolarWinds 服務台 (先前 Samanage 的自動使用者布建) 

本教學課程說明在 SolarWinds 服務台 (先前 Samanage 的) 和 Azure Active Directory (Azure AD) 設定自動使用者布建所需執行的步驟。 設定時，Azure AD 會使用 Azure AD 布建服務，自動將使用者和群組布建並取消布建到 [SolarWinds 服務台](https://www.samanage.com/pricing/) 。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。

## <a name="migrate-to-the-new-solarwinds-service-desk-application"></a>遷移至新的 SolarWinds 服務台應用程式

如果您有與 SolarWinds 服務台的現有整合，請參閱下一節有關即將進行的變更。 如果您是第一次設定 SolarWinds 服務台，可以略過本節並移至 **支援的功能**。

#### <a name="whats-changing"></a>有哪些變更項目？

* Azure AD 端的變更：在 Samange 中布建使用者的授權方法，在過去是 **基本驗證**。您很快就會看到授權方法變更為 **長時間的秘密權杖**。


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>若要將現有的自訂整合遷移至新的應用程式，我需要做什麼？

如果您有現有的 SolarWinds 服務台與有效的系統管理員認證整合， **則不需要採取任何動作**。 我們會自動將客戶遷移至新的應用程式。 此程式會在幕後完全完成。 如果現有的認證過期，或您需要再次授權存取應用程式，則需要產生長期的秘密權杖。 若要產生新的權杖，請參閱本文的步驟2。


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>如何分辨我的應用程式是否已遷移？ 

當您的應用程式遷移時，在 [ **管理員認證** ] 區段中，系統 **管理員使用者名稱** 和 **管理員密碼** 欄位將會取代為單一 **秘密權杖** 欄位。

## <a name="capabilities-supported"></a>支援的功能

> [!div class="checklist"]
> * 在 SolarWinds 服務台中建立使用者
> * 當使用者不再需要存取權時，請移除 SolarWinds 服務台中的使用者
> * Azure AD 與 SolarWinds 之間保持使用者屬性同步服務台
> * 在 SolarWinds 服務台中布建群組和群組成員資格
> * [單一登入](https://docs.microsoft.com/azure/active-directory/saas-apps/samanage-tutorial) SolarWinds 服務台 (建議的) 

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租用戶](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中具有設定佈建[權限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的使用者帳戶 (例如，應用程式管理員、雲端應用程式管理員、應用程式擁有者或全域管理員)。 
* 具有專業套件的 [SolarWinds 服務台租](https://www.samanage.com/pricing/) 使用者。
* SolarWinds 服務台中具有系統管理員許可權的使用者帳戶。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃佈建部署
1. 了解[佈建服務的運作方式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) \(部分機器翻譯\)。
2. 判斷誰會在[佈建範圍](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)內。
3. 判斷要 [在 Azure AD 與 SolarWinds 服務台之間對應](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的資料。 

## <a name="step-2-configure-solarwinds-service-desk-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定 SolarWinds 服務台以支援以 Azure AD 布建

若要產生驗證的秘密權杖，請參閱 [API 整合的教學課程權杖驗證](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657)。

## <a name="step-3-add-solarwinds-service-desk-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式庫新增 SolarWinds 服務台

從 Azure AD 應用程式資源庫新增 SolarWinds 服務台，開始管理 SolarWinds 服務台的布建。 如果您先前已設定 SSO 的 SolarWinds 服務台，您可以使用相同的應用程式。 不過，建議您在一開始測試整合時，建立個別的應用程式。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)深入了解從資源庫新增應用程式。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義將在佈建範圍內的人員 

Azure AD 佈建服務可供根據對應用程式的指派，或根據使用者/群組的屬性，界定將要佈建的人員。 如果您選擇根據指派來界定將佈建至應用程式的人員，您可以使用下列[步驟](../manage-apps/assign-user-or-group-access-portal.md)將使用者和群組指派給應用程式。 如果您選擇僅根據使用者或群組的屬性來界定將要佈建的人員，可以使用如[這裡](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的範圍篩選條件。 

* 將使用者和群組指派給 SolarWinds 服務台時，您必須選取 **預設存取**以外的角色。 具有預設存取角色的使用者會從佈建中排除，而且會在佈建記錄中被標示為沒有效率。 如果應用程式上唯一可用的角色是 [預設存取] 角色，您可以[更新應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) \(部分機器翻譯\) 以新增其他角色。 

* 從小規模開始。 在推出給所有人之前，先使用一小部分的使用者和群組進行測試。 當佈建範圍設為已指派的使用者和群組時，您可將一或兩個使用者或群組指派給應用程式來控制這點。 當範圍設為所有使用者和群組時，您可指定[以屬性為基礎的範圍篩選條件](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-solarwinds-service-desk"></a>步驟 5。 設定自動使用者布建至 SolarWinds 服務台 

此節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 TestApp 中建立、更新和停用使用者和/或群組。

### <a name="to-configure-automatic-user-provisioning-for-solarwinds-service-desk-in-azure-ad"></a>若要在 Azure AD 中設定 SolarWinds 服務台的自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [ **SolarWinds 服務台**]。

3. 選取 [佈建] 索引標籤。

    ![顯示已選取 [布建] 索引標籤的螢幕擷取畫面。](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![顯示布建模式設定為 [自動] 的螢幕擷取畫面。](common/provisioning-automatic.png)

5. 在 [ **管理員認證** ] 區段下的 [ `https://api.samanage.com` **租使用者 URL**] 中輸入。  輸入稍早在 [祕密權杖] 中所擷取的祕密權杖值。 選取 [ **測試連接** ] 以確保 Azure AD 可以連線到 SolarWinds 服務台。 如果連接失敗，請確定您的 SolarWinds 服務台帳戶具有系統管理員許可權，然後再試一次。

    ![顯示已選取 [測試連接] 按鈕的螢幕擷取畫面。](./media/samanage-provisioning-tutorial/provisioning.png)

6. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者] 以 SolarWinds 服務台**。

9. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理到 SolarWinds 服務台的使用者屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對 SolarWinds 服務台中的使用者帳戶，以進行更新作業。 如果您選擇變更相符的 [目標屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，您將需要確保 SOLARWINDS 服務台 API 支援根據該屬性篩選使用者。 選取 [儲存] 按鈕以認可所有變更。

      ![Samange 使用者對應](./media/samanage-provisioning-tutorial/user-attributes.png)

10. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 群組] 以 SolarWinds 服務台**。

11. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理到 SolarWinds 服務台的群組屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對 SolarWinds 服務台中的群組以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

      ![Samange 群組對應](./media/samanage-provisioning-tutorial/group-attributes.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要啟用 SolarWinds 服務台的 Azure AD 布建服務，請在 [**設定**] 區段中，將 [布建**狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建到 SolarWinds 服務台的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要布建時，請選取 [ **儲存**]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中所定義所有使用者和群組啟動首次同步處理週期。 初始週期會比後續週期花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
設定佈建後，請使用下列資源來監視您的部署：

1. 使用[佈建記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) \(部分機器翻譯\) 來判斷哪些使用者已佈建成功或失敗
2. 檢查[進度列](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) \(部分機器翻譯\) 來查看佈建週期的狀態，以及其接近完成的程度
3. 如果佈建設定似乎處於狀況不良的狀態，應用程式將會進入隔離狀態。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) \(部分機器翻譯\) 深入了解隔離狀態。

## <a name="connector-limitations"></a>連接器限制

如果您選取 [ **同步所有使用者和群組** ] 選項，並設定 [SolarWinds 服務台 **角色** ] 屬性的值，則 [ **預設值 ** ] 下的值 (若為 [選擇性]，則 [預設值] 下的值必須以下列格式表示) box：

- {"displayName"： "role"}，其中 role 是您想要的預設值。

## <a name="change-log"></a>變更記錄

* 09/14/2020-已將兩個 SaaS 教學課程中的公司名稱從 Samanage 變更為 SolarWinds 服務台 (先前每個 Samanage) https://github.com/ravitmorales 。
* 04/22/2020-更新授權方法，從基本驗證到長期的秘密權杖。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)
