---
title: 教程：使用 Azure 活動目錄自動設定訪問源， |微軟文檔
description: 瞭解如何將 Azure 活動目錄配置為自動預配和取消將使用者帳戶預配為"訪問"。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 73cc1a58689db7902843f222aa4874a5e188be44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063141"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>教程：為自動使用者預配配置訪問

本教程的目的是演示您在訪問和 Azure 活動目錄 （Azure AD） 中執行的步驟，以將 Azure AD 配置為自動預配和取消將使用者或組預配為"訪問"。

> [!NOTE]
> 本教程介紹在 Azure AD 使用者預配服務之上構建的連接器。 有關此服務的作用、工作方式以及常見問題的重要詳細資訊，請參閱[使用 Azure 活動目錄 自動使用者預配和取消預配軟體即服務 （SaaS） 應用程式](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 有關預覽功能的一般 Microsoft Azure 使用條款的詳細資訊，請參閱[Microsoft Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [訪問租戶](https://www.visitly.io/pricing/)
* 具有管理員許可權的訪問中的使用者帳戶

## <a name="assign-users-to-visitly"></a>將使用者分配到訪問 

Azure 活動目錄使用稱為*分配*的概念來確定哪些使用者應接收對選定應用的存取權限。 在自動使用者預配的上下文中，只有分配給 Azure AD 中應用程式的使用者或組才會同步。

在配置和啟用自動使用者預配之前，請決定 Azure AD 中的哪些使用者或組需要訪問訪問。 然後按照此處的說明將這些使用者或組指定為"訪問"：
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>將使用者分配到訪問的重要提示 

* 我們建議您為訪問點分配單個 Azure AD 使用者以測試自動使用者預配配置。 稍後可以分配其他使用者或組。

* 將使用者分配給"訪問"時，必須在分配對話方塊中選擇任何有效的特定于應用程式的角色（如果可用）。 具有預設存取角色的使用者會從佈建中排除。

## <a name="set-up-visitly-for-provisioning"></a>為預配設置訪問

在使用 Azure AD 自動設定訪問之前，需要在訪問時啟用跨域標識管理系統 （SCIM） 預配。

1. 登錄[訪問。](https://app.visitly.io/login) 選擇**集成** > **主機同步**。

    ![主機同步](media/Visitly-provisioning-tutorial/login.png)

2. 選擇**Azure AD**部分。

    ![Azure AD 部分](media/Visitly-provisioning-tutorial/integration.png)

3. 複製**API 金鑰**。 這些值在 Azure 門戶中訪問應用程式"**預配**"選項卡上的 **"秘密權杖"** 框中輸入。

    ![API 金鑰](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>從圖庫中添加訪問

要使用 Azure AD 自動設定訪問，請將"訪問"從 Azure AD 應用程式庫添加到託管 SaaS 應用程式清單中。

要從 Azure AD 應用程式庫添加訪問，請按照以下步驟操作。

1. 在[Azure 門戶](https://portal.azure.com)中，在左側功能窗格中，選擇**Azure 活動目錄**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 轉到**企業應用程式**，然後選擇 **"所有應用程式**"。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 要添加新應用程式，請選擇窗格頂部的 **"新建應用程式**"按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜索框中，在"**訪問"** 中輸入"訪問"，在結果面板中選擇 **"訪問"，** 然後選擇 **"添加"** 以添加應用程式。

    ![結果清單中的 Visitly](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>將自動使用者預配配置為訪問 

本節將指導您完成將 Azure AD 預配服務配置為根據 Azure AD 中的使用者或組分配創建、更新和禁用訪問中的使用者或組的步驟。

> [!TIP]
> 要啟用基於 SAML 的單登錄訪問，請按照[訪問單登錄教程](Visitly-tutorial.md)中的說明進行操作。 單一登入可以獨立于自動使用者預配進行配置，儘管這兩個功能相輔相成。

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>在 Azure AD 中為訪問配置自動使用者預配

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式** > **所有應用程式**。

    ![所有應用程式](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Visitly]****。

    ![應用程式清單中的 Visitly 連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![預配選項卡](common/provisioning.png)

4. 將**預配模式**設置為 **"自動**"。

    ![預配模式設置為"自動"](common/provisioning-automatic.png)

5. 在"管理員憑據"部分下，`https://api.visitly.io/v1/usersync/SCIM`分別輸入**在租戶 URL**和**秘密權杖**中檢索的 和**API 金鑰**值。 選擇 **"測試連接**"以確保 Azure AD 可以以訪問方式連接到。 如果連接失敗，請確保您的存取帳戶具有管理員許可權，然後重試。

    ![租戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 **"通知電子郵件"** 框中，輸入應接收預配錯誤通知的個人或組的電子郵件地址。 選中"**在發生故障時發送電子郵件通知**"核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]****。

8. 在 **"映射**"部分下，選擇 **"同步 Azure 活動目錄使用者以訪問**"。

    ![訪問使用者映射](media/visitly-provisioning-tutorial/usermapping.png)

9. 在**屬性對應**部分中查看從 Azure AD 同步到訪問的使用者屬性。 選擇為 **"匹配屬性"** 的屬性用於與"訪問"中的使用者帳戶匹配以進行更新操作。 選取 [儲存]**** 認可任何變更。

    ![訪問使用者屬性](media/visitly-provisioning-tutorial/userattribute.png)

10. 要配置範圍篩選器，請按照[範圍篩選器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的說明進行操作。

11. 要啟用 Azure AD 預配服務以訪問，在 **"設置"** 部分將**預配狀態**更改為 **"打開**"。

    ![預配狀態切換打開](common/provisioning-toggle-on.png)

12. 通過在 **"設置"** 部分中選擇"**範圍"** 中所需的值，定義要預配到訪問的使用者或組。

    ![佈建範圍](common/provisioning-scope.png)

13. 準備好預配時，請選擇"**保存**"。

    ![保存預配配置](common/provisioning-configuration-save.png)

此操作將開始**在"設置"** 部分中定義"**範圍"** 中定義的所有使用者或組的初始同步。 初始同步執行的時間比後續同步長。 有關使用者或組預配需要多長時間的詳細資訊，請參閱[預配使用者需要多長時間？](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

可以使用"**目前狀態**"部分監視進度並遵循指向預配活動報告的連結，該報表描述 Azure AD 預配服務在訪問時執行的所有操作。 有關詳細資訊，請參閱[檢查使用者預配的狀態](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 要讀取 Azure AD 預配日誌，請參閱[報告自動使用者帳戶預配](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

訪問不支援實刪除。 一切都是虛刪除的。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
