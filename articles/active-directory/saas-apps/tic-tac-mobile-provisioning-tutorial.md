---
title: 教學課程：以 Azure Active Directory 設定 Tic-Tac Mobile 來自動佈建使用者 | Microsoft Docs
description: 了解如何將使用者帳戶從 Azure AD 針對 Tic-Tac Mobile 進行自動佈建和取消佈建。
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
ms.openlocfilehash: a09594d1bc6037f252ba71855ae302208b4980d4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182209"
---
# <a name="tutorial-configure-tic-tac-mobile-for-automatic-user-provisioning"></a>教學課程：設定 Tic-Tac Mobile 來自動佈建使用者

此教學課程說明您在 Tic-Tac Mobile 與 Azure Active Directory (Azure AD) 中設定自動使用者佈建所需的步驟。 設定後，Azure AD 就會使用 Azure AD 佈建服務，自動對 [Tic-Tac Mobile](https://www.tictacmobile.com/) 佈建及取消佈建使用者和群組。 如需此服務的用途、運作方式和常見問題集的詳細資訊，請參閱[使用 Azure AD 對軟體即服務 (SaaS) 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。


## <a name="capabilities-supported"></a>支援的功能

> [!div class="checklist"]
> * 在 Tic-Tac Mobile 中建立使用者。
> * 當使用者不再需要存取時，將其從 Tic-Tac Mobile 中移除。
> * 讓 Azure AD 與 Tic-Tac Mobile 之間的使用者屬性保持同步。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租用戶](../develop/quickstart-create-new-tenant.md)。
* Azure AD 中具有[權限](../roles/permissions-reference.md)可設定佈建的使用者帳戶。 範例包括應用程式系統管理員、雲端應用程式系統管理員、應用程式擁有者或全域管理員。
* 具有超級管理員角色的 [Tic-Tac Mobile](https://www.tictacmobile.com/) 帳戶。


## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃佈建部署

1. 了解[佈建服務的運作方式](../app-provisioning/user-provisioning.md) \(部分機器翻譯\)。
1. 判斷誰會在[佈建範圍](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)內。
1. 判斷哪些資料要[在 Azure AD 與 Tic-Tac Mobile 之間對應](../app-provisioning/customize-application-attributes.md)。

## <a name="step-2-configure-tic-tac-mobile-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定 Tic-Tac Mobile 以支援使用 Azure AD 進行佈建

請連絡 support@tictacmobile.com 以取得 **租用戶 URL** 和 **秘密權杖**。 您必須具有 Tic-Tac Mobile 的超級管理員角色，才能接收權杖。 在 Azure 入口網站中，此權杖會輸入至 Tic-Tac Mobile 應用程式的 [佈建] 索引標籤上的 [秘密權杖] 方塊中。

## <a name="step-3-add-tic-tac-mobile-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式庫新增 Tic-Tac Mobile

從 Azure AD 應用程式庫新增 Tic-Tac Mobile，以開始管理對 Tic-Tac Mobile 的佈建。 如果您先前已設定 Tic-Tac Mobile 的單一登入，則可以使用相同的應用程式。 當您最初測試整合時，請建立個別的應用程式。 若要深入了解如何從資源庫新增應用程式，請參閱[含範圍篩選器的屬性型應用程式佈建](../manage-apps/add-application-portal.md)。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義將在佈建範圍內的人員

透過 Azure AD 佈建服務，您可以根據對應用程式的指派，或根據使用者或群組的屬性，界定將要佈建的人員。 如果您選擇根據指派界定要佈建至應用程式的人員，請依照[在 Azure Active Directory 中管理應用程式的使用者指派](../manage-apps/assign-user-or-group-access-portal.md)中的步驟，將使用者和群組指派給應用程式。 如果您選擇僅根據使用者或群組的屬性來界定要佈建的人員，請使用範圍篩選器，如[使用範圍篩選器的屬性型應用程式佈建](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所說明。

* 將使用者和群組指派給 Tic-Tac Mobile 時，您必須選取 [預設存取] 以外的角色。 具有預設存取角色的使用者會從佈建中排除，且會在佈建記錄中標示為不具有效授權。 如果應用程式上唯一可用的角色是預設存取角色，您可以[更新應用程式資訊清單](../develop/howto-add-app-roles-in-azure-ad-apps.md)以新增更多角色。
* 從小規模開始。 在推出給所有人之前，先使用一小部分的使用者和群組進行測試。 當佈建範圍設為已指派的使用者和群組時，您可將一或兩個使用者或群組指派給應用程式加以控制。 當範圍設為所有使用者和群組時，您可以指定[屬性型範圍篩選器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。

## <a name="step-5-configure-automatic-user-provisioning-to-tic-tac-mobile"></a>步驟 5。 設定使用者自動佈建至 Tic-Tac Mobile

本節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者或群組指派，在 TestApp 中建立、更新和停用使用者或群組。

### <a name="configure-automatic-user-provisioning-for-tic-tac-mobile-in-azure-ad"></a>在 Azure AD 中為 Tic-Tac Mobile 設定自動使用者佈建

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]   > [所有應用程式]  。

    ![顯示 [企業應用程式] 窗格的螢幕擷取畫面。](common/enterprise-applications.png)

1. 在應用程式清單中，選取 [Tic-Tac Mobile]。

    ![在應用程式清單中顯示 Tic-Tac Mobile 連結的螢幕擷取畫面。](common/all-applications.png)

1. 選取 [佈建]  索引標籤。

    ![顯示 [佈建] 索引標籤的螢幕擷取畫面。](common/provisioning.png)

1. 將 [佈建模式] 設定為 [自動]。

    ![顯示 [佈建] 索引標籤 [自動] 選項的螢幕擷取畫面。](common/provisioning-automatic.png)

1. 在 [管理員認證] 區段底下，輸入您的 Tic-Tac Mobile [租用戶 URL] 和 [秘密權杖]。 選取 [測試連線]，以確定 Azure AD 可連線至 Tic-Tac Mobile。 如果連線失敗，請確定您的 Tic-Tac Mobile 帳戶具有管理員權限，然後再試一次。

    ![顯示 [秘密權杖] 方塊的螢幕擷取畫面。](common/provisioning-testconnection-tenanturltoken.png)

1. 在 [通知電子郵件] 方塊中，輸入應收到佈建錯誤通知的個人或群組電子郵件地址。 選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![顯示 [通知電子郵件] 方塊的螢幕擷取畫面。](common/provisioning-notification-email.png)

1. 選取 [儲存]。

1. 在 [對應] 區段中，選取 [將 Azure Active Directory 使用者同步至 Tic-Tac Mobile]。

1. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步處理至 Tic-Tac Mobile 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 Tic-Tac Mobile 中的使用者帳戶，以進行更新作業。 如果您變更[比對目標屬性](../app-provisioning/customize-application-attributes.md)，則必須確定 Tic-Tac Mobile API 支援根據該屬性來篩選使用者。 選取 [儲存] 按鈕以認可所有變更。

   |屬性|類型|
   |---|---|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |title|String|
   |emails[type eq"work"].value|String|
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

1. 若要設定範圍篩選條件，請參閱[範圍篩選器教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的指示。

1. 若要啟用 Tic-Tac Mobile 的 Azure AD 佈建服務，請在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

    ![顯示將 [佈建狀態] 切換為 [開啟] 的螢幕擷取畫面。](common/provisioning-toggle-on.png)

1. 藉由在 [設定] 區段的 [範圍] 中選取所需的值，定義要佈建至 Tic-Tac Mobile 的使用者或群組。

    ![顯示佈建範圍的螢幕擷取畫面。](common/provisioning-scope.png)

1. 準備好要佈建時，請選取 [儲存]。

    ![顯示儲存佈建設定的螢幕擷取畫面。](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中所定義所有使用者和群組啟動首次同步處理週期。 初始週期會比後續週期花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署

設定佈建後，請使用下列資源來監視部署。

1. 使用[佈建記錄](../reports-monitoring/concept-provisioning-logs.md)來判斷哪些使用者已佈建成功或失敗。
1. 檢查[進度列](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)來查看佈建週期的狀態，以及其接近完成的程度。
1. 如果佈建設定似乎處於狀況不良的狀態，應用程式將會進入隔離狀態。 若要深入了解隔離狀態，請參閱[應用程式佈建處於隔離狀態](../app-provisioning/application-provisioning-quarantine-status.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)