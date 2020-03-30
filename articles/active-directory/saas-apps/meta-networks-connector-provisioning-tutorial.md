---
title: 教程：配置元網路連接器，以便使用 Azure 活動目錄進行自動使用者預配 |微軟文檔
description: 瞭解如何將 Azure 活動目錄配置為自動預配和取消將使用者帳戶預配到元網路連接器。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 6e34e540-7761-4bdf-a2f9-8c12becc119d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: 03c2dc6253fba5c2c7d59f3aefc5c1c663ed8248
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061351"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>教程：配置用於自動使用者預配的元網路連接器

本教程的目的是演示在元網路連接器和 Azure 活動目錄 （Azure AD） 中執行的步驟，以將 Azure AD 配置為自動預配和取消向元網路連接器預配和取消預配使用者和/或組。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [元網路連接器租戶](https://www.metanetworks.com/)
* 具有管理員許可權的 Meta 網路連接器中的使用者帳戶。

## <a name="assigning-users-to-meta-networks-connector"></a>將使用者分配給元網路連接器

Azure 活動目錄使用稱為*分配*的概念來確定哪些使用者應接收對選定應用的存取權限。 在自動使用者預配的上下文中，只有分配給 Azure AD 中應用程式的使用者和/或組才會同步。

在配置和啟用自動使用者預配之前，應確定 Azure AD 中的哪些使用者和/或組需要訪問元網路連接器。 決定後，您可以按照此處的說明將這些使用者和/或組分配給元網路連接器：
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>將使用者分配給元網路連接器的重要提示

* 建議將單個 Azure AD 使用者分配給元網路連接器以測試自動使用者預配配置。 其他使用者及/或群組可能會稍後再指派。

* 將使用者分配給 Meta Networks 連接器時，必須在分配對話方塊中選擇任何有效的特定于應用程式的角色（如果可用）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="setup-meta-networks-connector-for-provisioning"></a>設置用於預配的元網路連接器

1. 使用組織名稱登錄到[元網路連接器管理主控台](https://login.metanetworks.com/login/)。 導航到**管理> API 金鑰**。

    ![元網路連接器管理主控台](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  按一下螢幕右上角的加號以創建新的**API 金鑰**。

    ![元網路連接器加圖示](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  設置**API 金鑰名稱**和**API 金鑰描述**。

    ![元網路連接器創建權杖](media/meta-networks-connector-provisioning-tutorial/keyname.png)

4.  打開**組**和**使用者的****寫入**許可權。

    ![元網路連接器許可權](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  按一下"**添加**"。 複製**SECRET**並保存它，因為這將是您唯一可以查看它的時間。 此值將在 Azure 門戶中的 Meta Networks 連接器應用程式的預配選項卡中的"機密權杖"欄位中輸入。

    ![元網路連接器創建權杖](media/meta-networks-connector-provisioning-tutorial/token.png)

6.  通過將**IdP >創建新 的"管理設置"導航到管理>設置 >，添加 IdP。**

    ![元網路連接器添加 IdP](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  在**IdP 配置**頁中，您可以**命名**IdP 配置並選擇**圖示**。

    ![元網路連接器 IdP 名稱](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![元網路連接器 IdP 圖示](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  在 **"配置 SCIM"** 下，選擇在前面的步驟中創建的 API 金鑰名稱。 按一下 [ **儲存**]。

    ![元網路連接器配置 SCIM](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  導航到 **"管理>設置> IdP 選項卡**。按一下在前面的步驟中創建的 IdP 配置的名稱以查看**IdP ID**。 此**ID**添加到**租戶 URL**的末尾，同時在 Azure 門戶中的 Meta Networks 連接器應用程式的預配選項卡中輸入租戶**URL**欄位中的值。

    ![元網路連接器 IDP ID](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>從庫中添加元網路連接器

在配置元網路連接器以使用 Azure AD 進行自動使用者預配之前，需要將 Azure AD 應用程式庫中的元網路連接器添加到託管 SaaS 應用程式清單中。

**要從 Azure AD 應用程式庫添加元網路連接器，請執行以下步驟：**

1. 在**[Azure 門戶](https://portal.azure.com)** 中，在左側導航面板中，選擇**Azure 活動目錄**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 轉到**企業應用程式**，然後選擇 **"所有應用程式**"。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 要添加新應用程式，請選擇窗格頂部的 **"新建應用程式**"按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜索框中，輸入**元網路連接器**，在結果面板中選擇**元網路連接器**，然後按一下 **"添加**"按鈕以添加應用程式。

    ![結果清單中的 Meta Networks Connector](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>配置自動使用者預配到元網路連接器 

本節將指導您完成將 Azure AD 預配服務配置為根據 Azure AD 中的使用者和/或組分配在元網路連接器中創建、更新和禁用使用者和/或組的步驟。

> [!TIP]
> 您也可以根據[元網路連接器單一登入教程](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial)中提供的說明，選擇啟用基於 SAML 的單一登入元網路連接器。 單一登入可以獨立于自動使用者預配進行配置，儘管這兩個功能相互補充

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>要在 Azure AD 中配置元網路連接器的自動使用者預配：

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式**，然後選擇**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Meta Networks Connector]****。

    ![應用程式清單中的 Meta Networks Connector 連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![預配選項卡](common/provisioning.png)

4. 將**預配模式**設置為 **"自動**"。

    ![預配選項卡](common/provisioning-automatic.png)

5. 在 **"管理認證"** 部分下`https://api.metanetworks.com/v1/scim/<IdP ID>`，在**租戶 URL**中輸入 。 輸入在**秘密權杖**中檢索到的**SCIM 身份驗證權杖**值。 按一下 **"測試連接**"以確保 Azure AD 可以連接到元網路連接器。 如果連接失敗，請確保您的 Meta 網路連接器帳戶具有管理員許可權，然後重試。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [儲存]****。

8. 在 **"映射**"部分下，選擇**將 Azure 活動目錄使用者同步到元網路連接器**。

    ![元網路連接器使用者映射](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. 在**屬性對應**部分中查看從 Azure AD 同步到元網路連接器的使用者屬性。 選擇為 **"匹配屬性"** 的屬性用於與 Meta Networks 連接器中的使用者帳戶匹配以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![元網路連接器使用者屬性](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. 在 **"映射**"部分下，選擇**將 Azure 活動目錄組同步到元網路連接器**。

    ![元網路連接器組映射](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. 在**屬性對應**部分中查看從 Azure AD 同步到元網路連接器的組屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 Meta Networks 連接器中的組以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![元網路連接器組屬性](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 要為 Meta Networks 連接器啟用 Azure AD 預配服務，請在 **"設置"** 部分將**預配狀態**更改為 **"打開**"。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 通過在 **"設置"** 部分中選擇"**範圍"** 中所需的值，定義要預配到元網路連接器的使用者和/或組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 可以使用 **"同步詳細資訊"** 部分監視進度並跟蹤指向預配活動報告的連結，該報表描述 Azure AD 預配服務在 Meta Networks 連接器上執行的所有操作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

