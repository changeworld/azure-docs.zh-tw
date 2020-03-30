---
title: 教程：使用 Azure 活動目錄配置 OrgWiki 以自動預配使用者 |微軟文檔
description: 瞭解如何將 Azure 活動目錄配置為自動預配和取消預配到 TheOrgWiki 的使用者帳戶。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eb2096e-f7b6-4a56-a814-0f3d0e788063
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2b0b11ae89e1f0d150b84f49b04a8badcb44e82c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063133"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>教程：為自動使用者預配配置 OrgWiki

本教程的目的是演示在 TheOrgWiki 和 Azure 活動目錄 （Azure AD） 中執行的步驟，以將 Azure AD 配置為自動預配和取消向 TheOrgWiki 預配使用者和/或組。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶。
* [組織維琪租戶](https://www.theorgwiki.com/welcome/)。
* 具有管理員許可權的 TheOrgWiki 中的使用者帳戶。

## <a name="assign-users-to-theorgwiki"></a>將使用者分配給 TheOrgWiki

Azure 活動目錄使用稱為分配的概念來確定哪些使用者應接收對選定應用的存取權限。 在自動使用者預配的上下文中，只有分配給 Azure AD 中應用程式的使用者和/或組才會同步。

在配置和啟用自動使用者預配之前，應決定 Azure AD 中的哪些使用者和/或組需要訪問 TheOrgWiki。 一旦確定，您可以按照此處的說明將這些使用者和/或組分配給 TheOrgWiki：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>將使用者分配給 TheOrgWiki 的重要提示

* 建議將單個 Azure AD 使用者分配給 TheOrgWiki 以測試自動使用者預配配置。 其他使用者及/或群組可能會稍後再指派。

* 將使用者分配給 TheOrgWiki 時，必須在分配對話方塊中選擇任何有效的特定于應用程式的角色（如果可用）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="set-up-theorgwiki-for-provisioning"></a>設置用於預配的 OrgWiki

在配置 OrgWiki 以進行 Azure AD 自動使用者預配之前，需要在 TheOrgWiki 上啟用 SCIM 預配。

1. 登錄到您的[TheOrgWiki管理主控台](https://www.theorgwiki.com/login/)。 按一下**管理主控台**。

    ![OrgWiki 添加 SCIM](media/theorgwiki-provisioning-tutorial/login.png)

2. 在管理主控台中，按一下 **"設置"選項卡**。 

    ![OrgWiki 添加 SCIM](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. 導航到**服務帳戶**。

    ![OrgWiki 添加 SCIM](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. 按一下 **+服務帳戶**。 在**服務帳戶類型**下 ，選擇**基於權杖**。 按一下 [儲存]****。

    ![OrgWiki 添加 SCIM](media/theorgwiki-provisioning-tutorial/auth.png)

5.  複製**活動權杖**。 此值將在 Azure 門戶中 TheOrgWiki 應用程式的預配選項卡中的"機密權杖"欄位中輸入。
     
    ![OrgWiki 添加 SCIM](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>從圖庫添加 TheOrgWiki

要將 TheOrgWiki 配置為使用 Azure AD 進行自動使用者預配，需要將 Azure AD 應用程式庫中的 TheOrgWiki 添加到託管 SaaS 應用程式清單中。

1. 在**[Azure 門戶](https://portal.azure.com)** 中，在左側導航面板中，選擇**Azure 活動目錄**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 轉到**企業應用程式**，然後選擇 **"所有應用程式**"。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 要添加新應用程式，請選擇窗格頂部的 **"新建應用程式**"按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜索框中，輸入 **"OrgWiki"，** 在結果面板中選擇 **"OrgWiki"。** 

    ![結果清單中的OrgWiki](common/search-new-app.png)

5. 選擇 **"註冊為TheOrgWiki"** 按鈕，該按鈕將您重定向到TheOrgWiki的登錄頁面。 

    ![OrgWiki 添加 SCIM](media/theorgwiki-provisioning-tutorial/image00.png)

6.  在右上角，選擇 **"登錄**"。

    ![OrgWiki 添加 SCIM](media/theorgwiki-provisioning-tutorial/image02.png)

7. 由於 TheOrgWiki 是一個 OpenIDConnect 應用程式，因此選擇使用您的 Microsoft 工作帳戶登錄到 OrgWiki。

    ![OrgWiki 添加 SCIM](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. 身份驗證成功後，應用程式將自動添加到租戶中，您將被重定向到您的 TheOrgWiki 帳戶。

    ![OrgWiki 添加 SCIM](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>配置自動使用者預配到 OrgWiki 

本節將指導您完成將 Azure AD 預配服務配置為根據 Azure AD 中的使用者和/或組分配在 TheOrgWiki 中創建、更新和禁用使用者和/或組的步驟。


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>要在 Azure AD 中配置 TheOrgWiki 的自動使用者預配：

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式**，然後選擇**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選擇 **"OrgWiki"。**

    !["應用程式"清單中的 OrgWiki 連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![預配選項卡](common/provisioning.png)

4. 將**預配模式**設置為 **"自動**"。

    ![預配選項卡](common/provisioning-automatic.png)

5. 在 **"管理認證"** 部分下`https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/`，在**租戶 URL**中輸入 。 

    範例： `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> **子域值**只能在 TheOrgWiki 的初始註冊過程中設置。
 
6. 在 **"秘密權杖"** 欄位中輸入權杖值，該欄位是您之前從 TheOrgWiki 檢索的。 按一下 **"測試連接**"以確保 Azure AD 可以連接到 OrgWiki。 如果連接失敗，請確保您的 TheOrgWiki 帳戶具有管理員許可權，然後重試。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

7. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

8. 按一下 [儲存]****。

9. 在 **"映射"** 部分下，選擇**將 Azure 活動目錄使用者同步到 OrgWiki**。

    ![OrgWiki 使用者映射](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. 在**屬性對應**部分中查看從 Azure AD 同步到 TheOrgWiki 的使用者屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 TheOrgWiki 中的使用者帳戶以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![OrgWiki 使用者屬性](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

12. 要啟用 TheOrgWiki 的 Azure AD 預配服務，在 **"設置"** 部分將**預配狀態**更改為 **"打開**"。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

13. 通過在 **"設置"** 部分中選擇"**範圍"** 中所需的值，定義要預配到 OrgWiki 的使用者和/或組。

    ![佈建範圍](common/provisioning-scope.png)

14. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步執行的時間比後續同步長。 有關使用者和/或組預配需要多長時間的詳細資訊，請參閱[預配使用者需要多長時間](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)。

可以使用"**目前狀態**"部分監視進度並關注指向預配活動報告的連結，該報表描述 Azure AD 預配服務在 TheOrgWiki 上執行的所有操作。 有關詳細資訊，請參閱[檢查使用者預配的狀態](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 要讀取 Azure AD 預配日誌，請參閱[報告自動使用者帳戶預配](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](../app-provisioning/configure-automatic-user-provisioning-portal.md)。
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何查看日誌並獲取有關預配活動的報告](../app-provisioning/check-status-user-account-provisioning.md)。