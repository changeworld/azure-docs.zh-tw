---
title: 教學課程︰以 Azure Active Directory 設定 Tableau Online 來自動佈建使用者 | Microsoft Docs
description: 瞭解如何將 Azure 活動目錄配置為自動預配和取消將使用者帳戶預配到 Tableau Online。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0be9c435-f9a1-484d-8059-e578d5797d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fae770950810899f7c6583fa401110c3e85022b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064202"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>教學課程︰設定 Tableau Online 來自動佈建使用者

本教程演示了在 Tableau 連線目錄和 Azure 活動目錄 （Azure AD） 中執行的步驟，以將 Azure AD 配置為自動預配和取消將使用者和組預配到 Tableau Online。

> [!NOTE]
> 本教程介紹在 Azure AD 使用者預配服務之上構建的連接器。 有關此服務的作用、工作方式以及常見問題的資訊，請參閱[使用 Azure 活動目錄 自動預配和取消預配軟體即服務 （SaaS） 應用程式](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>Prerequisites

本教程中概述的方案假定您具有：

*   Azure AD 租用戶。
*   [Tableau 線上租戶](https://www.tableau.com/)。
*   Tableau Online 中的具有管理員許可權的使用者帳戶。

> [!NOTE]
> Azure AD 預配集成依賴于[Tableau 線上休息 API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm)。 此 API 可供 Tableau 線上開發人員使用。

## <a name="add-tableau-online-from-the-azure-marketplace"></a>從 Azure 應用商店連線添加 Tableau
在為使用 Azure AD 自動使用者預配配置之前，請將 Tableau Online 從 Azure 應用商店添加到託管 SaaS 應用程式清單中。

要從應用商店添加 Tableau 連線，請按照以下步驟操作。

1. 在[Azure 門戶](https://portal.azure.com)中，在左側的功能窗格中，選擇**Azure 活動目錄**。

    ![Azure 活動目錄圖示](common/select-azuread.png)

2. 轉到**企業應用程式**，然後選擇 **"所有應用程式**"。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請選取對話方塊頂端的 [新增應用程式]****。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜索框中，輸入**Tableau 連線**，並從結果面板中選擇**Tableau 連線**。 要添加應用程式，請選擇"**添加**"。

    ![結果清單中的 Tableau Online](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>將使用者分配到 Tableau 連線

Azure 活動目錄使用稱為*分配*的概念來確定哪些使用者應接收對選定應用的存取權限。 在自動使用者預配的上下文中，只有分配給 Azure AD 中應用程式的使用者或組才會同步。

在配置和啟用自動使用者預配之前，請決定 Azure AD 中的哪些使用者或組需要訪問 Tableau Online。 要將這些使用者或組分配給 Tableau Online，請按照將[使用者或組分配給企業應用](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)中的說明進行操作。

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>將使用者指派給 Tableau Online 的重要秘訣

*   我們建議您將單個 Azure AD 使用者分配給 Tableau Online 以測試自動使用者預配配置。 您可以稍後分配其他使用者或組。

*   將使用者分配給 Tableau Online 時，請在分配對話方塊中選擇任何有效的特定于應用程式的角色（如果可用）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>配置自動使用者預配到 Tableau 線上

本節將指導您完成配置 Azure AD 預配服務的步驟。 使用它根據 Azure AD 中的使用者或組分配在 Tableau Online 中創建、更新和禁用使用者或組。

> [!TIP]
> 您還可以為 Tableau Online 啟用基於 SAML 的單一登入。 按照[Tableau 線上單一登入教程](tableauonline-tutorial.md)中的說明進行操作。 單一登入可以獨立于自動使用者預配進行配置，儘管這兩個功能相輔相成。

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>在 Azure AD 中為 Tableau 連線配置自動使用者預配

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式** > **所有應用程式** > **Tableau 線上**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Tableau Online] ****。

    ![應用程式清單中的 Tableau 連線連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![Tableau Online 佈建](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. 將**預配模式**設置為 **"自動**"。

    ![Tableau 線上預配模式](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. 在 **"管理員憑據"** 部分下，輸入 Tableau Online 帳戶的域、管理員使用者名、管理員密碼和內容 URL：

   * 在 **"域"** 框中，根據步驟 6 填寫子域。

   * 在 **"管理員使用者名"** 框中，填寫 Clarizen 租戶上管理員帳戶的使用者名。 例如 admin@contoso.com。

   * 在 **"管理員密碼"** 框中，填寫與管理員使用者名對應的管理員帳戶的密碼。

   * 在 **"內容 URL"** 框中，根據步驟 6 填寫子域。

6. 登錄到 Tableau Online 的管理帳戶後，可以從管理頁面的 URL 獲取**域**和**內容 URL**的值。

    * Tableau Online 帳戶的**網域**可以從 URL 的這個部分複製：

        ![Tableau 線上域](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * 可以從此部分複製 Tableau Online 帳戶**的內容 URL。** 它是在帳戶設置期間定義的值。 在此範例中，值是 "contoso"：

        ![Tableau 線上內容 URL](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > 您的**域**可能與此處顯示的域不同。

7. 填寫步驟 5 中所示的框後，選擇 **"測試連接**"以確保 Azure AD 可以連接到 Tableau Online。 如果連接失敗，請確保您的 Tableau Online 帳戶具有管理員許可權，然後重試。

    ![Tableau 線上測試連接](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. 在 **"通知電子郵件"** 框中，輸入接收預配錯誤通知的個人或組的電子郵件地址。 選中"**在發生故障時發送電子郵件通知**"核取方塊。

    ![Tableau 線上通知電子郵件](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. 選取 [儲存]****。

10. 在 [對應]**** 區段中，選取 [將 Azure Active Directory 使用者同步至 Tableau]****。

    ![Tableau 線上使用者同步](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. 在 **"屬性對應**"部分中查看從 Azure AD 同步到 Tableau 連線的使用者屬性。 選取為 [比對]**** 屬性的屬性會用來比對 Tableau Online 中的使用者帳戶以進行更新作業。 要保存任何更改，請選擇"**保存**"。

    ![Tableau 線上匹配使用者屬性](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. 在 [對應]**** 區段中，選取 [同步處理 Azure Active Directory 群組至 Tableau]****。

    ![Tableau 連線組同步](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. 在 **"屬性對應**"部分中查看從 Azure AD 同步到 Tableau 連線的組屬性。 選取為 [比對]**** 屬性的屬性會用來比對 Tableau Online 中的使用者帳戶以進行更新作業。 要保存任何更改，請選擇"**保存**"。

    ![Tableau 連線匹配組屬性](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. 要配置範圍篩選器，請按照[範圍界定篩選器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的說明進行操作。

15. 要為 Tableau Online 啟用 Azure AD 預配服務，在 **"設置"** 部分中，將**預配狀態**更改為**On**。

    ![Tableau 線上預配狀態](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. 定義要預配到 Tableau Online 的使用者或組。 在 **"設置"** 部分中，在 **"範圍**"中選擇所需的值。

    ![Tableau 線上範圍](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. 準備好預配時，請選擇"**保存**"。

    ![Tableau 線上保存](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

此操作將開始**在"設置"** 部分中定義"**範圍"** 中定義的所有使用者或組的初始同步。 初始同步執行的時間比以後同步長。 只要 Azure AD 預配服務運行，它們大約每 40 分鐘發生一次。 

您可以使用 **"同步詳細資訊"** 部分監視進度並關注指向預配活動報告的連結。 該報告描述了 Azure AD 預配服務在 Tableau Online 上執行的所有操作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
