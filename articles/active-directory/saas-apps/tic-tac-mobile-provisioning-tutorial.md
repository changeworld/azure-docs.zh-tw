---
title: 教學課程：設定 Tic-Tac Mobile 以使用 Azure Active Directory 自動布建使用者 |Microsoft Docs
description: 瞭解如何從 Azure AD 將使用者帳戶自動布建和取消布建 Tic-Tac 行動裝置。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d0f24e81-fecf-4e71-bd8a-ab911366fdf5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: Zhchia
ms.openlocfilehash: 99fe2f8356b53b5d8e459dbf62534ddb4f0019b2
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357261"
---
# <a name="tutorial-configure-tic-tac-mobile-for-automatic-user-provisioning"></a>教學課程：設定 Tic-Tac 行動裝置來自動布建使用者

本教學課程說明您需要在 Tic-Tac 行動裝置和 Azure Active Directory (Azure AD) 中執行的步驟，以設定自動使用者布建。 當設定時，Azure AD 使用 Azure AD 布建服務，自動將使用者和群組布建及取消布建至 [Tic 的](https://www.tictacmobile.com/) 行動裝置。 如需此服務的用途、運作方式和常見問題的詳細資訊，請參閱 [使用 Azure AD 自動將使用者布建和解除布建至軟體即服務 (SaaS) 應用程式](../manage-apps/user-provisioning.md)。


## <a name="capabilities-supported"></a>支援的功能

> [!div class="checklist"]
> * 在 Tic-Tac Mobile 中建立使用者。
> * 當使用者不再需要存取權時，請移除 Tic-Tac Mobile 中的使用者。
> * Azure AD 與 Tic-Tac 行動裝置之間保持使用者屬性同步。

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租用戶](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)。
* Azure AD 中具有設定布建之 [許可權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 的使用者帳戶。 範例有應用程式系統管理員、雲端應用程式系統管理員、應用程式擁有者或全域管理員。
* 具有超級管理員角色的 [Tic 式](https://www.tictacmobile.com/) 行動帳戶。


## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃佈建部署

1. 了解[佈建服務的運作方式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) \(部分機器翻譯\)。
1. 判斷誰會在[佈建範圍](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)內。
1. 判斷要 [在 Azure AD 與 Tic-Tac Mobile 之間對應](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的資料。

## <a name="step-2-configure-tic-tac-mobile-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定 Tic-Tac Mobile 以支援以 Azure AD 布建

support@tictacmobile.com取得您的 **租使用者 URL** 和 **秘密權杖** 的連絡人。 您必須具備 Tic-Tac Mobile 的超級管理員角色，才能接收權杖。 權杖將會在 Azure 入口網站的 Tic-Tac 行動應用程式的 [布 **建] 索引** 標籤上的 [ **秘密權杖** ] 方塊中輸入。

## <a name="step-3-add-tic-tac-mobile-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式庫新增 Tic-Tac Mobile

從 Azure AD 應用程式資源庫新增 Tic-Tac 行動裝置，以開始管理 Tic-Tac 行動裝置的布建。 如果您先前已設定 Tic-Tac Mobile 以進行單一登入，您可以使用相同的應用程式。 當您一開始測試整合時，請建立個別的應用程式。 若要深入瞭解如何從資源庫新增應用程式，請參閱 [使用範圍篩選器進行以屬性為基礎的應用程式布建](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義將在佈建範圍內的人員

使用 Azure AD 布建服務時，您可以根據應用程式的指派，或根據使用者或群組的屬性，來界定將布建的人員。 如果您選擇根據指派將布建到您應用程式的範圍，請遵循在 Azure Active Directory 中 [管理應用程式的使用者指派中](../manage-apps/assign-user-or-group-access-portal.md) 的步驟，將使用者和群組指派給應用程式。 如果您選擇根據使用者或群組的屬性來設定要布建的物件，請使用範圍篩選器，如以 [屬性為基礎的應用程式布建與範圍篩選器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)中所述。

* 當您指派使用者和群組給 Tic-Tac 的行動裝置時，您必須選取 **預設存取** 以外的角色。 具有預設存取角色的使用者會從布建中排除，而且將在布建記錄中標示為未有效的許可權。 如果應用程式上唯一可用的角色是預設存取角色，您可以 [更新應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) ，以新增更多角色。
* 從小規模開始。 先測試一小部分的使用者和群組，再向所有人推出。 當布建的範圍設定為指派的使用者和群組時，您可以藉由將一或兩個使用者或群組指派給應用程式來維護控制。 當範圍設定為 [所有使用者和群組] 時，您可以指定以 [屬性為基礎的範圍篩選準則](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。

## <a name="step-5-configure-automatic-user-provisioning-to-tic-tac-mobile"></a>步驟 5。 設定自動使用者布建至 Tic-Tac Mobile

本節將引導您完成設定 Azure AD 布建服務，以根據 Azure AD 中的使用者或群組指派，在 TestApp 中建立、更新及停用使用者或群組的步驟。

### <a name="configure-automatic-user-provisioning-for-tic-tac-mobile-in-azure-ad"></a>在 Azure AD 中設定 Tic-Tac Mobile 的自動使用者布建

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]   > [所有應用程式]  。

    ![顯示 [企業應用程式] 窗格的螢幕擷取畫面。](common/enterprise-applications.png)

1. 在應用程式清單中，選取 [ **Tic** ]。

    ![在應用程式清單中顯示 Tic-Tac Mobile] 連結的螢幕擷取畫面。](common/all-applications.png)

1. 選取 [佈建]  索引標籤。

    ![顯示 [布建] 索引標籤的螢幕擷取畫面。](common/provisioning.png)

1. 將 [佈建模式] 設定為 [自動]。

    ![顯示 [布建] 索引標籤自動選項的螢幕擷取畫面。](common/provisioning-automatic.png)

1. 在 [系統 **管理員認證** ] 區段下，輸入您的 Tic-Tac 行動 **租使用者 URL** 和 **秘密權杖** 。 選取 [ **測試連接** ] 以確保 Azure AD 可以連線至 Tic-Tac 行動裝置。 如果連線失敗，請確定您的 Tic-Tac 行動帳戶具有系統管理員許可權，然後再試一次。

    ![顯示 [秘密權杖] 方塊的螢幕擷取畫面。](common/provisioning-testconnection-tenanturltoken.png)

1. 在 [ **通知電子郵件** ] 方塊中，輸入應收到布建錯誤通知之個人或群組的電子郵件地址。 選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![顯示 [通知電子郵件] 方塊的螢幕擷取畫面。](common/provisioning-notification-email.png)

1. 選取 [儲存]。

1. **在 [對應** ] 區段下，選取 [ **同步處理 Azure Active Directory 使用者] 以 Tic-Tac** 行動裝置。

1. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理至 Tic-Tac 行動裝置的使用者屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對 Tic-Tac Mobile 中的使用者帳戶以進行更新作業。 如果您變更 [相符的目標屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，您必須確定 Tic-Tac Mobile API 支援根據該屬性篩選使用者。 選取 [儲存] 按鈕以認可所有變更。

   |屬性|類型|
   |---|---|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |title|String|
   |電子郵件 [type eq "work"]。值|String|
   |preferredLanguage|String|
   |externalId|String|
   |userType|String|
   |地區設定|String|
   |timezone|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

1. 若要設定範圍篩選器，請參閱 [範圍篩選器教學](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)課程中的指示。

1. 若要啟用 Tic-Tac Mobile 的 Azure AD 布建服務，請在 [ **設定** ] **區段中，將 [** 布建 **狀態** ] 變更為。

    ![顯示已切換的布建狀態的螢幕擷取畫面。](common/provisioning-toggle-on.png)

1. 在 [ **設定** ] 區段的 [ **範圍** ] 中選取所需的值，以定義您想要布建至 Tic-Tac 行動裝置的使用者或群組。

    ![顯示布建範圍的螢幕擷取畫面。](common/provisioning-scope.png)

1. 準備好要佈建時，請選取 [儲存]。

    ![顯示儲存布建設定的螢幕擷取畫面。](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中所定義所有使用者和群組啟動首次同步處理週期。 初始週期會比後續週期花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署

設定布建之後，請使用下列資源來監視您的部署。

1. 使用[佈建記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)來判斷哪些使用者已佈建成功或失敗。
1. 檢查[進度列](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)來查看佈建週期的狀態，以及其接近完成的程度。
1. 如果佈建設定似乎處於狀況不良的狀態，應用程式將會進入隔離狀態。 若要深入瞭解隔離狀態，請參閱 [隔離狀態中的應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)布建。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)
