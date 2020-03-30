---
title: 教學課程︰以 Azure Active Directory 設定 Samanage 來自動佈建使用者 | Microsoft Docs
description: 瞭解如何將 Azure 活動目錄配置為自動預配使用者帳戶並將其預配到 Samanage。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988efc2087b3b30e6073bd7f6e2cf08f91fd397c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060425"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>教學課程︰設定 Samanage 來自動佈建使用者

本教程演示了在 Samanage 和 Azure 活動目錄 （Azure AD） 中執行的步驟，以將 Azure AD 配置為自動預配和取消將使用者和組預配到 Samanage。

> [!NOTE]
> 本教程介紹在 Azure AD 使用者預配服務之上構建的連接器。 有關此服務的作用、工作方式以及常見問題的資訊，請參閱[使用 Azure 活動目錄 自動預配和取消預配軟體即服務 （SaaS） 應用程式](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>Prerequisites

本教程中概述的方案假定您具有：

* Azure AD 租用戶。
* 具有專業包的[Samanage 租戶](https://www.samanage.com/pricing/)。
* 具有管理員許可權的 Sa 管理中的使用者帳戶。

> [!NOTE]
> Azure AD 預配集成依賴于[Sa管理休息 API](https://www.samanage.com/api/)。 此 API 可用於具有專業包的帳戶的 Samanage 開發人員。

## <a name="add-samanage-from-the-azure-marketplace"></a>從 Azure 應用商店添加管理

在使用 Azure AD 配置用於自動使用者預配的 Samanage 之前，請將從 Azure 應用商店中的 Samanage 添加到託管 SaaS 應用程式清單中。

要從應用商店添加 Samanage，請按照以下步驟操作。

1. 在[Azure 門戶](https://portal.azure.com)中，在左側的功能窗格中，選擇**Azure 活動目錄**。

    ![Azure 活動目錄圖示](common/select-azuread.png)

2. 轉到**企業應用程式**，然後選擇 **"所有應用程式**"。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請選取對話方塊頂端的 [新增應用程式]****。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜索框中，輸入 **"管理"** 並從結果面板中選擇 **"Sa管理**"。 要添加應用程式，請選擇"**添加**"。

    ![結果清單中的 Samanage](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>將使用者分配給 Samanage

Azure 活動目錄使用稱為*分配*的概念來確定哪些使用者應接收對選定應用的存取權限。 在自動使用者預配的上下文中，只有分配給 Azure AD 中應用程式的使用者或組才會同步。

在配置和啟用自動使用者預配之前，請決定 Azure AD 中的哪些使用者或組需要訪問 Samanage。 要將這些使用者或組分配給 Samanage，請按照將[使用者或組分配給企業應用](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)中的說明進行操作。

### <a name="important-tips-for-assigning-users-to-samanage"></a>將使用者指派給 Samanage 的重要秘訣

*    如今，Samanage 角色會自動動態地填充在 Azure 門戶 UI 中。 在將 Samanage 角色指派給使用者之前，請確保針對 Samanage 完成初始同步，以檢索 Samanage 租戶中的最新角色。

*    我們建議您將單個 Azure AD 使用者分配給 Samanage 以測試初始自動使用者預配配置。 測試成功後，您可以稍後分配其他使用者和組。

*    將使用者分配給 Samanage 時，請在分配對話方塊中選擇任何有效的特定于應用程式的角色（如果可用）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configure-automatic-user-provisioning-to-samanage"></a>將自動使用者預配配置為 Samanage

本節將指導您完成配置 Azure AD 預配服務的步驟。 使用它根據 Azure AD 中的使用者或組分配創建、更新和禁用 Samanage 中的使用者或組。

> [!TIP]
> 您還可以為 Samanage 啟用基於 SAML 的單一登入。 按照[Sa管理單登錄教程](samanage-tutorial.md)中的說明進行操作。 單一登入可以獨立于自動使用者預配進行配置，儘管這兩個功能相輔相成。

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>為 Azure AD 中的 Sa 管理配置自動使用者預配

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式** > **所有應用程式** > **Sa管理**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Samanage] ****。

    ![應用程式清單中的 Sa管理連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![Samanage 佈建](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. 將**預配模式**設置為 **"自動**"。

    ![預配選項卡](common/provisioning-automatic.png)

5. 在 **"管理認證"** 部分下，輸入您的 Samanage**租戶 URL**和**秘密權杖**。 按一下 **"測試連接**"以確保 Azure AD 可以連接到 Samanage。 如果連線失敗，請確定您的 Samanage 帳戶具有管理員權限，然後再試一次。

    ![管理測試連接](./media/samanage-provisioning-tutorial/provisioning.png)

6. 在 **"通知電子郵件"** 框中，輸入接收預配錯誤通知的個人或組的電子郵件地址。 選中"**在發生故障時發送電子郵件通知**"核取方塊。

    ![管理通知電子郵件](./media/samanage-provisioning-tutorial/EmailNotification.png)

7. 選取 [儲存]****。

8. 在 **"映射"** 部分下，選擇**將 Azure 活動目錄使用者同步到 Sa管理**。

    ![管理使用者同步](./media/samanage-provisioning-tutorial/UserMappings.png)

9. 在 [屬性對應]**** 區段中，檢閱從 Azure AD 同步至 Samanage 的使用者屬性。 選取為 [比對]**** 屬性的屬性會用來比對 Samanage 中的使用者帳戶，以進行更新作業。 要保存任何更改，請選擇"**保存**"。

    ![管理匹配的使用者屬性](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

10. 若要啟用群組對應，請在 [對應]**** 區段下方，選取 [將 Azure Active Directory 群組同步處理至 Samanage]****。

    ![Sa管理組同步](./media/samanage-provisioning-tutorial/GroupMappings.png)

11. 將 [已啟用]**** 設定為 [是]****，以同步處理群組。 在 [屬性對應]**** 區段中，檢閱從 Azure AD 同步處理至 Samanage 的群組屬性。 選取為 [比對]**** 屬性的屬性會用來比對 Samanage 中的使用者帳戶，以進行更新作業。 要保存任何更改，請選擇"**保存**"。

    ![管理匹配組屬性](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. 要配置範圍篩選器，請按照[範圍界定篩選器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的說明進行操作。

13. 要為 Samanage 啟用 Azure AD 預配服務，在 **"設置"** 部分中，將**預配狀態**更改為**On**。

    ![管理預配狀態](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

14. 定義要預配到 Samanage 的使用者或組。 在 **"設置"** 部分中，在 **"範圍**"中選擇所需的值。 選擇 **"同步所有使用者和組**"選項時，請考慮以下"連接器限制"部分中所述的限制。

    ![管理範圍](./media/samanage-provisioning-tutorial/ScopeSync.png)

15. 準備好預配時，請選擇"**保存**"。

    ![管理保存](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


此操作將開始**在"設置"** 部分中定義"**範圍"** 中定義的所有使用者或組的初始同步。 初始同步執行的時間比以後同步長。 只要 Azure AD 預配服務運行，它們大約每 40 分鐘發生一次。 

您可以使用 **"同步詳細資訊"** 部分監視進度並關注指向預配活動報告的連結。 該報告描述了 Azure AD 預配服務在 Samanage 上執行的所有操作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

如果選擇 **"同步所有使用者和組**"選項並為 SaManage**角色**屬性配置值，**則"預設值"下的值（可選）"** 框必須以以下格式表示：

- {"顯示名稱"："角色"，其中角色是您想要的預設值。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
