---
title: 教學課程：使用 Azure Active Directory 設定 GitHub AE 來自動布建使用者 |Microsoft Docs
description: 瞭解如何從 Azure AD 將使用者帳戶自動布建和取消布建至 GitHub AE。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d9818c05-e279-45b4-8aad-0fa156abd74e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2020
ms.author: Zhchia
ms.openlocfilehash: 4e43ebba9f5f3d0c52d1d03bbf6baca92d5b87a4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96178682"
---
# <a name="tutorial-configure-github-ae-for-automatic-user-provisioning"></a>教學課程：設定自動使用者布建的 GitHub AE

本教學課程說明在 GitHub AE 和 Azure Active Directory (Azure AD) 設定自動使用者布建時所需執行的步驟。 當設定時，Azure AD 會使用 Azure AD 布建服務，自動將使用者和/或群組布建並取消布建至 GitHub AE。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支援的功能
> [!div class="checklist"]
> * 在 GitHub AE 中建立使用者
> * 不再需要存取權時，請移除 GitHub AE 中的使用者
> * 在 Azure AD 與 GitHub AE 之間保持使用者屬性同步
> * 單一登入 [GITHUB AE](./github-ae-tutorial.md) (建議的) 

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租用戶](../develop/quickstart-create-new-tenant.md) 
* Azure AD 中具有設定佈建[權限](../roles/permissions-reference.md)的使用者帳戶 (例如，應用程式管理員、雲端應用程式管理員、應用程式擁有者或全域管理員)。 
* 透過您的 Azure AD 租使用者，以[SAML SSO](https://docs.github.com/github-ae@latest/admin/authentication/configuring-authentication-and-provisioning-for-your-enterprise-using-azure-ad)登入的 GitHub AE、完全[初始化](https://docs.github.com/github-ae@latest/admin/configuration/initializing-github-ae)和設定。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃佈建部署
1. 了解[佈建服務的運作方式](../app-provisioning/user-provisioning.md) \(部分機器翻譯\)。
2. 判斷誰會在[佈建範圍](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)內。
3. 判斷要 [在 Azure AD 與 GITHUB AE 之間對應](../app-provisioning/customize-application-attributes.md)的資料。 

## <a name="step-2-configure-github-ae-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定 GitHub AE 以支援 Azure AD 的布建

瞭解如何 [在此](https://docs.github.com/github-ae@latest/admin/authentication/configuring-user-provisioning-for-your-enterprise)啟用 GitHub AE 的布建。

## <a name="step-3-add-github-ae-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式資源庫新增 GitHub AE

從 Azure AD 應用程式資源庫新增 GitHub AE，以開始管理布建至 GitHub AE。 如果您先前已設定 GitHub AE 以進行 SSO，您可以使用相同的應用程式。 不過，建議您在一開始測試整合時，建立個別的應用程式。 [在此](../manage-apps/add-application-portal.md)深入了解從資源庫新增應用程式。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義將在佈建範圍內的人員 

Azure AD 布建服務可讓您根據應用程式的指派，或根據使用者及/或群組的屬性，來界定將布建的人員。 如果您選擇根據指派將布建到應用程式的範圍，您可以使用下列 [步驟](../manage-apps/assign-user-or-group-access-portal.md) 將使用者和/或群組指派給應用程式。 如果您選擇根據使用者及/或群組的屬性來設定要布建的物件，您可以使用 [這裡](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的範圍篩選器。 

* 將使用者指派給 GitHub AE 時，您必須選取 **預設存取** 以外的角色。 具有預設存取角色的使用者會從佈建中排除，而且會在佈建記錄中被標示為沒有效率。 如果應用程式上唯一可用的角色是 [預設存取] 角色，您可以[更新應用程式資訊清單](../develop/howto-add-app-roles-in-azure-ad-apps.md) \(部分機器翻譯\) 以新增其他角色。 

* 從小規模開始。 先測試一小部分的使用者和/或群組，再向所有人推出。 當布建的範圍設定為指派的使用者和/或群組時，您可以藉由將一或兩個使用者和/或群組指派給應用程式來控制此設定。 當範圍設為所有使用者和群組時，您可指定[以屬性為基礎的範圍篩選條件](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 


## <a name="step-5-configure-automatic-user-provisioning-to-github-ae"></a>步驟 5。 設定自動使用者布建至 GitHub AE 

此節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 TestApp 中建立、更新和停用使用者和/或群組。

### <a name="to-configure-automatic-user-provisioning-for-github-ae-in-azure-ad"></a>若要在 Azure AD 中設定 GitHub AE 的自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [GitHub AE]。

    ![應用程式清單中的 GitHub AE 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![佈建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![佈建索引標籤 [自動]](common/provisioning-automatic.png)

5. 在 [系統 **管理員認證** ] 區段下，輸入您先前從步驟2取出的 GitHub AE **租使用者 URL** 和 **秘密權杖** 。 按一下 [ **測試連接** ]，以確保 Azure AD 可以連線至 GitHub AE。 如果連接失敗，請確定您的 GitHub AE 帳戶具有系統管理員許可權，然後再試一次。

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者** 至 **GitHub AE**]。

9. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步至 GitHub AE 的使用者屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對 GitHub AE 中的使用者帳戶以進行更新作業。 如果您選擇變更相符的 [目標屬性](../app-provisioning/customize-application-attributes.md)，您將需要確定 GITHUB AE API 支援根據該屬性篩選使用者。 選取 [儲存] 按鈕以認可所有變更。

   |屬性|類型|
   |---|---|
   |userName|String|
   |externalId|String|
   |emails[type eq "work"].value|String|
   |作用中|Boolean|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |displayName|String|

10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 若要啟用 GitHub AE 的 Azure AD 布建服務，請在 [**設定**] 區段中，將 [布建 **狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建至 GitHub AE 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

13. 當您準備好要佈建時，按一下 [儲存]  。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

這項作業會在 [**設定**] 區段中，針對 [**範圍**] 中定義的所有使用者和/或群組，啟動首次同步處理迴圈。 初始週期會比後續週期花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 

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