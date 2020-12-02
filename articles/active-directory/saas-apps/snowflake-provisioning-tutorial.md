---
title: 教學課程：以 Azure Active Directory 設定 Snowflake 來自動佈建使用者 | Microsoft Docs
description: 了解如何設定 Azure Active Directory，以對 Snowflake 自動佈建及取消佈建使用者帳戶。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: ac6981d998f25b8df7cea553fe152d68cd43fee5
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181415"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>教學課程：設定 Snowflake 來自動佈建使用者

本教學課程旨在示範將 Azure AD 設定為可對 [Snowflake](https://www.Snowflake.com/pricing/) 自動佈建及取消佈建使用者和/或群組時，須在 Snowflake 與 Azure Active Directory (Azure AD) 中執行的步驟。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。 


> [!NOTE]
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="capabilities-supported"></a>支援的功能
> [!div class="checklist"]
> * 在 Snowflake 中建立使用者
> * 當使用者不再需要存取時，將其從 Snowflake 中移除
> * 讓 Azure AD 與 Snowflake 之間的使用者屬性保持同步
> * 在 Snowflake 中佈建群組和群組成員資格
> * [單一登入](./snowflake-tutorial.md)至 Snowflake (建議)

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租用戶](../develop/quickstart-create-new-tenant.md)。
* Azure AD 中具有設定佈建[權限](../roles/permissions-reference.md)的使用者帳戶 (例如，應用程式管理員、雲端應用程式管理員、應用程式擁有者或全域管理員)。
* [Snowflake 租用戶](https://www.Snowflake.com/pricing/)。
* Snowflake 中具有管理員權限的使用者帳戶。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃佈建部署
1. 了解[佈建服務的運作方式](../app-provisioning/user-provisioning.md) \(部分機器翻譯\)。
2. 判斷誰會在[佈建範圍](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)內。
3. 判斷哪些資料要[在 Azure AD 與 Snowflake 之間對應](../app-provisioning/customize-application-attributes.md)。 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定 Snowflake 以支援使用 Azure AD 進行佈建

以 Azure AD 設定 Snowflake 來自動佈建使用者之前，您必須在 Snowflake 上啟用 SCIM 佈建。

1. 登入您的 Snowflake 管理主控台。 在醒目提示的工作表中輸入顯示如下的查詢，然後按一下 [執行]。

    ![Snowflake 管理主控台](media/Snowflake-provisioning-tutorial/image00.png)

2.  系統會為您的 Snowflake 租用戶產生 SCIM 存取權杖。 若要加以擷取，請按一下下方醒目提示的連結。

    ![Snowflake UI 中所含工作表的螢幕擷取畫面，其中已標註 SCIM 存取權杖。](media/Snowflake-provisioning-tutorial/image01.png)

3. 複製產生的權杖值，然後按一下 [完成]。 此值會在 Azure 入口網站中輸入至 Snowflake 應用程式的 [佈建] 索引標籤中的 [祕密權杖] 欄位。

    ![[詳細資料] 區段的螢幕擷取畫面，其中顯示複製到文字欄位中的權杖，和已標註的 [完成] 選項。](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式庫新增 Snowflake

從 Azure AD 應用程式庫新增 Snowflake，以開始管理對 Snowflake 的佈建。 如果您先前已針對 SSO 設定 Snowflake，則可使用相同的應用程式。 不過，建議您在一開始測試整合時，建立個別的應用程式。 [在此](../manage-apps/add-application-portal.md)深入了解從資源庫新增應用程式。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義將在佈建範圍內的人員 

Azure AD 佈建服務可供根據對應用程式的指派，或根據使用者/群組的屬性，界定將要佈建的人員。 如果您選擇根據指派來界定將佈建至應用程式的人員，您可以使用下列[步驟](../manage-apps/assign-user-or-group-access-portal.md)將使用者和群組指派給應用程式。 如果您選擇僅根據使用者或群組的屬性來界定將要佈建的人員，可以使用如[這裡](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的範圍篩選條件。 

* 將使用者和群組指派給 Snowflake 時，您必須選取 [預設存取] 以外的角色。 具有預設存取角色的使用者會從佈建中排除，而且會在佈建記錄中被標示為沒有效率。 如果應用程式上唯一可用的角色是 [預設存取] 角色，您可以[更新應用程式資訊清單](../develop/howto-add-app-roles-in-azure-ad-apps.md) \(部分機器翻譯\) 以新增其他角色。 

* 從小規模開始。 在推出給所有人之前，先使用一小部分的使用者和群組進行測試。 當佈建範圍設為已指派的使用者和群組時，您可將一或兩個使用者或群組指派給應用程式來控制這點。 當範圍設為所有使用者和群組時，您可指定[以屬性為基礎的範圍篩選條件](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>步驟 5。 對 Snowflake 設定自動使用者佈建 

本節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 Snowflake 中建立、更新和停用使用者和/或群組。

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>若要在 Azure AD 中為 Snowflake 設定自動使用者佈建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Snowflake]。

    ![應用程式清單中的 Snowflake 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![[管理] 選項的螢幕擷取畫面，並已指出 [佈建] 選項。](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![[佈建模式] 下拉式清單的螢幕擷取畫面，並已指出 [自動] 選項。](common/provisioning-automatic.png)

5. 在 [管理員認證] 區段底下，分別在 [租用戶 URL] 和 [祕密權杖] 欄位中輸入稍早擷取到的 **SCIM 2.0 基底 URL 和驗證權杖** 值。 按一下 [測試連線]，以確定 Azure AD 可以連線至 Snowflake。 如果連線失敗，請確定您的 Snowflake 帳戶具有管理員權限，然後再試一次。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

7. 在 [通知電子郵件]  欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]  核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

8. 按一下 [檔案]  。

9. 在 [對應] 區段下，選取 [將 Azure Active Directory 使用者同步至 Snowflake]。

10. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Snowflake 的使用者屬性。 選取為 [比對] 屬性 (Property) 的屬性 (Attribute)，會用來比對 Snowflake 中的使用者帳戶以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

   |屬性|類型|
   |---|---|
   |作用中|Boolean|
   |displayName|String|
   |emails[type eq "work"].value|String|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultRole|字串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultWarehouse|字串|

11. 在 [對應] 區段底下，選取 [將 Azure Active Directory 群組同步至 Snowflake]。

12. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Snowflake 的群組屬性。 選取為 [比對] 屬性 (Property) 的屬性 (Attribute)，會用來比對 Snowflake 中的群組以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

      |屬性|類型|
      |---|---|
      |displayName|String|
      |members|參考|

13. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

14. 若要啟用 Snowflake 的 Azure AD 佈建服務，請在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

15. 在 [設定] 區段的 [範圍] 中選擇所需的值，以定義要佈建至 Snowflake 的使用者和/或群組。 如果此選項無法使用，請設定 [管理員認證] 底下的必要欄位，然後按一下 [儲存] 並重新整理頁面。 

    ![佈建範圍](common/provisioning-scope.png)

16. 當您準備好要佈建時，按一下 [儲存]  。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

    此作業會對在 [設定]  區段的 [範圍]  中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
設定佈建後，請使用下列資源來監視您的部署：

1. 使用[佈建記錄](../reports-monitoring/concept-provisioning-logs.md) \(部分機器翻譯\) 來判斷哪些使用者已佈建成功或失敗
2. 檢查[進度列](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) \(部分機器翻譯\) 來查看佈建週期的狀態，以及其接近完成的程度
3. 如果佈建設定似乎處於狀況不良的狀態，應用程式將會進入隔離狀態。 [在此](../app-provisioning/application-provisioning-quarantine-status.md)深入了解隔離狀態。  

## <a name="connector-limitations"></a>連接器限制

* Snowflake 產生的 SCIM 權杖會在 6 個月後到期。 請注意，必須在這些權杖到期之前加以更新，佈建同步才能繼續運作。 

## <a name="change-log"></a>變更記錄

* 07/21/2020 - 為所有使用者啟用虛刪除 (透過作用中屬性)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)。
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟
* [了解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)。