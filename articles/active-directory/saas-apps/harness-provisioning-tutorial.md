---
title: 教程：使用 Azure 活動目錄配置用於自動使用者預配的線束 |微軟文檔
description: 瞭解如何將 Azure 活動目錄配置為自動預配和取消配置使用者帳戶以使用工具。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057805"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>教程：為自動使用者預配配置線束

在本文中，您將瞭解如何配置 Azure 活動目錄 （Azure AD） 以自動預配和取消將使用者或組預配到"線束"。

> [!NOTE]
> 本文介紹在 Azure AD 使用者預配服務之上構建的連接器。 有關此服務的重要資訊和常見問題解答，請參閱[使用 Azure 活動目錄 自動預配和取消預配到 SaaS 應用程式](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為預覽版。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本文中概述的方案假定您已經具備以下先決條件：

* Azure AD 租用戶
* [線束租戶](https://harness.io/pricing/)
* 具有*管理員*許可權的線束中的使用者帳戶

## <a name="assign-users-to-harness"></a>將使用者分配給線束

Azure 活動目錄使用稱為*分配*的概念來確定哪些使用者應接收對選定應用的存取權限。 在自動使用者預配的上下文中，只有分配給 Azure AD 中應用程式的使用者或組才會同步。

在配置和啟用自動使用者預配之前，請決定 Azure AD 中的哪些使用者或組需要訪問線束。 然後，您可以按照[將使用者或組分配給企業應用](../manage-apps/assign-user-or-group-access-portal.md)中的說明將這些使用者或組分配給 Harness。

## <a name="important-tips-for-assigning-users-to-harness"></a>將使用者分配給線束的重要提示

* 我們建議您為 Harness 分配單個 Azure AD 使用者以測試自動使用者預配配置。 稍後可以分配其他使用者或組。

* 將使用者分配給線束時，必須在 **"分配"** 對話方塊中選擇任何有效的特定于應用程式的角色（如果可用）。 具有*預設存取*角色的使用者會從佈建中排除。

## <a name="set-up-harness-for-provisioning"></a>設置用於預配的線束

1. 登錄到[您的線束管理主控台](https://app.harness.io/#/login)，然後轉到**持續安全** > **訪問管理**。

    ![線束管理主控台](media/harness-provisioning-tutorial/admin.png)

1. 選擇**API 金鑰**。

    ![線束 API 金鑰連結](media/harness-provisioning-tutorial/apikeys.png)

1. 選擇 **"添加 API 金鑰**"。 

    ![線束添加 API 鍵連結](media/harness-provisioning-tutorial/addkey.png)

1. 在 **"添加 Api 鍵**"窗格中，執行以下操作：

    ![線束添加 Api 鍵窗格](media/harness-provisioning-tutorial/title.png)
   
   a. 在 **"名稱"** 框中，提供鍵的名稱。  
   b. 在"從下拉清單中**繼承的許可權**"清單中，選擇一個選項。 
   
1. 選取 [提交]****。

1. 複製**金鑰**，以便以後在本教程中使用。

    ![線束創建權杖](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>從庫中添加線束

在為使用 Azure AD 自動使用者預配配置使用線束之前，需要將線束從 Azure AD 應用程式庫添加到託管 SaaS 應用程式清單中。

1. 在 [Azure 入口網站](https://portal.azure.com)的左側窗格中，選取 [Azure Active Directory]****。

    ![[Azure Active Directory] 按鈕](common/select-azuread.png)

1. 選擇**企業應用程式** > **所有應用程式**。

    ![[所有應用程式] 連結](common/enterprise-applications.png)

1. 要添加新應用程式，請選擇窗格頂部的 **"新建應用程式**"按鈕。

    ![[新增應用程式] 按鈕](common/add-new-app.png)

1. 在搜索框中，在結果清單中輸入**線束**，然後選擇 **"添加**"按鈕以添加應用程式。 **Harness**

    ![結果清單中的線束](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>將自動使用者配置配置為線束 

本節將指導您完成將 Azure AD 預配服務配置為根據 Azure AD 中的使用者或組分配在 Harness 中創建、更新和禁用使用者或組的步驟。

> [!TIP]
> 您也可以按照[線束單登錄教程](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial)中的說明，選擇啟用基於 SAML 的單位登錄。 您可以獨立于自動使用者預配配置單一登入，儘管這兩個功能相輔相成。

> [!NOTE]
> 要瞭解有關線束 SCIM 終結點的更多資訊，請參閱線束[API 金鑰](https://docs.harness.io/article/smloyragsm-api-keys)一文。

要在 Azure AD 中配置線束的自動使用者預配，請執行以下操作：

1. 在[Azure 門戶](https://portal.azure.com)中，選擇**企業應用程式** > **所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

1. 在應用程式清單中，選取 [Harness]****。

    ![應用程式清單中的線束連結](common/all-applications.png)

1. 選擇**預配**。

    ![預配按鈕](common/provisioning.png)

1. 在**預配模式**下拉清單中，選擇 **"自動**"。

    !["預配模式"下拉清單](common/provisioning-automatic.png)

1. 在 **"管理員憑據"** 下，執行以下操作：

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)
 
   a. 在 **"租戶 URL"** 框中**`https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw`**，輸入 。  
   b. 在 **"秘密權杖"** 框中，輸入您保存在"設置資源調配工具"部分步驟 6 中保存的 SCIM 身份驗證權杖值。  
   c. 選擇 **"測試連接**"以確保 Azure AD 可以連接到線束。 如果連接失敗，請確保您的線束帳戶具有*管理員*許可權，然後重試。

1. 在 **"通知電子郵件**"框中，輸入應接收預配錯誤通知的個人或組的電子郵件地址，然後選擇"在**發生故障時發送電子郵件通知**"核取方塊。

    !["通知電子郵件"框](common/provisioning-notification-email.png)

1. 選取 [儲存]****。

1. 在 **"映射**"下，選擇**將 Azure 活動目錄使用者同步到線束**。

    ![利用"將 Azure 活動目錄使用者同步到線束"連結](media/harness-provisioning-tutorial/usermappings.png)

1. 在**屬性對應**下，查看從 Azure AD 同步到線束的使用者屬性。 選擇為 *"匹配"* 的屬性用於匹配 Harness 中的使用者帳戶以進行更新操作。 選取 [儲存]**** 認可任何變更。

    ![線束使用者"屬性對應"窗格](media/harness-provisioning-tutorial/userattributes.png)

1. 在 **"映射**"下，選擇**將 Azure 活動目錄組同步以進行利用**。

    ![利用"將 Azure 活動目錄組同步到線束"連結](media/harness-provisioning-tutorial/groupmappings.png)

1. 在**屬性對應**下，查看從 Azure AD 同步到線束的組屬性。 選擇為 *"匹配屬性"* 的屬性用於匹配線束中的組以進行更新操作。 選取 [儲存]**** 認可任何變更。

    ![線束組"屬性對應"窗格](media/harness-provisioning-tutorial/groupattributes.png)

1. 要配置範圍篩選器，請參閱[使用範圍篩選器的基於屬性的應用程式預配](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。

1. 在 **"設置"** 下，要啟用線束的 Azure AD 預配服務，請將**預配狀態**開關切換為 **"打開**"。

    ![預配狀態開關切換為"開"](common/provisioning-toggle-on.png)

1. 在 **"範圍**"下拉清單中的 **"設置**"下，選擇要如何同步要預配到線束的使用者或組。

    ![佈建範圍](common/provisioning-scope.png)

1. 準備好預配時，請選擇"**保存**"。

    ![預配保存按鈕](common/provisioning-configuration-save.png)

此操作將開始預配的使用者或組的初始同步。 初始同步執行的時間比以後的同步長。 只要 Azure AD 預配服務正在運行，大約每 40 分鐘發生一次同步。 要監視進度，請轉到 **"同步詳細資訊"** 部分。 還可以遵循指向預配活動報表的連結，該報表描述 Azure AD 預配服務線上束上執行的所有操作。

有關如何讀取 Azure AD 預配日誌的詳細資訊，請參閱[有關自動使用者帳戶預配的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
