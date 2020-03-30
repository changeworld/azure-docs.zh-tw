---
title: 教程：配置保管人密碼管理器&數位保存庫，以便使用 Azure 活動目錄自動預配使用者 |微軟文檔
description: 瞭解如何將 Azure 活動目錄配置為自動預配和取消將使用者帳戶預配到保管人密碼管理器&數位保存庫。
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
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 236527a9889879f872ef8c3867a7ec3c1b1ba0a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057500"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>教程：為自動使用者預配配置保管員密碼管理器&數位保存庫

本教程的目的是演示在"守護者密碼管理器&數位保存庫和 Azure 活動目錄 （Azure AD） 中執行的步驟，以將 Azure AD 配置為自動預配和取消向保管人密碼管理器&數位保存庫預配和取消預配使用者和/或組。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [保管人密碼管理器&數位保存庫租戶](https://keepersecurity.com/pricing.html?t=e)
* 管理員密碼管理器中的使用者帳戶&具有管理員許可權的數位保存庫。

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>從庫中添加保管員密碼管理器&數位保存庫

在將保管人密碼管理器&數位保存庫配置為使用 Azure AD 進行自動使用者預配之前，需要將 Keeper 密碼管理器& Azure AD 應用程式庫中的數位保存庫添加到託管 SaaS 應用程式清單中。

**要從 Azure AD 應用程式庫添加保管人密碼管理器&數位保存庫，請執行以下步驟：**

1. 在**[Azure 門戶](https://portal.azure.com)** 中，在左側導航面板中，選擇**Azure 活動目錄**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 轉到**企業應用程式**，然後選擇 **"所有應用程式**"。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 要添加新應用程式，請選擇窗格頂部的 **"新建應用程式**"按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜索框中，在"**數位保存庫"&輸入"保管人密碼管理器**"，在結果面板中選擇 **"保管人密碼管理器&數位保存庫**"，然後按一下"**添加**"按鈕以添加應用程式。

    ![結果清單中的 Keeper Password Manager & Digital Vault](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>將使用者分配給保管員密碼管理器&數位保存庫

Azure 活動目錄使用稱為*分配*的概念來確定哪些使用者應接收對選定應用的存取權限。 在自動使用者預配的上下文中，只有分配給 Azure AD 中應用程式的使用者和/或組才會同步。

在配置和啟用自動使用者預配之前，應決定 Azure AD 中的哪些使用者和/或組需要訪問 Keeper 密碼管理器&數位保存庫。 一旦確定，您可以按照此處的說明將這些使用者和/或組分配給管理員密碼管理器&數位保存庫：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>將使用者分配給 &數位保存庫的管理員密碼管理器的重要提示

* 建議將單個 Azure AD 使用者分配給保管人密碼管理器&數位保存庫以測試自動使用者預配配置。 其他使用者及/或群組可能會稍後再指派。

* 將使用者分配給 &數位保存庫的保管人密碼管理器時，必須在分配對話方塊中選擇任何有效的特定于應用程式的角色（如果可用）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>將自動使用者預配配置配置給保管人密碼管理器&數位保存庫 

本節將指導您完成將 Azure AD 預配服務配置為根據 Azure AD 中的使用者和/或組分配在 Keeper 密碼管理器&數位保存庫中創建、更新和禁用使用者和/或組的步驟。

> [!TIP]
> 您也可以根據["保管人密碼管理器&數位保存庫單一登入教程](keeperpasswordmanager-tutorial.md)"中提供的說明，選擇啟用基於 SAML 的單一登入狀態的保管人密碼管理器&數位保存庫。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>要為 Azure AD 中的保管人密碼管理器&數位保存庫配置自動使用者預配：

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式**，然後選擇**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 **Keeper Password Manager & Digital Vault**。

    ![應用程式清單中的 Keeper Password Manager & Digital Vault 連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![預配選項卡](common/provisioning.png)

4. 將**預配模式**設置為 **"自動**"。

    ![預配選項卡](common/provisioning-automatic.png)

5. 在 **"管理員憑據"** 部分下，輸入管理員密碼管理器的**租戶 URL**和**機密權杖**&數位保存庫帳戶，如步驟 6 所述。

6. 登錄到[您的管理員管理主控台](https://keepersecurity.com/console/#login)。 按一下 **"管理員"** 並選擇現有節點或創建新節點。 導航到**預配**選項卡，然後選擇 **"添加方法**"。

    ![管理員管理主控台](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    選擇**SCIM（跨域標識管理系統**）。

    ![守護者添加 SCIM](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    按一下 **"創建預配權杖**"。

    ![保管人創建終結點](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    複製**URL**和**權杖**的值，並將其粘貼到 Azure AD 中的**租戶 URL**和**秘密權杖**中。 按一下 **"保存**"以完成 Keeper 上的預配設置。

    ![保管人創建權杖](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. 填充步驟 5 中顯示的欄位後，按一下 **"測試連接**"以確保 Azure AD 可以連接到 &數位保存庫的保管人密碼管理器。 如果連接失敗，請確保您的保管人密碼管理器&數位保存庫帳戶具有管理員許可權，然後重試。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

8. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

9. 按一下 [儲存]****。

10. 在 **"映射**"部分下，選擇**將 Azure 活動目錄使用者同步到"數位保存庫"&管理員密碼管理器**。

    ![管理員使用者映射](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. 在**屬性對應**部分中查看從 Azure AD 同步到保管人密碼管理器&數位保存庫的使用者屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 Keeper 密碼管理器&數位保存庫中的使用者帳戶以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![管理員使用者屬性](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. 在 **"映射**"部分下，選擇**將 Azure 活動目錄組同步到"數位保存庫"&管理員密碼管理器**。

    ![保管人組映射](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. 在**屬性對應**部分中查看從 Azure AD 同步到保管人密碼管理器&數位保存庫的組屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 Keeper 密碼管理器&數位保存庫中的組以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![保管人組屬性](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

15. 要為保管人密碼管理器啟用 Azure AD 預配服務&數位保存庫，請將 **"設置"** 部分中的**預配狀態**更改為 **"打開**"。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

16. 通過在 **"設置"** 部分中選擇"**範圍"** 中所需的值，定義要預配給保管人密碼管理器&數位保存庫的使用者和/或組。

    ![佈建範圍](common/provisioning-scope.png)

17. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 可以使用 **"同步詳細資訊"** 部分監視進度並關注指向預配活動報告的連結，該報表描述 Azure AD 預配服務在 Keeper 密碼管理器 &數位保存庫上執行的所有操作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

* &數位保存庫的保管人密碼管理器要求**電子郵件**和**使用者名**具有相同的源值，因為對任一屬性的任何更新都將修改其他值。
* 保管員密碼管理器&數位保存庫不支援使用者刪除，僅禁用。 已禁用的使用者將顯示在管理員管理主控台 UI 中鎖定。

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

