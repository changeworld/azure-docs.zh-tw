---
title: 教學課程：使用 Azure Active Directory 設定 Contentful 來自動布建使用者
description: 瞭解如何從 Azure Active Directory (Azure AD) 將使用者帳戶自動布建和取消布建至 Contentful。
services: active-directory
documentationcenter: ''
author: zchia
manager: beatrizd
ms.assetid: 3b761984-a9a0-4519-b23e-563438978de5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2020
ms.author: zhchia
ms.openlocfilehash: c9d19624d90b1228b2a44caeff7d103af3172ed9
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516346"
---
# <a name="tutorial-configure-contentful-for-automatic-user-provisioning"></a>教學課程：設定 Contentful 來自動布建使用者

本文說明在 Contentful 和 Azure Active Directory (Azure AD) 設定自動使用者布建所需完成的步驟。 當設定時，Azure AD 會使用 Azure AD 布建服務，自動將使用者和群組布建及取消布建至 [Contentful](https://www.contentful.com/) 。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 自動對 SaaS 應用程式進行和取消使用者的佈建](../app-provisioning/user-provisioning.md)。 

## <a name="capabilities-supported"></a>支援的功能

> [!div class="checklist"]
> * 在 Contentful 中建立使用者
> * 當使用者不再需要存取權時，請移除 Contentful 中的使用者
> * Azure AD 與 Contentful 之間保持使用者屬性同步
> * 在 Contentful 中布建群組和群組成員資格
> * Contentful (建議的[單一登入](contentful-tutorial.md)) 

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租用戶](../develop/quickstart-create-new-tenant.md)。 
* Azure AD 中的使用者帳戶，具有設定布建 (的 [許可權](../roles/permissions-reference.md) ，例如，應用程式系統管理員、雲端應用程式系統管理員、應用程式擁有者或全域系統管理員) 。 
* 具有訂用帳戶的 Contentful 組織帳戶，可支援系統進行跨網域身分識別管理 (SCIM) 布建。 如果您有關于組織訂用帳戶的問題，請洽詢 [Contentful 支援](mailto:support@contentful.com)人員。
 
## <a name="plan-your-provisioning-deployment"></a>規劃佈建部署

1. 了解[佈建服務的運作方式](../app-provisioning/user-provisioning.md) \(部分機器翻譯\)。
2. 判斷誰會在[佈建範圍](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)內。
3. 判斷要 [在 Azure AD 與 Contentful 之間對應](../app-provisioning/customize-application-attributes.md)的資料。 

## <a name="configure-contentful-to-support-provisioning-with-azure-ad"></a>設定 Contentful 以支援 Azure AD 的布建

1. 在 Contentful 中，建立 **服務使用者** 帳戶。 您可以透過此帳戶提供 Azure 的所有布建許可權。 我們建議您選擇 [ **擁有** 者] 作為此帳戶的組織角色。

2. 以 **服務使用者** 的身份登入 Contentful。

3. 在左側功能表中，選取 [**組織設定**  >  **存取工具**  >  **使用者** 布建]。

   ![Contentful 中 [組織設定] 功能表的螢幕擷取畫面，其中已醒目提示 [存取工具] 底下的使用者布建。](media/contentful-provisioning-tutorial/access.png)

4. 複製並儲存 **SCIM URL**。 您會在 Contentful 應用程式的 [布建] 索引標籤上， **于 [Azure 入口網站** ] 中輸入此值。

5. 選取 [ **產生個人存取權杖**]。

    ![url](media/contentful-provisioning-tutorial/generate.png)

6. 在 [模式] 視窗中，輸入您個人存取權杖的名稱，然後選取 [ **產生**]。

7. 系統會產生 SCIM URL 和秘密權杖。 複製並儲存這些值。 您將在 Azure 入口網站中，于 Contentful 應用程式 **的 [布** 建] 索引標籤上輸入這些值。

    ![[個人存取權杖] 窗格的螢幕擷取畫面，其中的 C F P A T 和標記預留位置名稱已反白顯示。](media/contentful-provisioning-tutorial/token.png)


如果您在 Contentful 管理主控台中設定布建時遇到問題，請聯絡 [Contentful 支援](mailto:support@contentful.com)人員。

## <a name="add-contentful-from-the-azure-ad-application-gallery"></a>從 Azure AD 應用程式資源庫新增 Contentful

若要管理布建至 Contentful，請從 Azure AD 應用程式資源庫新增 Contentful。 如果您先前已設定單一登入的 Contentful，您可以使用相同的應用程式。 不過，我們建議您建立個別的應用程式，以一開始測試整合。 瞭解如何 [在資源庫中新增應用程式](../manage-apps/add-application-portal.md)。 

## <a name="define-who-will-be-in-scope-for-provisioning"></a>定義將在佈建範圍內的人員 

您可以使用 Azure AD 布建服務，以根據應用程式的指派，或根據使用者或群組的屬性來進行布建的範圍。 

如果您選擇根據指派將布建到您應用程式的範圍，請完成將 [使用者和群組指派給應用程式](../manage-apps/assign-user-or-group-access-portal.md)的步驟。

