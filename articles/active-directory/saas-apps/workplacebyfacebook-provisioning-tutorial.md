---
title: 教學課程︰以 Azure Active Directory 設定 Workplace by Facebook 來自動佈建使用者 | Microsoft Docs
description: 了解您在 Workplace by Facebook 與 Azure Active Directory (Azure AD) 中設定自動使用者佈建所需的步驟。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/28/2020
ms.author: jeedes
ms.openlocfilehash: e690a761efcd16428f9da8fe53e5dd8d26e9ff79
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181276"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>教學課程：設定 Workplace by Facebook 來自動佈建使用者

本教學課程描述需要在 Workplace by Facebook 與 Azure Active Directory (Azure AD) 中執行的步驟，以設定自動使用者佈建。 設定後，Azure AD 就會使用 Azure AD 佈建服務，自動對 [Workplace by Facebook](https://work.workplace.com/) 佈建及取消佈建使用者和群組。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。

## <a name="capabilities-supported"></a>支援的功能
> [!div class="checklist"]
> * 在 Workplace by Facebook 中建立使用者
> * 當使用者不再需要存取權時，將其從 Workplace by Facebook 中移除
> * 讓 Azure AD 與 Workplace by Facebook 之間的使用者屬性保持同步
> * [單一登入](./workplacebyfacebook-tutorial.md)至 Workplace by Facebook (建議選項)

>[!VIDEO https://www.youtube.com/embed/oF7I0jjCfrY]

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租用戶](../develop/quickstart-create-new-tenant.md) 
* Azure AD 中具有設定佈建[權限](../roles/permissions-reference.md)的使用者帳戶 (例如，應用程式管理員、雲端應用程式管理員、應用程式擁有者或全域管理員)
* 已啟用 Workplace by Facebook 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃佈建部署
1. 了解[佈建服務的運作方式](../app-provisioning/user-provisioning.md) \(部分機器翻譯\)。
2. 判斷誰會在[佈建範圍](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)內。
3. 判斷哪些資料要[在 Azure AD 與 Workplace by Facebook 之間對應](../app-provisioning/customize-application-attributes.md)。

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定 Workplace by Facebook 以支援使用 Azure AD 進行佈建

在設定並啟用佈建服務之前，您必須決定 Azure AD 中的哪些使用者及/或群組代表需要 Workplace by Facebook 應用程式存取權的使用者。 一旦決定後，您可以依照此處的指示，將這些使用者指派給 Workplace by Facebook 應用程式︰

*   建議將單一 Azure AD 使用者指派給 Workplace by Facebook，以測試佈建設定。 其他使用者及/或群組可能會稍後再指派。

*   將使用者指派給 Workplace by Facebook 時，必須選取有效的使用者角色。 「預設存取」角色不適用於佈建。

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式庫新增 Workplace by Facebook

從 Azure AD 應用程式庫新增 Workplace by Facebook，以開始管理對 Workplace by Facebook 的佈建。 如果您先前已針對 SSO 設定 Workplace by Facebook，則可使用相同的應用程式。 不過，建議您在一開始測試整合時，建立個別的應用程式。 [在此](../manage-apps/add-application-portal.md)深入了解從資源庫新增應用程式。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義將在佈建範圍內的人員 

Azure AD 佈建服務可供根據對應用程式的指派，或根據使用者/群組的屬性，界定將要佈建的人員。 如果您選擇根據指派來界定將佈建至應用程式的人員，您可以使用下列[步驟](../manage-apps/assign-user-or-group-access-portal.md)將使用者和群組指派給應用程式。 如果您選擇僅根據使用者或群組的屬性來界定將要佈建的人員，可以使用如[這裡](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的範圍篩選條件。 

* 將使用者和群組指派給 Workplace by Facebook 時，您必須選取 [預設存取] 以外的角色。 具有預設存取角色的使用者會從佈建中排除，而且會在佈建記錄中被標示為沒有效率。 如果應用程式上唯一可用的角色是 [預設存取] 角色，您可以[更新應用程式資訊清單](../develop/howto-add-app-roles-in-azure-ad-apps.md) \(部分機器翻譯\) 以新增其他角色。 

* 從小規模開始。 在推出給所有人之前，先使用一小部分的使用者和群組進行測試。 當佈建範圍設為已指派的使用者和群組時，您可將一或兩個使用者或群組指派給應用程式來控制這點。 當範圍設為所有使用者和群組時，您可指定[以屬性為基礎的範圍篩選條件](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Workplace by Facebook]。

    ![應用程式清單中的 Workplace by Facebook 連結](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![[管理] 選項的螢幕擷取畫面，並已指出 [佈建] 選項。](common/provisioning.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![[佈建模式] 下拉式清單的螢幕擷取畫面，並已指出 [自動] 選項。](common/provisioning-automatic.png)

5. 在 [管理員認證] 區段下，按一下 [授權]。 系統會將您重新導向至 Workplace by Facebook 的授權頁面。 輸入 Workplace by Facebook 使用者名稱，然後按一下 [繼續] 按鈕。 按一下 [測試連線]，以確保 Azure AD 可以連線至 Workplace by Facebook。 如果連線失敗，請確定您的 Workplace by Facebook 帳戶具有管理員權限並再試一次。

    ![螢幕擷取畫面：顯示 [管理員認證] 對話方塊與 [授權] 選項。](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![授權](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]。

8. 在 [對應] 區段下，選取 [將 Azure Active Directory 使用者同步至 Workplace by Facebook]。

9. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Workplace by Facebook 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 Workplace by Facebook 中的使用者帳戶以進行更新作業。 如果您選擇變更[比對目標屬性](../app-provisioning/customize-application-attributes.md)，則必須確保 Workplace by Facebook API 支援根據該屬性來篩選使用者。 選取 [儲存] 按鈕以認可所有變更。

   |屬性|類型|
   |---|---|
   |userName|String|
   |displayName|String|
   |作用中|Boolean|
   |title|布林值|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |addresses[type eq "work"].formatted|String|
   |addresses[type eq "work"].streetAddress|String|
   |addresses[type eq "work"].locality|String|
   |addresses[type eq "work"].region|String|
   |addresses[type eq "work"].country|String|
   |addresses[type eq "work"].postalCode|String|
   |addresses[type eq "other"].formatted|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |externalId|String|
   |preferredLanguage|String|
   |urn:scim:schemas:extension:enterprise:1.0.manager|String|
   |urn:scim:schemas:extension:enterprise:1.0.department|String|
   |urn:scim:schemas:extension:enterprise:1.0.division|String|
   |urn:scim:schemas:extension:enterprise:1.0.organization|String|
   |urn:scim:schemas:extension:enterprise:1.0.costCenter|String|
   |urn:scim:schemas:extension:enterprise:1.0.employeeNumber|String|
   |urn:scim:schemas:extension:facebook:auth_method:1.0:auth_method|字串|
   |urn:scim:schemas:extension:facebook:frontline:1.0.is_frontline|布林值|
   |urn:scim:schemas:extension:facebook:starttermdates:1.0.startDate|整數|


10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 若要啟用 Workplace by Facebook 的 Azure AD 佈建服務，在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 透過在 [設定] 區段的 [範圍] 中選擇需要的值，可定義要佈建到 Workplace by Facebook 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

13. 當您準備好要佈建時，按一下 [儲存]  。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中所定義所有使用者和群組啟動首次同步處理週期。 初始週期會比後續週期花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
設定佈建後，請使用下列資源來監視您的部署：

1. 使用[佈建記錄](../reports-monitoring/concept-provisioning-logs.md) \(部分機器翻譯\) 來判斷哪些使用者已佈建成功或失敗
2. 檢查[進度列](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) \(部分機器翻譯\) 來查看佈建週期的狀態，以及其接近完成的程度
3. 如果佈建設定似乎處於狀況不良的狀態，應用程式將會進入隔離狀態。 [在此](../app-provisioning/application-provisioning-quarantine-status.md)深入了解隔離狀態。

## <a name="troubleshooting-tips"></a>疑難排解秘訣
*  如果您看到使用者未成功建立，而且有一個代碼為 "1789003" 的稽核記錄事件，則表示使用者來自未驗證的網域。

## <a name="change-log"></a>變更記錄

* 2020/09/10 - 已新增對企業屬性 "division"、"organization"、"costCenter" 和 "employeeNumber" 的支援。 已新增對自訂屬性 "startDate"、"auth_method" 和 "frontline" 的支援

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)