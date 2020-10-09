---
title: 教學課程：以 Azure Active Directory 設定 MediusFlow 來自動佈建使用者 | Microsoft Docs
description: 了解如何將使用者帳戶從 Azure AD 針對 MediusFlow 進行自動佈建和取消佈建。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 04/30/2020
ms.author: Zhchia
ms.openlocfilehash: 71dc05e3949c2675be6255723530693304e1aa3e
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91850076"
---
# <a name="tutorial-configure-mediusflow-for-automatic-user-provisioning"></a>教學課程：設定 MediusFlow 來自動佈建使用者

本教學課程說明您需要在 MediusFlow 與 Azure Active Directory (Azure AD) 中執行的步驟，以設定自動使用者佈建。 設定後，Azure AD 就會使用 Azure AD 佈建服務，自動對 [MediusFlow](https://www.mediusflow.com/) 佈建及取消佈建使用者和群組。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支援的功能
> [!div class="checklist"]
> * 在 MediusFlow 中建立使用者
> * 當使用者不再需要存取權時，請從 MediusFlow 中將其移除
> * 讓 Azure AD 與 MediusFlow 之間的使用者屬性保持同步
> * 在 MediusFlow 中佈建群組和群組成員資格
> * 單一登入到 MediusFlow (建議)

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租用戶](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中具有設定佈建[權限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的使用者帳戶 (例如，應用程式管理員、雲端應用程式管理員、應用程式擁有者或全域管理員)。 
* 具有品質保證或生產租用戶的有效 MediusFlow 訂用帳戶。
* MediusFlow 中具有管理員存取權限的使用者帳戶，能夠在 MediusFlow 內執行設定。
* 在 MediusFlow 租用戶中新增的公司，應在其中佈建使用者。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃您的佈建部署
1. 了解[佈建服務的運作方式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 判斷誰會在[佈建範圍](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)內。
3. 判斷哪些資料要[在 Azure AD 與 MediusFlow 之間對應](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)。 

## <a name="step-2-configure-mediusflow-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定 MediusFlow 以支援使用 Azure AD 進行佈建

### <a name="activate-the-microsoft-365-app-within-mediusflow"></a>在 MediusFlow 中啟用 Microsoft 365 應用程式
藉由執行下列步驟，開始在 MediusFlow 內啟用 Azure AD 登入和 Azure AD 設定功能的存取權：

#### <a name="user-login"></a>使用者登入
若要啟用 Microsoft 365/Azure AD 的登入流程，請參閱 [this] (https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-login-setup) 文章。

#### <a name="user-transfer-configuration"></a>使用者轉移設定
若要從 Azure AD 啟用使用者布建的設定入口網站，請參閱 [這](
https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-sync-setup) 篇文章。

#### <a name="configure-user-provisioning"></a>設定使用者佈建

1.  藉由提供租用戶識別碼，登入 [MediusFlow 管理主控台](https://office365.cloudapp.mediusflow.com/)。

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/1-auth.png" alt-text="MediusFlow 管理主控台的螢幕擷取畫面。[MediusFlow 租使用者名稱] 方塊和 [驗證] 按鈕會在第一個整合步驟中反白顯示。" border="false":::

2. 驗證與 MediusFlow 的連線。

    ![Verify](./media/mediusflow-provisioning-tutorial/2-verify-connection.png)

3. 提供 Azure AD 的租用戶識別碼。

    ![提供租用戶識別碼](./media/mediusflow-provisioning-tutorial/3-provide-azuread-tenantid.png)

   您可以在[常見問題集](https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#how-do-i-get-the-azure-tenant-id)中閱讀更多有關如何尋找的資訊。

4. 儲存組態。

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/4-save-config.png" alt-text="MediusFlow 管理主控台的螢幕擷取畫面。[MediusFlow 租使用者名稱] 方塊和 [驗證] 按鈕會在第一個整合步驟中反白顯示。" border="false":::

5. 選取 [使用者佈建]，然後按一下 [確定]。

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/5-select-user-provisioning.png" alt-text="MediusFlow 管理主控台的螢幕擷取畫面。[MediusFlow 租使用者名稱] 方塊和 [驗證] 按鈕會在第一個整合步驟中反白顯示。" border="false":::

6. 按一下 [產生祕密金鑰]。 複製並儲存此值。此值會輸入到 Azure 入口網站 MediusFLow 應用程式 [佈建] 索引標籤中的 [祕密權杖] 欄位。

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/6-create-secret-1.png" alt-text="MediusFlow 管理主控台的螢幕擷取畫面。[MediusFlow 租使用者名稱] 方塊和 [驗證] 按鈕會在第一個整合步驟中反白顯示。" border="false":::

7. 按一下 [確定]。

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/7-confirm-secret.png" alt-text="MediusFlow 管理主控台的螢幕擷取畫面。[MediusFlow 租使用者名稱] 方塊和 [驗證] 按鈕會在第一個整合步驟中反白顯示。" border="false":::

8. 若要使用 MediusFlow 中預先定義的一組角色、公司和其他一般設定來匯入使用者，您必須先進行設定。 首先，按一下 [新增設定] 來新增設定。

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/8-configure-user-configuration-1.png" alt-text="MediusFlow 管理主控台的螢幕擷取畫面。[MediusFlow 租使用者名稱] 方塊和 [驗證] 按鈕會在第一個整合步驟中反白顯示。" border="false":::

9. 提供使用者的預設設定。 在此檢視中，您可以設定預設屬性。 如果標準設定為正確，則只提供有效的公司名稱就足夠。 由於這些組態設定是從 Mediusflow 擷取，因此必須先進行設定。 如需詳細資訊，請參閱本文的＜必要條件＞一節。

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/9-configure-user-config-detail-1.png" alt-text="MediusFlow 管理主控台的螢幕擷取畫面。[MediusFlow 租使用者名稱] 方塊和 [驗證] 按鈕會在第一個整合步驟中反白顯示。" border="false":::

10. 按一下 [儲存] 以儲存使用者設定。

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/10-done-1.png" alt-text="MediusFlow 管理主控台的螢幕擷取畫面。[MediusFlow 租使用者名稱] 方塊和 [驗證] 按鈕會在第一個整合步驟中反白顯示。" border="false":::

11. 若要取得使用者佈建連結，請按一下 [複製 SCIM 連結]。 複製並儲存此值。 此值會在 Azure 入口網站中 MediusFLow 應用程式的 [佈建] 索引標籤中，[租用戶 URL] 欄位中輸入。
 
    :::image type="content" source="./media/mediusflow-provisioning-tutorial/11-get-scim-link.png" alt-text="MediusFlow 管理主控台的螢幕擷取畫面。[MediusFlow 租使用者名稱] 方塊和 [驗證] 按鈕會在第一個整合步驟中反白顯示。" border="false":::

## <a name="step-3-add-mediusflow-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式庫新增 MediusFlow

從 Azure AD 應用程式庫新增 MediusFlow，以開始管理對 MediusFlow 的佈建。 如果您先前已針對 SSO 設定 MediusFlow，則可使用相同的應用程式。 不過，建議您在一開始測試整合時，建立個別的應用程式。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)深入了解從資源庫新增應用程式。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義將在佈建範圍內的人員 

Azure AD 佈建服務可讓您根據對應用程式的指派，或根據使用者/群組的屬性，界定將要佈建的人員。 如果您選擇根據指派來界定將佈建至應用程式的人員，您可以使用下列[步驟](../manage-apps/assign-user-or-group-access-portal.md)將使用者和群組指派給應用程式。 如果您選擇僅根據使用者或群組的屬性來界定將要佈建的人員，可以使用如[這裡](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的範圍篩選條件。 

* 將使用者和群組指派給 MediusFlow 時，您必須選取 [預設存取] 以外的角色。 具有預設存取角色的使用者會從佈建中排除，而且會在佈建記錄中被標示為沒有效率。 如果應用程式上唯一可用的角色是預設存取角色，您可[更新應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以新增其他角色。 

* 從小規模開始。 在推出給所有人之前，先使用一小部分的使用者和群組進行測試。 當佈建範圍設為已指派的使用者和群組時，您可將一或兩個使用者或群組指派給應用程式來進行控制。 當範圍設為所有使用者和群組時，您可指定[以屬性為基礎的範圍篩選條件](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-mediusflow"></a>步驟 5。 設定使用者自動佈建至 MediusFlow 

本節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 TestApp 中建立、更新和停用使用者和/或群組。

### <a name="to-configure-automatic-user-provisioning-for-mediusflow-in-azure-ad"></a>若要在 Azure AD 中為 MediusFlow 設定自動使用者佈建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [MediusFlow]。

    ![應用程式清單中的 MediusFlow 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![已呼叫 [布建] 選項的 [管理選項] 螢幕擷取畫面。](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![[布建模式] 下拉式清單的螢幕擷取畫面，其中已呼叫 [自動] 選項。](common/provisioning-automatic.png)

5. 在 [管理員認證] 區段下，輸入稍早在 [租用戶 URL] 中取得的租用戶 URL 值。 輸入稍早在 [祕密權杖] 中所取得的祕密權杖值。 按一下 [測試連線]，以確保 Azure AD 可以連線至 MediusFlow。 如果連線失敗，請確定您的 MediusFlow 帳戶具有管理員權限，然後再試一次。

      ![螢幕擷取畫面顯示 [管理認證] 對話方塊，您可以在其中輸入租使用者 U R L 和秘密權杖。](./media/mediusflow-provisioning-tutorial/provisioning.png)

6. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]。

8. 在 [對應] 區段下，選取 [同步處理 Azure Active Directory 使用者至 MediusFlow]。

9. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步處理至 MediusFlow 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 MediusFlow 中的使用者帳戶，以進行更新作業。 如果您選擇變更[比對目標屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，則必須確保 MediusFlow API 支援根據該屬性來篩選使用者。 選取 [儲存] 按鈕以認可所有變更。

   |屬性|類型|
   |---|---|
   |userName|String|
   |emails[type eq "work"].value|String|
   |name.displayName|String|
   |作用中|Boolean|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |externalID|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|參考|


10. 在 [對應] 區段中，選取 [同步處理 Azure Active Directory 群組至 MediusFlow]。

11. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步處理至 MediusFlow 的群組屬性。 選取為 [比對] 屬性的屬性會用來比對 MediusFlow 中的群組以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

      |屬性|類型|
      |---|---|
      |displayName|String|
      |externalID|String|
      |members|參考|

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要對 MediusFlow 啟用 Azure AD 佈建服務，請在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 透過在 [設定] 區段的 [範圍] 中選擇需要的值，可定義要佈建到 MediusFlow 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中定義的所有使用者和群組，啟動首次同步處理週期。 初始週期會比後續週期花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
設定佈建後，請使用下列資源來監視您的部署：

1. 使用[佈建記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)來判斷哪些使用者已佈建成功或失敗
2. 檢查[進度列](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)來查看佈建週期的狀態，以及其接近完成的程度
3. 如果佈建設定似乎處於狀況不良的狀態，應用程式將會進入隔離狀態。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)深入了解隔離狀態。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)
