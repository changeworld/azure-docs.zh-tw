---
title: 教學課程：以 Azure Active Directory 設定 8x8 來自動佈建使用者 | Microsoft Docs
description: 了解如何將使用者帳戶從 Azure AD 針對 8x8 進行自動佈建和取消佈建。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/15/2020
ms.author: Zhchia
ms.openlocfilehash: 90e3464ac9ddf1e839c3a731f79ac2c0771c37ea
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532687"
---
# <a name="tutorial-configure-8x8-for-automatic-user-provisioning"></a>教學課程：設定 8x8 來自動佈建使用者

本教學課程說明您需要在 8x8 Configuration Manager 與 Azure Active Directory (Azure AD) 中執行的步驟，以設定自動使用者佈建。 設定後，Azure AD 就會使用 Azure AD 佈建服務，自動對 [8x8](https://www.8x8.com) 佈建及取消佈建使用者和群組。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。 

## <a name="capabilities-supported"></a>支援的功能
> [!div class="checklist"]
> * 在 8x8 中建立使用者
> * 當使用者不再需要存取權時，將其從 8x8 中移除
> * 讓 Azure AD 與 8x8 之間的使用者屬性保持同步
> * [單一登入](./8x8virtualoffice-tutorial.md)至 8x8 (建議)

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租用戶](../develop/quickstart-create-new-tenant.md) 
* Azure AD 中具有設定佈建[權限](../roles/permissions-reference.md)的使用者帳戶 (例如，應用程式管理員、雲端應用程式管理員、應用程式擁有者或全域管理員)。
* 任何層級的 8x8 X 系列訂用帳戶。
* 在 [Configuration Manager](https://vo-cm.8x8.com) 中具有系統管理員權限的 8x8 使用者帳戶。
* 已設定[使用 Azure AD 進行單一登入](./8x8virtualoffice-tutorial.md)。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃佈建部署
1. 了解[佈建服務的運作方式](../app-provisioning/user-provisioning.md) \(部分機器翻譯\)。
2. 判斷誰會在[佈建範圍](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)內。
3. 判斷哪些資料要[在 Azure AD 與 8x8 之間對應](../app-provisioning/customize-application-attributes.md)。

## <a name="step-2-configure-8x8-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定 8x8 以支援使用 Azure AD 進行佈建

本節會引導您完成設定 8x8 以支援使用 Azure AD 佈建的步驟。

### <a name="to-configure-a-user-provisioning-access-token-in-8x8-configuration-manager"></a>若要在 8x8 Configuration Manager 中設定使用者佈建存取權杖：

1. 登入[管理主控台](https://admin.8x8.com)。 選取 [身分識別管理]。

   ![Admin](./media/8x8-provisioning-tutorial/8x8-identity-management.png)

2. 按一下 [顯示使用者佈建資訊] 連結以產生權杖。

   ![顯示](./media/8x8-provisioning-tutorial/8x8-show-user-provisioning.png)

3. 複製 **8x8 URL** 和 **8x8 API 權杖** 值。 這些值會分別輸入到 Azure 入口網站 8x8 應用程式 [佈建] 索引標籤中的 [租用戶 URL] 和 [祕密權杖] 欄位。

   ![Token](./media/8x8-provisioning-tutorial/8x8-copy-url-token.png)

## <a name="step-3-add-8x8-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式庫新增 8x8

從 Azure AD 應用程式庫新增 8x8，以開始管理對 8x8 的佈建。 如果您先前已針對 SSO 設定 8x8，則可使用相同的應用程式。 不過，建議您在一開始測試整合時，建立個別的應用程式。 [在此](../manage-apps/add-application-portal.md)深入了解從資源庫新增應用程式。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義將在佈建範圍內的人員

Azure AD 佈建服務可供根據對應用程式的指派，或根據使用者/群組的屬性，界定將要佈建的人員。 如果您選擇根據指派來界定將佈建至應用程式的人員，您可以使用下列[步驟](../manage-apps/assign-user-or-group-access-portal.md)將使用者和群組指派給應用程式。 這是比較簡單的選項，大部分的人都使用此選項。

如果您選擇僅根據使用者或群組的屬性來界定將要佈建的人員，可以使用如[這裡](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的範圍篩選條件。 

* 將使用者和群組指派給 8x8 時，您必須選取 [預設存取] 以外的角色。 具有預設存取角色的使用者會從佈建中排除，而且會在佈建記錄中被標示為沒有效率。 如果應用程式上唯一可用的角色是 [預設存取] 角色，您可以[更新應用程式資訊清單](../develop/howto-add-app-roles-in-azure-ad-apps.md) \(部分機器翻譯\) 以新增其他角色。 

* 從小規模開始。 在推出給所有人之前，先使用一小部分的使用者和群組進行測試。 當佈建範圍設為已指派的使用者和群組時，您可將一或兩個使用者或群組指派給應用程式來控制這點。 當範圍設為所有使用者和群組時，您可指定[以屬性為基礎的範圍篩選條件](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 

## <a name="step-5-configure-automatic-user-provisioning-to-8x8"></a>步驟 5。 設定使用者自動佈建至 8x8 

本節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 8x8 中建立、更新和停用使用者和/或群組。

### <a name="to-configure-automatic-user-provisioning-for-8x8-in-azure-ad"></a>在 Azure AD 中為 8x8 設定自動使用者佈建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](./media/8x8-provisioning-tutorial/enterprise-applications.png)

    ![所有應用程式刀鋒視窗](./media/8x8-provisioning-tutorial/all-applications.png)

2. 在應用程式清單中，選取 [8x8]。

    ![應用程式清單中的 8x8 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。按一下 [開始使用]。

    ![[管理] 選項的螢幕擷取畫面，並已指出 [佈建] 選項。](common/provisioning.png)

   ![[開始使用] 刀鋒視窗](./media/8x8-provisioning-tutorial/get-started.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![[佈建模式] 下拉式清單的螢幕擷取畫面，並已指出 [自動] 選項。](common/provisioning-automatic.png)

5. 在 [管理員認證] 區段下，將 **8x8 URL** 從 Configuration Manager 複製到 **租用戶 URL**。 將 **8x8 API 權杖** 從 Configuration Manager 複製到 **祕密權杖**。 按一下 [測試連線]，以確保 Azure AD 可以連線至 8x8。 如果連線失敗，請確定您的 8x8 帳戶具有系統管理員權限並再試一次。

    ![顯示 [管理員認證] 對話方塊的螢幕擷取畫面，您可以在其中輸入您的租用戶 URL 和祕密權杖。](./media/8x8-provisioning-tutorial/provisioning.png)

6. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]。

8. 在 [對應] 區段底下，選取 [佈建 Azure Active Directory 使用者]。

9. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 8x8 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 8x8 中的使用者帳戶以進行更新作業。 如果您選擇變更[比對目標屬性](../app-provisioning/customize-application-attributes.md)，則必須確保 8x8 API 支援根據該屬性來篩選使用者。 選取 [儲存] 按鈕以認可所有變更。

   |屬性|類型|注意|
   |---|---|---|
   |userName|String|同時設定使用者名稱和同盟識別碼|
   |externalId|String||
   |作用中|Boolean||
   |title|String||
   |emails[type eq "work"].value|String||
   |name.givenName|String||
   |name.familyName|String||
   |phoneNumbers[type eq "mobile"].value|String|個人連絡號碼|
   |phoneNumbers[type eq "work"].value|String|個人連絡號碼|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String||
   |urn:ietf:params:scim:schemas:extension:8x8:1.1:User:site|String|使用者建立之後無法更新|
   |地區設定|String|預設未對應|
   |timezone|String|預設未對應|

10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 若要啟用 8x8 的 Azure AD 佈建服務，在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 透過在 [設定] 區段的 [範圍] 中選擇需要的值，可定義要佈建到 8x8 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

13. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中所定義所有使用者和群組啟動首次同步處理週期。 初始週期會比後續週期花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
設定佈建後，請使用下列資源來監視您的部署：

1. 使用[佈建記錄](../reports-monitoring/concept-provisioning-logs.md)來判斷哪些使用者已佈建成功或失敗。
2. 檢查[進度列](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)來查看佈建週期的狀態，以及其接近完成的程度。
3. 如果佈建設定似乎處於狀況不良的狀態，應用程式將會進入隔離狀態。 [在此](../app-provisioning/application-provisioning-quarantine-status.md) \(部分機器翻譯\) 深入了解隔離狀態。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)