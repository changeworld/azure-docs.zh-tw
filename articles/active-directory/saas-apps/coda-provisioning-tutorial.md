---
title: 教學課程：使用 Azure Active Directory 設定 Coda 來自動布建使用者 |Microsoft Docs
description: 瞭解如何從 Azure AD 將使用者帳戶自動布建和取消布建至 Coda。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2020
ms.author: Zhchia
ms.openlocfilehash: 95ee630c1190167227a4ab3514a0c36db0401e45
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273359"
---
# <a name="tutorial-configure-coda-for-automatic-user-provisioning"></a>教學課程：設定 Coda 來自動布建使用者

本教學課程說明在 Coda 和 Azure Active Directory (Azure AD) 設定自動使用者布建時，所需執行的步驟。 當設定時，Azure AD 會使用 Azure AD 布建服務，自動將使用者布建和取消布建至 [Coda](https://coda.io/) 。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。


## <a name="capabilities-supported"></a>支援的功能
> [!div class="checklist"]
> * 在 Coda 中建立使用者
> * 當 Coda 中的使用者不再需要存取權時，請將其移除
> * 在 Azure AD 和 Coda 之間保持使用者屬性同步
> * [單一登入](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial) Coda (建議的) 

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租用戶](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)
* Azure AD 中具有設定佈建[權限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的使用者帳戶 (例如，應用程式管理員、雲端應用程式管理員、應用程式擁有者或全域管理員)。
* [Coda 企業](https://help.coda.io/en/articles/3520174-getting-started-with-sso)系統管理員帳戶。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃佈建部署
1. 了解[佈建服務的運作方式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) \(部分機器翻譯\)。
2. 判斷誰會在[佈建範圍](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)內。
3. 判斷要 [在 Azure AD 和 Coda 之間對應](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的資料。

## <a name="step-2-configure-coda-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定 Coda 以支援 Azure AD 的布建

1. 選取 [...] 下的 [組織設定]，開啟您的組織管理主控台功能表下的功能表。

    ![Coda 企業組織 SCIM 設定](media/coda-provisioning-tutorial/coda-scim-enable.png)

2. 確定已啟用 [使用 SCIM 布建]。
3. 請注意 SCIM 基底 URL 和 SCIM 持有人權杖。 如果沒有持有人權杖，請按一下 [產生新的權杖]。

## <a name="step-3-add-coda-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式資源庫新增 Coda

從 Azure AD 應用程式資源庫新增 Coda，以開始管理 Coda 的布建。 如果您先前已設定 SSO 的 SSO，您可以使用相同的應用程式。 不過，建議您在一開始測試整合時，建立個別的應用程式。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)深入了解從資源庫新增應用程式。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義將在佈建範圍內的人員

Azure AD 布建服務可讓您根據應用程式的指派，或根據使用者的屬性，來界定將布建的人員。 如果您選擇根據指派將布建到應用程式的範圍，您可以使用下列 [步驟](../manage-apps/assign-user-or-group-access-portal.md) 將使用者指派給應用程式。 如果您選擇根據使用者的屬性來設定要布建的物件範圍，您可以使用 [這裡](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的範圍篩選器。

* 將使用者指派給 Coda 時，您必須選取 **預設存取**以外的角色。 具有預設存取角色的使用者會從佈建中排除，而且會在佈建記錄中被標示為沒有效率。 如果應用程式上唯一可用的角色是 [預設存取] 角色，您可以[更新應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) \(部分機器翻譯\) 以新增其他角色。

* 從小規模開始。 先測試一小部分的使用者，再將其推出給所有人。 當布建的範圍設定為指派的使用者時，您可以將一或兩個使用者指派給應用程式來控制此設定。 當範圍設定為 [所有使用者] 時，您可以指定以 [屬性為基礎的範圍篩選準則](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。


## <a name="step-5-configure-automatic-user-provisioning-to-coda"></a>步驟 5。 設定自動使用者布建至 Coda

本節將引導您完成設定 Azure AD 布建服務，以根據 Azure AD 中的使用者指派，在 TestApp 中建立、更新及停用使用者的步驟。

### <a name="to-configure-automatic-user-provisioning-for-coda-in-azure-ad"></a>若要為 Azure AD 中的 Coda 設定自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Coda]****。

    ![應用程式清單中的 Coda 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![已呼叫 [布建] 選項的 [管理選項] 螢幕擷取畫面。](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![[布建模式] 下拉式清單的螢幕擷取畫面，其中已呼叫 [自動] 選項。](common/provisioning-automatic.png)

5. 在 [ **管理員認證** ] 區段下，輸入您稍早在步驟2中取出的 Coda 租使用者 URL 和秘密權杖。 按一下 [ **測試連接** ] 以確保 Azure AD 可以連線至 Coda。 如果連接失敗，請確定您的 Coda 帳戶具有系統管理員許可權，然後再試一次。

    ![佈建](./media/coda-provisioning-tutorial/provisioning.png)

6. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者至 Coda**]。

9. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理至 Coda 的使用者屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對 Coda 中的使用者帳戶以進行更新作業。 如果您選擇變更相符的 [目標屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，您將必須確定 Coda API 支援根據該屬性篩選使用者。 選取 [儲存] 按鈕以認可所有變更。

   |屬性|類型|
   |---|---|
   |userName|String|
   |作用中|Boolean|
   |name.givenName|String|
   |name.familyName|String|


10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 若要啟用 Coda 的 Azure AD 布建服務，請在 [**設定**] 區段中將 [布建**狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建至 Coda 的使用者。

    ![佈建範圍](common/provisioning-scope.png)

13. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

這項作業會在 [**設定**] 區段中，針對 [**範圍**] 中定義的所有使用者啟動初始同步處理迴圈。 初始週期會比後續週期花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
設定佈建後，請使用下列資源來監視您的部署：

1. 使用[佈建記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) \(部分機器翻譯\) 來判斷哪些使用者已佈建成功或失敗
2. 檢查[進度列](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) \(部分機器翻譯\) 來查看佈建週期的狀態，以及其接近完成的程度
3. 如果佈建設定似乎處於狀況不良的狀態，應用程式將會進入隔離狀態。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)深入了解隔離狀態。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)
