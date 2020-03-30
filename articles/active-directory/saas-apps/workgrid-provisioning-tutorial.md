---
title: 教程：使用 Azure 活動目錄配置工作網格以自動預配使用者 |微軟文檔
description: 瞭解如何將 Azure 活動目錄配置為自動預配和取消預配到工作網格的使用者帳戶。
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
ms.date: 08/17/2019
ms.author: Zhchia
ms.openlocfilehash: 94d70447117c73a309959ddf66972c921aa5e687
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062801"
---
# <a name="tutorial-configure-workgrid--for-automatic-user-provisioning"></a>教程：為自動使用者預配配置工作網格

本教程的目的是演示要在工作網格和 Azure 活動目錄 （Azure AD） 中執行的步驟，以將 Azure AD 配置為自動預配和取消向工作網格預配使用者和/或組。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶。
* [工作網格租戶](https://www.workgrid.com/)
* 具有管理員許可權的工作網格中的使用者帳戶。

## <a name="assigning-users-to-workgrid"></a>將使用者分配給工作網格 

Azure 活動目錄使用稱為*分配*的概念來確定哪些使用者應接收對選定應用的存取權限。 在自動使用者預配的上下文中，只有分配給 Azure AD 中應用程式的使用者和/或組才會同步。

在配置和啟用自動使用者預配之前，應決定 Azure AD 中的哪些使用者和/或組需要訪問工作網格。 一旦確定，您可以按照此處的說明將這些使用者和/或組分配給 Workgrid：
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-workgrid"></a>將使用者分配給工作網格的重要提示 

* 建議將單個 Azure AD 使用者分配到工作網格以測試自動使用者預配配置。 其他使用者及/或群組可能會稍後再指派。

* 將使用者分配給 Workgrid 時，必須在分配對話方塊中選擇任何有效的特定于應用程式的角色（如果可用）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="set-up-workgrid-for-provisioning"></a>設置用於預配的工作網格

在使用 Azure AD 配置工作網格以自動預配使用者之前，需要在工作網格上啟用 SCIM 預配。

1. 登錄到工作網格。 導航到**使用者>使用者預配**。

    ![Workgrid](media/Workgrid-provisioning-tutorial/user.png)

2. 在**帳戶管理 API**下，按一下"**創建憑據**"。

    ![Workgrid](media/Workgrid-provisioning-tutorial/scim.png)

3. 複製**SCIM 終結點**和**訪問權杖**值。 這些將在 Azure 門戶中工作網格應用程式的預配選項卡中的 **"租戶 URL"** 和 **"秘密權杖"** 欄位中輸入。

    ![Workgrid](media/Workgrid-provisioning-tutorial/token.png)


## <a name="add-workgrid--from-the-gallery"></a>從庫中添加工作網格

要配置工作網格以使用 Azure AD 自動預配使用者，需要將工作網格從 Azure AD 應用程式庫添加到託管 SaaS 應用程式清單中。

**要從 Azure AD 應用程式庫添加工作網格，請執行以下步驟：**

1. 在**[Azure 門戶](https://portal.azure.com)** 中，在左側導航面板中，選擇**Azure 活動目錄**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 轉到**企業應用程式**，然後選擇 **"所有應用程式**"。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 要添加新應用程式，請選擇窗格頂部的 **"新建應用程式**"按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜索框中，在"**工作網格**"中輸入"工作網格"，在結果面板中選擇 **"工作網格**"，然後按一下"**添加**"按鈕以添加應用程式。

    ![結果清單中的工作網格](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-workgrid"></a>將自動使用者預配配置配置為工作網格  

本節將指導您完成將 Azure AD 預配服務配置為根據 Azure AD 中的使用者和/或組分配在工作網格中創建、更新和禁用使用者和/或組的步驟。

> [!TIP]
> 您也可以根據[工作網格單一登入教程](Workgrid-tutorial.md)中提供的說明，選擇啟用基於 SAML 的單一登入工作網格。 單一登入可以獨立于自動使用者預配進行配置，儘管這兩個功能相互補充

### <a name="to-configure-automatic-user-provisioning-for-workgrid--in-azure-ad"></a>要在 Azure AD 中配置工作網格的自動使用者預配：

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式**，然後選擇**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Workgrid]****。

    !["應用程式"清單中的工作網格連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![預配選項卡](common/provisioning.png)

4. 將**預配模式**設置為 **"自動**"。

    ![預配選項卡](common/provisioning-automatic.png)

5. 在"管理認證"部分下，輸入之前在**租戶 URL**和 **"秘密權杖"** 中檢索的**SCIM 終結點**和**訪問權杖**值。 按一下 **"測試連接**"以確保 Azure AD 可以連接到工作網格。 如果連接失敗，請確保您的工作網格帳戶具有管理員許可權，然後重試。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [儲存]****。

8. 在 **"映射**"部分下，選擇**將 Azure 活動目錄使用者同步到工作網格**。

    ![工作網格使用者映射](media/Workgrid-provisioning-tutorial/usermapping.png)

9. 在**屬性對應**部分中查看從 Azure AD 同步到工作網格的使用者屬性。 選擇為 **"匹配屬性"** 的屬性用於與 Workgrid 中的使用者帳戶匹配以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![工作網格使用者屬性](media/Workgrid-provisioning-tutorial/userattribute.png)

10. 在 **"映射**"部分下，選擇**將 Azure 活動目錄組同步到工作網格**

    ![工作網格使用者映射](media/Workgrid-provisioning-tutorial/groupmapping.png)

12. 在**屬性對應**部分中查看從 Azure AD 同步到工作網格的組屬性。 選擇為 **"匹配屬性"** 的屬性用於與 Workgrid 中的使用者帳戶匹配以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![工作網格使用者映射](media/Workgrid-provisioning-tutorial/groupattribute.png)

13. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

14. 要為工作網格啟用 Azure AD 預配服務，在 **"設置"** 部分將**預配狀態**更改為 **"打開**"。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

15. 通過在 **"設置"** 部分中選擇"**範圍"** 中所需的值，定義要預配到工作網格的使用者和/或組。

    ![佈建範圍](common/provisioning-scope.png)

16. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步執行的時間比後續同步長。 有關使用者和/或組預配需要多長時間的詳細資訊，請參閱[預配使用者需要多長時間](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)。

可以使用"**目前狀態**"部分監視進度並遵循指向預配活動報告的連結，該報表描述 Azure AD 預配服務在工作網格上執行的所有操作。 有關詳細資訊，請參閱[檢查使用者預配的狀態](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 要讀取 Azure AD 預配日誌，請參閱[報告自動使用者帳戶預配](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