如果您選擇將僅以使用者或群組的屬性為基礎布建的物件範圍，請使用範圍篩選準則來定義布建 [使用者帳戶的條件式規則](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 

* 當您將使用者和群組指派給 Contentful 時，您必須選取 **預設存取** 以外的角色。 具有預設存取角色的使用者會從布建中排除，而且在布建記錄中會顯示為未提供有效的授權。 如果應用程式上唯一可用的角色是預設存取角色，您可以 [更新應用程式資訊清單](../develop/howto-add-app-roles-in-azure-ad-apps.md) ，以新增更多角色。 
* 從小規模開始。 在推出給所有人之前，先使用一小部分的使用者和群組進行測試。 當布建範圍設定為指派的使用者和群組時，您可以藉由將一或兩個使用者或群組指派給應用程式來控制範圍。 當範圍設定為 [所有使用者和群組] 時，您可以指定以 [屬性為基礎的範圍篩選準則](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 

## <a name="configure-automatic-user-provisioning-to-contentful"></a>設定自動使用者布建至 Contentful 

本節將引導您完成設定 Azure AD 布建服務，以根據 Azure AD 中的使用者或群組指派，在測試應用程式中建立、更新及停用使用者和群組的步驟。

### <a name="configure-automatic-user-provisioning-for-contentful-in-azure-ad"></a>在 Azure AD 中為 Contentful 設定自動使用者布建

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [ **企業應用程式**]，然後選取 [ **所有應用程式**]。

   ![顯示 Azure 入口網站中的 [企業應用程式] 功能表的螢幕擷取畫面，其中所有的應用程式都已反白顯示。](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Contentful]。

   ![螢幕擷取畫面，顯示應用程式清單中傳回的前20個結果。](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

   ![在左側功能表的 [管理] 區段中醒目提示 [布建] 索引標籤的螢幕擷取畫面。](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

   ![顯示 [布建模式] 選項的螢幕擷取畫面，並自動反白顯示。](common/provisioning-automatic.png)

5. 在 [ **管理員認證** ] 區段中，輸入您的 Contentful 租使用者 URL 和秘密權杖。 若要確保 Azure AD 可以連接到 Contentful，請選取 [ **測試連接**]。 如果連接失敗，請確定您的 Contentful 帳戶具有系統管理員許可權，然後再試一次。

   ![顯示租使用者 U R L 和秘密權杖文字方塊的螢幕擷取畫面，其中已醒目提示 [測試連接] 按鈕。](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [ **通知電子郵件**] 中，輸入應收到布建錯誤通知之個人或群組的電子郵件地址，然後選取 [ **發生失敗時傳送電子郵件通知** ] 核取方塊。

   ![顯示通知電子郵件文字方塊的螢幕擷取畫面。](common/provisioning-notification-email.png)

7. 選取 [儲存]  。

8. **在 [對應**] 區段中，選取 [**同步處理 Azure Active Directory 使用者至 Contentful**]。

9. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步至 Contentful 的使用者屬性。 選取為 [比對 **] 屬性的屬性會** 用來比對 Contentful 中的使用者帳戶以進行更新作業。 如果您選擇變更相符的 [目標屬性](../app-provisioning/customize-application-attributes.md)，您必須確定 Contentful API 支援根據該屬性篩選使用者。 選取 [儲存] 按鈕以認可所有變更。

   |屬性|類型|支援篩選|
   |---|---|---|
   |userName|String|&check;|
   |name.givenName|String|
   |name.familyName|String|

10. **在 [對應**] 區段中，選取 [**同步處理 Azure Active Directory 群組至 Contentful**]。

11. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步至 Contentful 的群組屬性。 選取為 [比對 **] 屬性的屬性會** 用來比對 Contentful 中的群組以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    |屬性|類型|支援篩選|
    |---|---|---|
    |displayName|String|&check;|
    |members|參考|

12. 若要設定範圍篩選準則，請完成 [範圍篩選器教學](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)課程中所述的步驟。

13. 若要啟用 Azure AD 布建服務以進行 Contentful，請在 [ **設定** ] 區段中，針對 [布建 **狀態**] 選取 [ **開啟**]。

    ![顯示布建狀態開啟和關閉切換的螢幕擷取畫面。](common/provisioning-toggle-on.png)

14. 若要定義您想要布建到 Contentful 的使用者或群組，請在 [ **設定** ] 區段的 [ **範圍**] 中，選取相關的選項。

    ![顯示您可以在 [領域] 窗格中選取之選項的螢幕擷取畫面。](common/provisioning-scope.png)

15. 準備好要佈建時，請選取 [儲存]。

    ![顯示 [儲存] 按鈕和 [取消] 按鈕的螢幕擷取畫面。](common/provisioning-configuration-save.png)

這項作業會啟動 [**設定**] 下的 [**範圍**] 中定義的所有使用者和群組的初始同步處理週期。 初始週期會比後續週期花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 

## <a name="monitor-your-deployment"></a>監視您的部署

設定布建之後，請使用下列資源來監視您的部署：

* 若要判斷哪些使用者已成功或未成功布建，請參閱布建 [記錄](../reports-monitoring/concept-provisioning-logs.md)。
* 若要查看布建週期的狀態以及完成的接近程度，請檢查 [進度](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)列。
* 如果布建設定似乎處於狀況不良的狀態，應用程式會進入隔離狀態。 深入瞭解 [隔離狀態](../app-provisioning/application-provisioning-quarantine-status.md)。  

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)
