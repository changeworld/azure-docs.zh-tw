---
title: 教程：使用 Azure 活動目錄配置 StarLeaf 以自動預配使用者 |微軟文檔
description: 瞭解如何將 Azure 活動目錄配置為自動預配和取消向 StarLeaf 預配使用者帳戶。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2019
ms.author: zhchia
ms.openlocfilehash: 520373fc6a05bcaada973273e3553f9da623c669
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064286"
---
# <a name="tutorial-configure-starleaf-for-automatic-user-provisioning"></a>教程：為自動使用者預配配置 StarLeaf

本教程的目的是演示在 StarLeaf 和 Azure 活動目錄 （Azure AD） 中執行的步驟，以將 Azure AD 配置為自動預配和取消向 StarLeaf 預配使用者和/或組。

> [!NOTE]
>  本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶。
* [星葉租戶](https://www.starleaf.com/solutions/)。
* 具有管理員許可權的 StarLeaf 中的使用者帳戶。

## <a name="assign-users-to-starleaf"></a>將使用者分配給 StarLeaf
Azure 活動目錄使用稱為分配的概念來確定哪些使用者應接收對選定應用的存取權限。 在自動使用者預配的上下文中，只有分配給 Azure AD 中應用程式的使用者和/或組才會同步。

在配置和啟用自動使用者預配之前，應決定 Azure AD 中的哪些使用者和組需要訪問 StarLeaf。 然後，您可以按照[這些說明](../manage-apps/assign-user-or-group-access-portal.md)將使用者和組分配給 StarLeaf。

## <a name="important-tips-for-assigning-users-to-starleaf"></a>將使用者分配給 StarLeaf 的重要提示

* 建議將單個 Azure AD 使用者分配給 StarLeaf 以測試自動使用者預配配置。 稍後可以分配其他使用者和組。

* 將使用者分配給 StarLeaf 時，必須在分配對話方塊中選擇任何有效的特定于應用程式的角色（如果可用）。 具有預設存取角色的使用者會從佈建中排除。

## <a name="set-up-starleaf-for-provisioning"></a>設置用於預配的 StarLeaf

在為使用 Azure AD 進行自動使用者預配之前，需要在 StarLeaf 中配置 SCIM 預配：

1. 登錄到您的[StarLeaf 管理主控台](https://portal.starleaf.com/#page=login)。 導航到**集成** > **添加集成**。

    ![星葉添加 SCIM](media/starleaf-provisioning-tutorial/image00.png)

2. 選擇 **"類型**"為 Microsoft Azure 活動目錄。 在**名稱**中輸入合適的名稱。 按一下 [套用]****。

    ![星葉添加 SCIM](media/starleaf-provisioning-tutorial/image01.png)

3.  然後將顯示**SCIM 基本 URL**和**Access 權杖**值。 這些值將在 Azure 門戶 StarLeaf 應用程式的預配選項卡中的 **"租戶 URL"** 和 **"秘密權杖"** 欄位中輸入。 

    ![星葉創建權杖](media/starleaf-provisioning-tutorial/image02.png)

## <a name="add-starleaf-from-the-gallery"></a>從圖庫添加星葉

要配置 StarLeaf 以使用 Azure AD 進行自動使用者預配，需要將 Azure AD 應用程式庫中的 StarLeaf 添加到託管 SaaS 應用程式清單中。

**要從 Azure AD 應用程式庫添加 StarLeaf，請執行以下步驟：**

1. 在**[Azure 門戶](https://portal.azure.com)** 中，在左側導航面板中，選擇**Azure 活動目錄**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 轉到**企業應用程式**，然後選擇 **"所有應用程式**"。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 要添加新應用程式，請選擇窗格頂部的 **"新建應用程式**"按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜索框中，輸入**StarLeaf**，在結果面板中選擇**StarLeaf。**
    ![結果清單中的 StarLeaf](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-starleaf"></a>配置自動使用者預配到 StarLeaf

本節將指導您完成將 Azure AD 預配服務配置為根據 Azure AD 中的使用者和/或組分配在 StarLeaf 中創建、更新和禁用使用者和/或組的步驟。

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式**，然後選擇**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選擇**StarLeaf**。

    !["應用程式"清單中的 StarLeaf 連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![預配選項卡](common/provisioning.png)

4. 將**預配模式**設置為 **"自動**"。

    ![預配選項卡](common/provisioning-automatic.png)

5. 在"管理認證"部分下，輸入之前在**租戶 URL**和 **"秘密權杖"** 中檢索的**SCIM 基本 URL**和**訪問權杖**值。 按一下 **"測試連接**"以確保 Azure AD 可以連接到 StarLeaf。 如果連接失敗，請確保您的 StarLeaf 帳戶具有管理員許可權，然後重試。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 **"通知電子郵件"** 欄位中，輸入應接收預配錯誤通知的個人或組的電子郵件地址，並在**發生故障時選中"發送電子郵件通知**"核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [儲存]****。

8. 在 **"映射"** 部分下，選擇**將 Azure 活動目錄使用者同步到 StarLeaf**。

    ![星葉創建權杖](media/starleaf-provisioning-tutorial/usermapping.png)

9. 在**屬性對應**部分中查看從 Azure AD 同步到 StarLeaf 的使用者屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 StarLeaf 中的使用者帳戶以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![星葉創建權杖](media/starleaf-provisioning-tutorial/userattribute.png)


10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。


11. 要為 StarLeaf 啟用 Azure AD 預配服務，在 **"設置"** 部分將**預配狀態**更改為 **"打開**"。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 通過在 **"設置"** 部分中選擇"**範圍"** 中所需的值，定義要預配到 StarLeaf 的使用者和/或組。

    ![佈建範圍](common/provisioning-scope.png)

13. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 可以使用 **"同步詳細資訊"** 部分監視進度並關注指向預配活動報告的連結，該報表描述 Azure AD 預配服務在 StarLeaf 上執行的所有操作。

有關如何讀取 Azure AD 預配日誌的詳細資訊，請參閱[報告自動使用者帳戶預配](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>連接器限制

* StarLeaf 目前不支援組預配。 
* StarLeaf 要求**電子郵件**和**使用者名**值具有相同的源值。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* 瞭解如何[查看日誌並獲取有關預配活動的報告](../app-provisioning/check-status-user-account-provisioning.md)。
