---
title: 教學課程：使用 Azure Active Directory 設定 Apple Business Manager 來自動布建使用者 |Microsoft Docs
description: 瞭解如何從 Azure AD 將使用者帳戶自動布建和取消布建至 Apple Business Manager。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 4ad30031-9904-4ac3-a4d2-e8c28d44f319
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2020
ms.author: Zhchia
ms.openlocfilehash: c4ac4a17e577ea69f4359e1e9b321e5fcc839697
ms.sourcegitcommit: 4ce82b6df65ebd81157b6168d3aa4e7323355022
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2020
ms.locfileid: "90761478"
---
# <a name="tutorial-configure-apple-business-manager-for-automatic-user-provisioning"></a>教學課程：設定 Apple Business Manager 以自動布建使用者



本教學課程說明在 Apple Business Manager 和 Azure Active Directory (Azure AD) 設定自動使用者布建時所需執行的步驟。 當設定時，Azure AD 會使用 Azure AD 布建服務，自動將使用者布建和取消布建至 [Apple Business Manager](https://business.apple.com/) 。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。 

## <a name="capabilities-supported"></a>支援的功能
> [!div class="checklist"]
> * 在 Apple Business Manager 中建立使用者
> * 不再需要存取權時，請移除 Apple Business Manager 中的使用者
> * 在 Azure AD 與 Apple Business Manager 之間保持使用者屬性同步

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 的租](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)使用者。
* Azure AD 中具有設定佈建[權限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的使用者帳戶 (例如，應用程式管理員、雲端應用程式管理員、應用程式擁有者或全域管理員)。
* 具有 [系統管理員] 或 [人員管理員] 角色的 Apple Business Manager 帳戶。

> [!NOTE]
> 必須在4個行事曆天內完成權杖傳送，才能 Azure AD 及建立成功的連線，否則必須重新開機此程式。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃佈建部署
1. 了解[佈建服務的運作方式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) \(部分機器翻譯\)。
2. 判斷誰會在[佈建範圍](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)內。
3. 判斷要 [在 Azure AD 與 Apple Business Manager 之間對應](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的資料。 

## <a name="step-2-configure-apple-business-manager-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定 Apple Business Manager 以支援 Azure AD 的布建

1. 在 Apple Business Manager 中，使用具有 [系統管理員] 或 [人員經理] 角色的帳戶登入。
2. 按一下側邊欄底部的 [設定]，按一下 [組織設定] 下方的 [資料來源]，然後按一下 [連接到資料來源]。
3. 按一下 [SCIM] 旁的 [連接]，仔細閱讀警告，按一下 [複製]，然後按一下 [關閉]。
[連接到 SCIM] 視窗，它會在其下提供權杖和複製按鈕。]讓此視窗保持開啟，以將租使用者 URL 從 Apple Business Manager 複製到 Azure AD，也就是： https://federation.apple.com/feeds/business/scim

    ![Apple Business Manager](media/applebusinessmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> 秘密權杖不應與 Azure AD 系統管理員以外的任何人共用。

## <a name="step-3-add-apple-business-manager-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式資源庫新增 Apple Business Manager

從 Azure AD 應用程式資源庫新增 Apple Business Manager，以開始管理 Apple Business Manager 的布建。 如果您先前已設定「適用于 SSO 的 Apple 商務經理」，則可以使用相同的應用程式。 不過，建議您在一開始測試整合時，建立個別的應用程式。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)深入了解從資源庫新增應用程式。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義將在佈建範圍內的人員 

Azure AD 佈建服務可供根據對應用程式的指派，或根據使用者/群組的屬性，界定將要佈建的人員。 如果您選擇根據指派來界定將佈建至應用程式的人員，您可以使用下列[步驟](../manage-apps/assign-user-or-group-access-portal.md)將使用者和群組指派給應用程式。 如果您選擇僅根據使用者或群組的屬性來界定將要佈建的人員，可以使用如[這裡](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的範圍篩選條件。 

* 將使用者指派給 Apple Business Manager 時，您必須選取 **預設存取**以外的角色。 具有預設存取角色的使用者會從佈建中排除，而且會在佈建記錄中被標示為沒有效率。 如果應用程式上唯一可用的角色是 [預設存取] 角色，您可以[更新應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) \(部分機器翻譯\) 以新增其他角色。 

* 從小規模開始。 在推出給所有人之前，先使用一小部分的使用者和群組進行測試。 當佈建範圍設為已指派的使用者和群組時，您可將一或兩個使用者或群組指派給應用程式來控制這點。 當範圍設為所有使用者和群組時，您可指定[以屬性為基礎的範圍篩選條件](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-business-manager"></a>步驟 5。 設定自動使用者布建至 Apple Business Manager

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [ **Apple Business Manager**]。

    ![應用程式清單中的 Apple Business Manager](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![佈建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![自動布建索引標籤](common/provisioning-automatic.png)

5. 在 [系統**管理員認證**] 區段下，輸入分別從**租使用者 URL**和**秘密權杖**中的 Apple Business Manager 取出的**SCIM 2.0 基底 url 和存取權杖**值。 按一下 [ **測試連接** ]，以確保 Azure AD 可以連接至 Apple Business Manager。 如果連接失敗，請確定您的 Apple Business Manager 帳戶具有系統管理員許可權，然後再試一次。

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>如果連接成功，Apple Business Manager 會將 SCIM 連接顯示為使用中。 此程式最多可能需要60秒，Apple Business Manager 才能反映最新的連接狀態。

6. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [儲存]。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者至 Apple Business Manager**]。

9. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步至 Apple Business Manager 的使用者屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對 Apple Business Manager 中的使用者帳戶，以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

   |屬性|類型|
   |---|---|
   |作用中|Boolean|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.givenName|String|
   |externalId|String|
   |地區設定|String|
   |timezone|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 若要啟用 Apple Business Manager 的 Azure AD 布建服務，請在 [設定] 區段中，將 [布建 **狀態** ] 變更為 [ **開啟** ]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建至 Apple Business Manager 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

13. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
設定佈建後，請使用下列資源來監視您的部署：

1. 使用[佈建記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) \(部分機器翻譯\) 來判斷哪些使用者已佈建成功或失敗
2. 檢查[進度列](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) \(部分機器翻譯\) 來查看佈建週期的狀態，以及其接近完成的程度
3. 如果佈建設定似乎處於狀況不良的狀態，應用程式將會進入隔離狀態。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) \(部分機器翻譯\) 深入了解隔離狀態。  

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)
* [審查 Apple Business Manager 的 SCIM 需求](URL=https://support.apple.com/guide/apple-business-manager/apdd88331cd6)
* [如何在 Apple Business Manager 中使用 Person 識別碼](URL=https://support.apple.com/guide/apple-business-manager/apd69e1e48e9)
* [使用 SCIM 將使用者匯入至 Apple Business Manager](URL=https://support.apple.com/guide/apple-business-manager/apd3ec7b95ad)
* [解決 Apple Business Manager 中的 SCIM 使用者帳戶衝突](URL=https://support.apple.com/guide/apple-business-manager/apd313013d12)
* [刪除出現在 Apple Business Manager Azure AD 帳戶](URL=https://support.apple.com/guide/apple-business-manager/apdaa5798fbe)
* [在 Apple Business Manager 中查看 SCIM 活動](URL=https://support.apple.com/guide/apple-business-manager/apd1bfd8dfde)
* [在 Apple Business Manager 中管理現有的 SCIM token 和連接](URL=https://support.apple.com/guide/apple-business-manager/apdc9a823611)
* [中斷 Apple Business Manager](URL=https://support.apple.com/guide/apple-business-manager/apd609be3a61) *  中的 SCIM 連接[在 Apple Business Manager 中管理現有的 SCIM token 和連接](URL=https://support.apple.com/guide/apple-business-manager/apdc9a8236e9)
* [在 Apple Business Manager 中針對 SCIM 連接進行疑難排解](URL=https://support.apple.com/guide/apple-business-manager/apd403a0f3bd)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)

