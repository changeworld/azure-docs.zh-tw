---
title: 教程：使用 Azure 活動目錄配置 Wrike 以自動預配使用者 |微軟文檔
description: 瞭解如何將 Azure 活動目錄配置為自動預配和取消將使用者帳戶預配到 Wrike。
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
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: 5dd4f5ac6152c22b5e2a84ecc0774672bcd5590b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064184"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>教程：為自動使用者預配配置 Wrike

本教程的目的是演示您在 Wrike 和 Azure 活動目錄 （Azure AD） 中執行的步驟，以將 Azure AD 配置為自動預配和取消將使用者或組預配到 Wrike。

> [!NOTE]
> 本教程介紹在 Azure AD 使用者預配服務之上構建的連接器。 有關此服務的作用、工作方式以及常見問題的重要詳細資訊，請參閱[使用 Azure 活動目錄 自動使用者預配和取消預配軟體即服務 （SaaS） 應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
>
> 此連接器目前為公開預覽版。 有關預覽功能的一般 Microsoft Azure 使用條款的詳細資訊，請參閱[Microsoft Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [一個雷克斯租戶](https://www.wrike.com/price/)
* 具有管理員許可權的 Wrike 中的使用者帳戶

## <a name="assign-users-to-wrike"></a>將使用者分配給 Wrike
Azure 活動目錄使用稱為*分配*的概念來確定哪些使用者應接收對選定應用的存取權限。 在自動使用者預配的上下文中，只有分配給 Azure AD 中應用程式的使用者或組才會同步。

在配置和啟用自動使用者預配之前，請決定 Azure AD 中的哪些使用者或組需要訪問 Wrike。 然後按照此處的說明將這些使用者或組分配給 Wrike：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>將使用者分配給 Wrike 的重要提示

* 我們建議您將單個 Azure AD 使用者分配給 Wrike 以測試自動使用者預配配置。 稍後可以分配其他使用者或組。

* 將使用者分配給 Wrike 時，必須在分配對話方塊中選擇任何有效的特定于應用程式的角色（如果可用）。 具有預設存取角色的使用者會從佈建中排除。

## <a name="set-up-wrike-for-provisioning"></a>設置用於預配的 Wrike

在為使用 Azure AD 自動使用者預配配置之前，需要在 Wrike 上啟用跨域標識管理系統 （SCIM） 預配。

1. 登錄到您的[Wrike 管理主控台](https://www.Wrike.com/login/)。 轉到租戶 ID。 選擇 **&集成的應用**。

    ![應用&集成](media/Wrike-provisioning-tutorial/admin.png)

2.  轉到**Azure AD**並選擇它。

    ![Azure AD](media/Wrike-provisioning-tutorial/Capture01.png)

3.  選擇 SCIM。 複製**基本 URL**。

    ![基底 URL](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. 選擇**API** > **Azure SCIM**。

    ![Azure SCIM](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  將打開一個快顯視窗。 輸入之前創建的創建帳戶的相同密碼。

    ![創建權杖](media/Wrike-provisioning-tutorial/password.png)

6.  複製**機密權杖**，並將其粘貼到 Azure AD 中。 選擇 **"保存**"以完成 Wrike 上的預配設置。

    ![永久訪問權杖](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>從庫中添加 Wrike

在使用 Azure AD 配置 Wrike 以自動預配使用者之前，請將 Wrike 從 Azure AD 應用程式庫添加到託管 SaaS 應用程式清單中。

要從 Azure AD 應用程式庫添加 Wrike，請按照以下步驟操作。

1. 在[Azure 門戶](https://portal.azure.com)中，在左側功能窗格中，選擇**Azure 活動目錄**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 轉到**企業應用程式**，然後選擇 **"所有應用程式**"。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 要添加新應用程式，請選擇窗格頂部的 **"新建應用程式**"按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜索框中，在結果面板中選擇 **"Wrike"，** 然後選擇**Wrike****"添加**"以添加應用程式。

    ![結果清單中的 Wrike](common/search-new-app.png)


## <a name="configure-automatic-user-provisioning-to-wrike"></a>將自動使用者預配配置為 Wrike 

本節將指導您完成將 Azure AD 預配服務配置為根據 Azure AD 中的使用者或組分配在 Wrike 中創建、更新和禁用使用者或組的步驟。

> [!TIP]
> 要啟用基於 SAML 的單登錄對 Wrike 的操作，請按照[Wrike 單登錄教程](wrike-tutorial.md)中的說明進行操作。 單一登入可以獨立于自動使用者預配進行配置，儘管這兩個功能相輔相成。

### <a name="configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>在 Azure AD 中為 Wrike 配置自動使用者預配

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式** > **所有應用程式**。

    ![所有應用程式](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Wrike]****。

    ![應用程式清單中的 Wrike 連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![預配選項卡](common/provisioning.png)

4. 將**預配模式**設置為 **"自動**"。

    ![預配模式設置為"自動"](common/provisioning-automatic.png)

5. 在"管理員憑據"部分下，分別輸入**在租戶 URL**和 **"機密權杖**"中檢索到**的基本 URL**和**永久訪問權杖**值。 選擇**測試連接**以確保 Azure AD 可以連接到 Wrike。 如果連接失敗，請確保您的 Wrike 帳戶具有管理員許可權，然後重試。

    ![租戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

7. 在 **"通知電子郵件"** 框中，輸入應接收預配錯誤通知的個人或組的電子郵件地址。 選中"**在發生故障時發送電子郵件通知**"核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

8. 選取 [儲存]****。

9. 在 **"映射"** 部分下，選擇**將 Azure 活動目錄使用者同步到 Wrike**。

    ![Wrike 使用者映射](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. 在**屬性對應**部分中查看從 Azure AD 同步到 Wrike 的使用者屬性。 選擇為 **"匹配屬性"** 的屬性用於與 Wrike 中的使用者帳戶匹配以進行更新操作。 選取 [儲存]**** 認可任何變更。

    ![Wrike 使用者屬性](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. 要配置範圍篩選器，請按照[範圍篩選器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的說明進行操作。

12. 要為 Wrike 啟用 Azure AD 預配服務，在 **"設置"** 部分將**預配狀態**更改為 **"打開**"。

    ![預配狀態切換打開](common/provisioning-toggle-on.png)

13. 通過在 **"設置"** 部分中選擇"**範圍"** 中所需的值，定義要預配到 Wrike 的使用者或組。

    ![佈建範圍](common/provisioning-scope.png)

14. 準備好預配時，請選擇"**保存**"。

    ![保存預配配置](common/provisioning-configuration-save.png)

此操作將開始**在"設置"** 部分中定義"**範圍"** 中定義的所有使用者或組的初始同步。 初始同步執行的時間比後續同步長。 有關使用者或組預配需要多長時間的詳細資訊，請參閱[預配使用者需要多長時間？](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

可以使用"**目前狀態**"部分監視進度並遵循指向預配活動報告的連結，該報表描述 Azure AD 預配服務在 Wrike 上執行的所有操作。 有關詳細資訊，請參閱[檢查使用者預配的狀態](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 要讀取 Azure AD 預配日誌，請參閱[報告自動使用者帳戶預配](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
