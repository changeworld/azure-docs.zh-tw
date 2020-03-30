---
title: 教程：使用 Azure 活動目錄配置智慧表以自動預配使用者 |微軟文檔
description: 瞭解如何將 Azure 活動目錄配置為自動預配和取消向智慧工作表預配使用者帳戶。
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
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: 9fbdf8a1c4b1881fc6dfd9d7b95a4103761e9ce7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063179"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>教程：為自動使用者預配配置智慧表

本教程的目的是演示在智慧工作表和 Azure 活動目錄 （Azure AD） 中執行的步驟，以將 Azure AD 配置為自動預配和取消向智慧工作表預配和取消預配使用者和/或組。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [智慧工作表租戶](https://www.smartsheet.com/pricing)
* 具有系統管理員許可權的智慧工作表企業或企業高級計畫的使用者帳戶。

## <a name="assign-users-to-smartsheet"></a>將使用者分配給智慧工作表

Azure 活動目錄使用稱為*分配*的概念來確定哪些使用者應接收對選定應用的存取權限。 在自動使用者預配的上下文中，只有分配給 Azure AD 中應用程式的使用者和/或組才會同步。

在配置和啟用自動使用者預配之前，應決定 Azure AD 中的哪些使用者和/或組需要訪問智慧表。 一旦確定，您可以按照此處的說明將這些使用者和/或組分配給 Smartsheet：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>將使用者分配給智慧表的重要提示

* 建議將單個 Azure AD 使用者分配給 Smartsheet 以測試自動使用者預配配置。 其他使用者及/或群組可能會稍後再指派。

* 將使用者分配給 Smartsheet 時，必須在分配對話方塊中選擇任何有效的特定于應用程式的角色（如果可用）。 具有**預設存取**角色的使用者會從佈建中排除。

* 為了確保智慧表和 Azure AD 之間的使用者角色指派同位，建議使用完整智慧工作表使用者清單中填充的相同角色指派。 若要從智慧工作表檢索此使用者清單，請導航到**帳戶管理員>使用者管理>更多操作>下載使用者清單 （csv）**。

* 要訪問應用中的某些功能，智慧表要求使用者具有多個角色。 要瞭解有關智慧表中的使用者類型和許可權的更多內容，請訪問[使用者類型和許可權](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions)。

*  如果使用者在 Smartsheet 中分配了多個角色，**則必須**確保在 Azure AD 中複製這些角色指派，以避免使用者可能永久失去對 Smartsheet 物件的存取權限的情況。 智慧工作表中的每個唯一角色**都必須**分配給 Azure AD 中的其他組。 然後 **，必須**將使用者添加到與所需角色對應的每個組。 

## <a name="set-up-smartsheet-for-provisioning"></a>設置用於預配的智慧工作表

在使用 Azure AD 配置智慧表以自動預配使用者之前，需要在智慧工作表上啟用 SCIM 預配。

1. 在**[智慧工作表門戶](https://app.smartsheet.com/b/home)** 中以**SysAdmin**身份登錄，然後導航到**帳戶管理員**。

    ![智慧表帳戶管理員](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. 轉到**安全控制>使用者自動預配>編輯**。

    ![智慧表安全控制](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. 添加和驗證計畫從 Azure AD 預配到智慧表的使用者的電子郵件域。 選擇 **"未啟用"** 可確保所有預配操作僅源自 Azure AD，並確保智慧工作表使用者清單與 Azure AD 分配同步。

    ![智慧表使用者預配](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. 驗證完成後，您必須啟動域。 

    ![智慧工作表啟動域](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. 通過導航到**應用和集成**，生成使用 Azure AD 配置自動使用者預配所需的**金鑰權杖**。

    ![智慧表安裝](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. 選擇**API 訪問**。 按一下 **"生成新的訪問權杖**"。

    ![智慧表安裝](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. 定義 API 訪問權杖的名稱。 按一下 [確定]****。

    ![智慧表安裝](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. 複製 API 訪問權杖並將其保存，因為這將是您唯一可以查看它的時間。 這在 Azure AD 中**的秘密權杖**欄位中是必需的。

    ![智慧工作表權杖](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>從庫中添加智慧工作表

要配置 Smartsheet 以使用 Azure AD 自動預配使用者，需要將 Azure AD 應用程式庫中的智慧表添加到託管 SaaS 應用程式清單中。

1. 在**[Azure 門戶](https://portal.azure.com)** 中，在左側導航面板中，選擇**Azure 活動目錄**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 轉到**企業應用程式**，然後選擇 **"所有應用程式**"。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 要添加新應用程式，請選擇窗格頂部的 **"新建應用程式**"按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜索框中，在"**智慧工作表"** 中輸入"智慧工作表"，在結果面板中選擇 **"智慧工作表**"。 

    ![結果清單中的智慧表](common/search-new-app.png)

5. 選擇 **"智慧工作表註冊**"按鈕，該按鈕將重定向到智慧工作表的登錄頁面。 

    ![智慧表 OIDC 添加](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. 由於智慧表是 OpenIDConnect 應用，因此選擇使用 Microsoft 工作帳戶登錄到智慧工作表。

    ![智慧表 OIDC 登錄](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. 身份驗證成功後，接受同意頁的同意提示。 然後，應用程式將自動添加到您的租戶，您將被重定向到您的智慧工作表帳戶。

    ![智慧表 OIDc 同意](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>配置自動使用者預配到智慧工作表 

本節將指導您完成將 Azure AD 預配服務配置為根據 Azure AD 中的使用者和/或組分配在智慧表中創建、更新和禁用使用者和/或組的步驟。

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>要在 Azure AD 中配置智慧表的自動使用者預配：

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式**，然後選擇**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選擇**智慧工作表**。

    !["應用程式"清單中的智慧工作表連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![預配選項卡](common/provisioning.png)

4. 將**預配模式**設置為 **"自動**"。

    ![預配選項卡](common/provisioning-automatic.png)

5. 在 **"管理認證"** 部分下`https://scim.smartsheet.com/v2/`，在**租戶 URL**中輸入 。 輸入您以前從**機密權杖**中的 Smartsheet 檢索和保存的值。 按一下 **"測試連接**"以確保 Azure AD 可以連接到智慧工作表。 如果連接失敗，請確保您的智慧工作表帳戶具有 SysAdmin 許可權，然後重試。

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [儲存]****。

8. 在 **"映射"** 部分下，選擇**將 Azure 活動目錄使用者同步到智慧工作表**。

    ![智慧工作表使用者映射](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. 在**屬性對應**部分中查看從 Azure AD 同步到智慧表的使用者屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配智慧表中的使用者帳戶以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![智慧工作表使用者屬性](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 要為智慧工作表啟用 Azure AD 預配服務，在 **"設置"** 部分將**預配狀態**更改為 **"打開**"。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 通過在 **"設置"** 部分中選擇"**範圍"** 中所需的值，定義要預配到智慧工作表的使用者和/或組。

    ![佈建範圍](common/provisioning-scope.png)

13. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 可以使用 **"同步詳細資訊"** 部分監視進度並遵循指向預配活動報告的連結，該報表描述 Azure AD 預配服務在智慧工作表上執行的所有操作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

* 智慧工作表不支援虛刪除。 當使用者**的活動**屬性設置為 False 時，智慧表將永久刪除該使用者。

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
