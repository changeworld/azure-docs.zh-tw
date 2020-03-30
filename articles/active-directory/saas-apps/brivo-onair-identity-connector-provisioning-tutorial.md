---
title: 教程：配置 Brivo Onair 標識連接器，以便使用 Azure 活動目錄自動預配使用者 |微軟文檔
description: 瞭解如何將 Azure 活動目錄配置為自動預配和取消將使用者帳戶預配到 Brivo Onair 標識連接器。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 542ce04c-ef7d-4154-9b0e-7f68e1154f6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: f3c1e7337c0ce07b7fbebb9f954deeb75f0b9584
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246648"
---
# <a name="tutorial-configure-brivo-onair-identity-connector-for-automatic-user-provisioning"></a>教程：配置 Brivo Onair 標識連接器，以便自動進行使用者預配

本教程的目的是演示將在 Brivo Onair 標識連接器和 Azure 活動目錄 （Azure AD） 中執行的步驟，以將 Azure AD 配置為自動預配和取消將使用者和/或組預配到 Brivo Onair 標識連接器。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [布裡沃 onair 標識連接器租戶](https://www.brivo.com/lp/quote)
* Brivo Onair 標識連接器中的使用者帳戶，具有高級管理員許可權。

## <a name="assigning-users-to-brivo-onair-identity-connector"></a>將使用者分配給 Brivo Onair 標識連接器

Azure 活動目錄使用稱為*分配*的概念來確定哪些使用者應接收對選定應用的存取權限。 在自動使用者預配的上下文中，只有分配給 Azure AD 中應用程式的使用者和/或組才會同步。

在配置和啟用自動使用者預配之前，應確定 Azure AD 中的哪些使用者和/或組需要訪問 Brivo Onair 標識連接器。 一旦確定，您可以按照此處的說明將這些使用者和/或組分配給 Brivo Onair 標識連接器：
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-brivo-onair-identity-connector"></a>將使用者分配給 Brivo Onair 標識連接器的重要提示

* 建議將單個 Azure AD 使用者分配給 Brivo Onair 標識連接器以測試自動使用者預配配置。 其他使用者及/或群組可能會稍後再指派。

* 將使用者分配給 Brivo Onair 標識連接器時，必須在分配對話方塊中選擇任何有效的特定于應用程式的角色（如果可用）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="setup-brivo-onair-identity-connector-for-provisioning"></a>設置用於預配的 Brivo Onair 標識連接器

1.    登錄到您的[Brivo Onair 身份連接器管理主控台](https://acs.brivo.com/login/)。 導航到**帳戶>帳戶設置**。

    ![布裡沃 Onair 身份連接器管理主控台](media/brivo-onair-identity-connector-provisioning-tutorial/admin.png)

2.  按一下**Azure AD**選項卡。在**Azure AD**詳細資訊頁上重新輸入高級管理員帳戶的密碼。 按一下"**提交**"。

    ![布裡沃 onair 標識連接器蔚藍](media/brivo-onair-identity-connector-provisioning-tutorial/azuread.png)

3.    按一下**複製權杖**按鈕並保存**秘密權杖**。 此值將在 Azure 門戶中的 Brivo Onair 標識連接器應用程式的"預配"選項卡中的"機密權杖"欄位中輸入。

    ![布裡沃 Onair 標識連接器權杖](media/brivo-onair-identity-connector-provisioning-tutorial/token.png)

## <a name="add-brivo-onair-identity-connector-from-the-gallery"></a>從庫中添加 Brivo Onair 標識連接器

在配置 Brivo Onair 標識連接器以使用 Azure AD 進行自動使用者預配之前，需要將 Azure AD 應用程式庫中的 Brivo Onair 標識連接器添加到託管 SaaS 應用程式清單中。

**要從 Azure AD 應用程式庫添加 Brivo Onair 標識連接器，請執行以下步驟：**

1. 在**[Azure 門戶](https://portal.azure.com)** 中，在左側導航面板中，選擇**Azure 活動目錄**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 轉到**企業應用程式**，然後選擇 **"所有應用程式**"。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 要添加新應用程式，請選擇窗格頂部的 **"新建應用程式**"按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜索框中，輸入**Brivo Onair 標識連接器**，在結果面板中選擇**Brivo Onair 標識連接器**，然後按一下"**添加**"按鈕以添加應用程式。

    ![結果清單中的 Brivo Onair 標識連接器](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-brivo-onair-identity-connector"></a>將自動使用者預配配置配置到 Brivo Onair 標識連接器 

本節將指導您完成將 Azure AD 預配服務配置為根據 Azure AD 中的使用者和/或組分配在 Brivo Onair 標識連接器中創建、更新和禁用使用者和/或組的步驟。

### <a name="to-configure-automatic-user-provisioning-for-brivo-onair-identity-connector-in-azure-ad"></a>要在 Azure AD 中為 Brivo Onair 標識連接器配置自動使用者預配：

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式**，然後選擇**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選擇**Brivo Onair 標識連接器**。

    ![應用程式清單中的 Brivo Onair 標識連接器連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![預配選項卡](common/provisioning.png)

4. 將**預配模式**設置為 **"自動**"。

    ![預配選項卡](common/provisioning-automatic.png)

5. 在 **"管理認證"** 部分下`https://scim.brivo.com/ActiveDirectory/v2/`，在**租戶 URL**中輸入 。 輸入在**秘密權杖**中檢索到的**SCIM 身份驗證權杖**值。 按一下 **"測試連接**"以確保 Azure AD 可以連接到 Brivo Onair 標識連接器。 如果連接失敗，請確保您的 Brivo Onair 標識連接器帳戶具有管理員許可權，然後重試。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [儲存]****。

8. 在 **"映射**"部分下，選擇**將 Azure 活動目錄使用者同步到 Brivo Onair 標識連接器**。

    ![布裡沃 onair 標識連接器使用者映射](media/brivo-onair-identity-connector-provisioning-tutorial/user-mappings.png )

9. 在**屬性對應**部分中查看從 Azure AD 同步到 Brivo Onair 標識連接器的使用者屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 Brivo Onair 標識連接器中的使用者帳戶以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![布裡沃 onair 標識連接器使用者屬性](media/brivo-onair-identity-connector-provisioning-tutorial/user-attributes.png)

10. 在 **"映射**"部分下，選擇**將 Azure 活動目錄組同步到 Brivo Onair 標識連接器**。

    ![布裡沃 Onair 標識連接器組映射](media/brivo-onair-identity-connector-provisioning-tutorial/group-mappings.png)

11. 查看**屬性對應**部分中從 Azure AD 同步到 Brivo Onair 標識連接器的組屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 Brivo Onair 標識連接器中的組以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![布裡沃 onair 標識連接器組屬性](media/brivo-onair-identity-connector-provisioning-tutorial/group-attributes.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 要為 Brivo Onair 標識連接器啟用 Azure AD 預配服務，請在 **"設置"** 部分將**預配狀態**更改為 **"打開**"。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 通過在 **"設置"** 部分中選擇"**範圍"** 中所需的值，定義要預配到 Brivo Onair 標識連接器的使用者和/或組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 可以使用 **"同步詳細資訊"** 部分監視進度並關注指向預配活動報告的連結，該報表描述由 Brivo Onair 標識連接器上 Azure AD 預配服務執行的所有操作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

