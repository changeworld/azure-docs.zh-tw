---
title: 教程：使用 Azure 活動目錄配置 Foode 以自動預配使用者 |微軟文檔
description: 瞭解如何將 Azure 活動目錄配置為自動預配和取消向 Foodee 預配使用者帳戶。
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
ms.openlocfilehash: 2195056ec66550063aba5ce5e2b977b51a6dc5e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057801"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>教程：為自動使用者預配配置 Foodee

本文介紹如何在 Foodee 和 Azure AD 中配置 Azure 活動目錄 （Azure AD） 以自動預配或取消向 Foodee 預配使用者或組。

> [!NOTE]
> 本文介紹了在 Azure AD 使用者預配服務之上構建的連接器。 要瞭解此服務的作用及其工作原理，並獲取常見問題的解答，請參閱[使用 Azure 活動目錄 自動預配和取消預配到 SaaS 應用程式](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為預覽版。 有關預覽功能的 Azure 使用條款功能的詳細資訊，請訪問 Microsoft Azure[預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教程假定您已滿足以下先決條件：

* Azure AD 租用戶
* [美食家租戶](https://www.food.ee/about/)
* 具有管理員許可權的 Foodee 中的使用者帳戶

## <a name="assign-users-to-foodee"></a>將使用者分配給 Foodee 

Azure AD 使用稱為*分配*的概念來確定哪些使用者應接收對選定應用的存取權限。 在自動使用者預配的上下文中，只有分配給 Azure AD 中應用程式的使用者或組才會同步。

在配置和啟用自動使用者預配之前，應決定 Azure AD 中的哪些使用者或組需要訪問 Foodee。 做出此確定後，您可以按照將[使用者或組分配給企業應用](../manage-apps/assign-user-or-group-access-portal.md)中的說明將這些使用者或組分配給 Foodee。

## <a name="important-tips-for-assigning-users-to-foodee"></a>將使用者分配給 Foode 的重要提示 

在分配使用者時，請記住以下提示：

* 我們建議您只向 Foodee 分配單個 Azure AD 使用者，以測試自動使用者預配的配置。 您可以稍後分配其他使用者或組。

* 將使用者分配給 Foodee 時，請在 **"分配"** 窗格中選擇任何有效的特定于應用程式的角色（如果可用）。 具有*預設存取權限*角色的使用者將從預配中排除。

## <a name="set-up-foodee-for-provisioning"></a>設置用於預配的食品

在使用 Azure AD 為自動使用者預配配置 Foode 之前，需要在 Foodee 中啟用跨域標識管理系統 （SCIM） 預配系統。

1. 登錄到[Foodee，](https://www.food.ee/login/)然後選擇您的租戶 ID。

    ![Foodee](media/Foodee-provisioning-tutorial/tenant.png)

1. 在 **"企業門戶**"下，選擇 **"單一登入**"。

    ![美食企業門戶左窗格功能表](media/Foodee-provisioning-tutorial/scim.png)

1. 複製**API 權杖**框中的值，以便以後使用。 您將在 Azure 門戶中"提供"應用程式的 **"預配**"選項卡中的 **"機密權杖"** 框中輸入它。

    ![Foodee](media/Foodee-provisioning-tutorial/token.png)

## <a name="add-foodee-from-the-gallery"></a>從庫中添加美食家

要使用 Azure AD 配置 Foodee 以自動預配使用者，需要將從 Azure AD 應用程式庫中的 Foodee 添加到託管 SaaS 應用程式清單中。

要從 Azure AD 應用程式庫添加 Foodee，請執行以下操作：

1. 在 [Azure 入口網站](https://portal.azure.com)的左側窗格中，選取 [Azure Active Directory]****。

    ![Azure 活動目錄命令](common/select-azuread.png)

1. 選擇**企業應用程式** > **所有應用程式**。

    ![[企業應用程式] 窗格](common/enterprise-applications.png)

1. 要添加新應用程式，請在窗格頂部選擇 **"新建應用程式**"。

    ![新增應用程式按鈕](common/add-new-app.png)

1. 在搜索框中，在結果窗格中選擇 **"Foodee"，** 然後選擇 **"添加**"以添加應用程式。 **Foodee**

    ![結果清單中的美食家](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>配置對 Foodee 的自動使用者預配 

在本節中，您將 Azure AD 預配服務配置為根據 Azure AD 中的使用者或組分配創建、更新和禁用 Foode 中的使用者或組。

> [!TIP]
> 您還可以按照[Foode 單一登入教程](Foodee-tutorial.md)中的說明為 Foode 啟用基於 SAML 的單一登入。 您可以獨立于自動使用者預配配置單一登入，儘管這兩個功能相輔相成。

通過執行以下操作，在 Azure AD 中為 Foode 配置自動使用者預配：

1. 在[Azure 門戶](https://portal.azure.com)中，選擇**企業應用程式** > **所有應用程式**。

    ![企業應用程式窗格](common/enterprise-applications.png)

1. 在 **"應用程式**"清單中，選擇 **"美食家**"。

    !["應用程式"清單中的美食人連結](common/all-applications.png)

1. 選擇 **"預配"** 選項卡。

    ![預配選項卡](common/provisioning.png)

1. 在**預配模式**下拉清單中，選擇 **"自動**"。

    ![預配選項卡](common/provisioning-automatic.png)

1. 在 **"管理員憑據"** 下，執行以下操作：

   a. 在 **"租戶 URL"** 框中，輸入之前檢索的**\/HTTPs：/concierge.food.ee/scim/v2**值。

   b. 在 **"秘密權杖"** 框中，輸入之前檢索的**API 權杖**值。
   
   c. 為確保 Azure AD 可以連接到 Foodee，請選擇 **"測試連接**"。 如果連接失敗，請確保您的 Foodee 帳戶具有管理員許可權，然後重試。

    ![測試連接連結](common/provisioning-testconnection-tenanturltoken.png)

1. 在 **"通知電子郵件**"框中，輸入應接收預配錯誤通知的個人或組的電子郵件地址，然後選擇"在**發生故障時發送電子郵件通知**"核取方塊。

    ![通知電子郵件文字方塊](common/provisioning-notification-email.png)

1. 選取 [儲存]****。

1. 在 **"映射**"下，選擇**將 Azure 活動目錄使用者同步到 Foode。**

    ![食品使用者映射](media/Foodee-provisioning-tutorial/usermapping.png)

1. 在**屬性對應**下，查看從 Azure AD 同步到 Foodee 的使用者屬性。 選擇為 **"匹配屬性"** 的屬性用於與 Foodee 中的*使用者帳戶*匹配以進行更新操作。 

    ![食品使用者映射](media/Foodee-provisioning-tutorial/userattribute.png)

1. 要提交更改，請選擇"**保存**"。
1. 在 **"映射**"下，選擇**將 Azure 活動目錄組同步到 Foode。**

    ![食品使用者映射](media/Foodee-provisioning-tutorial/groupmapping.png)

1. 在**屬性對應**下，查看從 Azure AD 同步到 Foodee 的使用者屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 Foodee 中的*群組帳戶*以進行更新操作。

    ![食品使用者映射](media/Foodee-provisioning-tutorial/groupattribute.png)

1. 要提交更改，請選擇"**保存**"。
1. 配置範圍篩選器。 要瞭解如何操作，請參閱[範圍篩選器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的說明。

1. 要為 Foode 啟用 Azure AD 預配服務，在 **"設置"** 部分中，將**預配狀態**更改為**On**。

    ![預配狀態開關](common/provisioning-toggle-on.png)

1. 在 **"設置**"下**Scope**拉清單中，定義要預配到 Foodee 的使用者或組。

    ![預配範圍下拉清單](common/provisioning-scope.png)

1. 準備好預配時，請選擇"**保存**"。

    ![預配配置保存按鈕](common/provisioning-configuration-save.png)

前面的操作開始您在 **"範圍**"下拉清單中定義的使用者或組的初始同步。 初始同步執行的時間比後續同步長。 有關詳細資訊，請參閱[預配使用者需要多長時間？。](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

您可以使用 **"目前狀態"** 部分監視進度並關注指向預配活動報告的連結。 該報表描述 Azure AD 預配服務在 Foode 上執行的所有操作。 有關詳細資訊，請參閱[檢查使用者預配的狀態](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 要讀取 Azure AD 預配日誌，請參閱[報告自動使用者帳戶預配](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
