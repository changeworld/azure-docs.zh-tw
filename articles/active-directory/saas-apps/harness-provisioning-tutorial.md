---
title: 教學課程：使用 Azure Active Directory 設定控管自動使用者布建 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory 自動布建和取消布建使用者帳戶以進行控管。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0cdb970b-440b-4e7c-9118-2f03baab6a20
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 518d86fff04a23f1c1e63c44c53485b99f30637d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77057805"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>教學課程：設定自動使用者布建的控制

在本文中，您將瞭解如何設定 Azure Active Directory （Azure AD），以自動布建和取消布建使用者或群組以進行控管。

> [!NOTE]
> 本文說明建立在 Azure AD 使用者布建服務之上的連接器。 如需這項服務的重要資訊和常見問題的解答，請參閱[使用 Azure Active Directory 自動化 SaaS 應用程式的使用者](../app-provisioning/user-provisioning.md)布建和解除布建。
>
> 此連接器目前為預覽版。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

本文中所述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [控管租使用者](https://harness.io/pricing/)
* 使用系統*管理員*許可權的使用者帳戶

## <a name="assign-users-to-harness"></a>將使用者指派給控管

Azure Active Directory 使用所謂「指派」的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動使用者布建的內容中，只有已指派給 Azure AD 中應用程式的使用者或群組會進行同步處理。

在您設定並啟用自動使用者布建之前，請決定 Azure AD 中的哪些使用者或群組需要存取控管。 接著，您可以依照[將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)中的指示，指派這些使用者或群組來進行控管。

## <a name="important-tips-for-assigning-users-to-harness"></a>將使用者指派給控管的重要秘訣

* 建議您將單一 Azure AD 使用者指派給控管，以測試自動使用者布建設定。 稍後可以指派其他使用者或群組。

* 當您將使用者指派給控管時，您必須在 [**指派**] 對話方塊中選取任何有效的應用程式特定角色（如果有的話）。 具有*預設存取*角色的使用者會從佈建中排除。

## <a name="set-up-harness-for-provisioning"></a>設定控管以進行布建

1. 登入您的控管[管理主控台](https://app.harness.io/#/login)，然後移至 [**持續安全性**  >  **存取管理**]。

    ![控制管理主控台](media/harness-provisioning-tutorial/admin.png)

1. 選取 [ **API 金鑰**]。

    ![控管 API 金鑰連結](media/harness-provisioning-tutorial/apikeys.png)

1. 選取 [**新增 API 金鑰**]。 

    ![控管新增 API 金鑰連結](media/harness-provisioning-tutorial/addkey.png)

1. 在 [**新增 Api 金鑰**] 窗格中，執行下列動作：

    ![控管新增 Api 金鑰窗格](media/harness-provisioning-tutorial/title.png)
   
   a. 在 [**名稱**] 方塊中，提供金鑰的名稱。  
   b. 在 [**繼承自的許可權**] 下拉式清單中，選取一個選項。 
   
1. 選取 [提交]。

1. 複製**金鑰**以供稍後在本教學課程中使用。

    ![控制建立權杖](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>從資源庫新增控管

在您設定控管以 Azure AD 自動布建使用者之前，您需要從 Azure AD 應用程式資源庫將控制項新增至受控 SaaS 應用程式清單。

1. 在 [Azure 入口網站](https://portal.azure.com)的左側窗格中，選取 [Azure Active Directory]****。

    ![[Azure Active Directory] 按鈕](common/select-azuread.png)

1. 選取 [**企業應用程式**] [  >  **所有應用程式**]。

    ![[所有應用程式] 連結](common/enterprise-applications.png)

1. 若要新增新的應用程式，請選取窗格頂端的 [**新增應用程式**] 按鈕。

    ![[新增應用程式] 按鈕](common/add-new-app.png)

1. 在搜尋方塊中，輸入控管，**選取結果清單中的 [** 控管 **]，然後**選取 [**新增**] 按鈕以新增應用程式。

    ![結果清單中的控管](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>設定自動使用者布建以控制 

本節會引導您完成設定 Azure AD 布建服務，以根據 Azure AD 中的使用者或群組指派來建立、更新和停用使用者或群組的步驟。

> [!TIP]
> 您也可以遵循控管[單一登入教學](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial)課程中的指示，選擇啟用控管的 SAML 型單一登入。 您可以設定與自動使用者布建無關的單一登入，雖然這兩個功能彼此互補。

> [!NOTE]
> 若要深入瞭解控管 SCIM 端點，請參閱控制[API 金鑰](https://docs.harness.io/article/smloyragsm-api-keys)一文。

若要在 Azure AD 中設定控管的自動使用者布建，請執行下列動作：

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，選取 [**企業應用程式**] [  >  **所有應用程式**]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

1. 在應用程式清單中，選取 [Harness]****。

    ![應用程式清單中的控管連結](common/all-applications.png)

1. 選取 **[** 布建]。

    ![[布建] 按鈕](common/provisioning.png)

1. 在 [布建**模式]** 下拉式清單中，選取 [**自動**]。

    ![[布建模式] 下拉式清單](common/provisioning-automatic.png)

1. 在 [**管理員認證**] 下，執行下列動作：

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)
 
   a. 在 [**租使用者 URL** ] 方塊中，輸入 **`https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw`** 。  
   b. 在 [**秘密權杖**] 方塊中，輸入您在「設定布建控管」一節的步驟6中所儲存的 SCIM Authentication Token 值。  
   c. 選取 [**測試連接**] 以確保 Azure AD 可以連接到控管。 如果連線失敗，請確定您的控管帳戶具有系統*管理員*許可權，然後再試一次。

1. 在 [**通知電子郵件**] 方塊中，輸入應收到布建錯誤通知之個人或群組的電子郵件地址，然後選取 [**發生失敗時傳送電子郵件通知**] 核取方塊。

    ![[通知電子郵件] 方塊](common/provisioning-notification-email.png)

1. 選取 [儲存]。

1. 在 **[** 對應] 底下，選取 [**同步處理 Azure Active Directory 使用者進行**控管]。

    ![控管「同步處理 Azure Active Directory 使用者以控管」連結](media/harness-provisioning-tutorial/usermappings.png)

1. 在 [**屬性**對應] 下，檢查從 Azure AD 同步處理到控管的使用者屬性。 選取*為 [* 比對] 的屬性會用來比對控管中的使用者帳戶以進行更新作業。 選取 [儲存] 認可任何變更。

    ![控制使用者的 [屬性對應] 窗格](media/harness-provisioning-tutorial/userattributes.png)

1. 在 **[** 對應] 底下，選取 [**同步處理 Azure Active Directory 群組**]。

    ![控管「同步處理 Azure Active Directory 群組以進行控管」連結](media/harness-provisioning-tutorial/groupmappings.png)

1. 在 [**屬性**對應] 底下，檢查從 Azure AD 同步處理到控管的群組屬性。 選取為 [比對] 屬性*的屬性會*用來比對控管中的群組以進行更新作業。 選取 [儲存] 認可任何變更。

    ![控制群組「屬性對應」窗格](media/harness-provisioning-tutorial/groupattributes.png)

1. 若要設定範圍篩選準則，請參閱[使用範圍篩選器進行以屬性為基礎的應用程式提供](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。

1. 在 [**設定**] 下，若要啟用用於控管的 Azure AD 布建服務，請將布建**狀態**切換切換為**開啟**。

    ![布建狀態切換已切換為 [開啟]](common/provisioning-toggle-on.png)

1. 在 [**設定**] 下的 [**範圍**] 下拉式清單中，選取您要如何同步處理您要布建的使用者或群組。

    ![佈建範圍](common/provisioning-scope.png)

1. 準備好要佈建時，請選取 [儲存]。

    ![[布建] [儲存] 按鈕](common/provisioning-configuration-save.png)

這項作業會啟動您要布建之使用者或群組的初始同步處理。 初始同步處理所花費的時間比更新版本更長。 只要有執行中的 Azure AD 布建服務，同步處理大約每40分鐘一次。 若要監視進度，請移至 [**同步處理詳細資料**] 區段。 您也可以遵循「布建活動報告」的連結，其中說明控管上的 Azure AD 布建服務所執行的所有動作。

如需如何讀取 Azure AD 布建記錄的詳細資訊，請參閱[關於自動使用者帳戶](../app-provisioning/check-status-user-account-provisioning.md)布建的報告。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
