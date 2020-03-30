---
title: 教學課程︰以 Azure Active Directory 設定 Zendesk 來自動佈建使用者 | Microsoft Docs
description: 瞭解如何將 Azure 活動目錄配置為自動預配和取消將使用者帳戶預配到 Zendesk。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 01d5e4d5-d856-42c4-a504-96fa554baf66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a480119ee88521b920be88669f6d80e3754d24d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062735"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>教學課程︰設定 Zendesk 來自動佈建使用者

本教程演示了在 Zendesk 和 Azure 活動目錄 （Azure AD） 中執行的步驟，以將 Azure AD 配置為自動預配和取消將使用者和組預配到 Zendesk。

> [!NOTE]
> 本教程介紹在 Azure AD 使用者預配服務之上構建的連接器。 有關此服務的作用、工作方式以及常見問題的資訊，請參閱[使用 Azure 活動目錄 自動預配和取消預配軟體即服務 （SaaS） 應用程式](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>Prerequisites

本教程中概述的方案假定您具有：

* Azure AD 租用戶。
* 具有專業計畫或啟用更好的 Zendesk 租戶。
* Zendesk 中的具有管理員許可權的使用者帳戶。

## <a name="add-zendesk-from-the-azure-marketplace"></a>從 Azure 應用商店添加 Zendesk

在配置 Zendesk 以使用 Azure AD 進行自動使用者預配之前，請將 Zendesk 從 Azure 應用商店添加到託管 SaaS 應用程式清單中。

要從應用商店中添加 Zendesk，請按照以下步驟操作。

1. 在[Azure 門戶](https://portal.azure.com)中，在左側的功能窗格中，選擇**Azure 活動目錄**。

    ![Azure 活動目錄圖示](common/select-azuread.png)

2. 轉到**企業應用程式**，然後選擇 **"所有應用程式**"。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請選取對話方塊頂端的 [新增應用程式]****。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜索框中，輸入**Zendesk**並從結果面板中選擇 **"Zendesk"。** 要添加應用程式，請選擇"**添加**"。

    ![結果清單中的 Zendesk](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>將使用者分配給 Zendesk

Azure 活動目錄使用稱為*分配*的概念來確定哪些使用者應接收對選定應用的存取權限。 在自動使用者預配的上下文中，只有分配給 Azure AD 中應用程式的使用者或組才會同步。

在配置和啟用自動使用者預配之前，請決定 Azure AD 中的哪些使用者或組需要訪問 Zendesk。 要將這些使用者或組分配給 Zendesk，請按照將[使用者或組分配給企業應用](../manage-apps/assign-user-or-group-access-portal.md)中的說明進行操作。

### <a name="important-tips-for-assigning-users-to-zendesk"></a>將使用者指派給 Zendesk 的重要秘訣

* 如今，Zendesk 角色會自動動態地填充在 Azure 門戶 UI 中。 在將 Zendesk 角色指派給使用者之前，請確保針對 Zendesk 完成初始同步，以檢索 Zendesk 租戶中的最新角色。

* 我們建議您將單個 Azure AD 使用者分配給 Zendesk 以測試初始自動使用者預配配置。 測試成功後，您可以稍後分配其他使用者或組。

* 將使用者分配給 Zendesk 時，請在分配對話方塊中選擇任何有效的特定于應用程式的角色（如果可用）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>配置自動使用者預配到 Zendesk 

本節將指導您完成配置 Azure AD 預配服務的步驟。 使用它根據 Azure AD 中的使用者或組分配在 Zendesk 中創建、更新和禁用使用者或組。

> [!TIP]
> 您還可以為 Zendesk 啟用基於 SAML 的單一登入。 按照[Zendesk 單一登入教程](zendesk-tutorial.md)中的說明進行操作。 單一登入可以獨立于自動使用者預配進行配置，儘管這兩個功能相輔相成。

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>在 Azure AD 中為 Zendesk 配置自動使用者預配

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式** > **所有應用程式** > **Zendesk**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Zendesk]****。

    ![應用程式清單中的 Zendesk 連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![Zendesk 佈建](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. 將**預配模式**設置為 **"自動**"。

    ![Zendesk 預配模式](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. 在 **"管理員憑據"** 部分下，輸入 Zendesk 帳戶的管理員使用者名、機密權杖和域。 這些值的範例如下：

   * 在 **"管理員使用者名"** 框中，填寫 Zendesk 租戶上管理員帳戶的使用者名。 例如 admin@contoso.com。

   * 在 **"機密權杖"** 框中，填寫步驟 6 中所述的秘密權杖。

   * 在 **"域"** 框中，填寫 Zendesk 租戶的子域。 例如，對於租戶 URL 的`https://my-tenant.zendesk.com`帳戶，您的子域是我的**租戶**。

6. Zendesk 帳戶的秘密權杖位於**管理** > **API** > **設置**中。 確保**權杖訪問**設置為**已啟用**。

    ![Zendesk 管理設置](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Zendesk 秘密權杖](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. 填寫步驟 5 中所示的框後，選擇 **"測試連接**"以確保 Azure AD 可以連接到 Zendesk。 如果連接失敗，請確保您的 Zendesk 帳戶具有管理員許可權，然後重試。

    ![Zendesk 測試連接](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. 在 **"通知電子郵件"** 框中，輸入接收預配錯誤通知的個人或組的電子郵件地址。 選中"**在發生故障時發送電子郵件通知**"核取方塊。

    ![Zendesk 通知電子郵件](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. 選取 [儲存]****。

10. 在 [對應]**** 區段中，選取 [同步處理 Azure Active Directory 使用者至 Zendesk]****。

    ![Zendesk 使用者同步](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. 在**屬性對應**部分中查看從 Azure AD 同步到 Zendesk 的使用者屬性。 選取為 [比對]**** 屬性的屬性會用來比對 Zendesk 中的使用者帳戶以進行更新作業。 要保存任何更改，請選擇"**保存**"。

    ![Zendesk 匹配使用者屬性](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. 在 **"映射**"部分下，選擇**將 Azure 活動目錄組同步到 Zendesk**。

    ![Zendesk 組同步](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. 在**屬性對應**部分中查看從 Azure AD 同步到 Zendesk 的組屬性。 選取為 [比對]**** 屬性的屬性會用來比對 Zendesk 中的群組以進行更新作業。 要保存任何更改，請選擇"**保存**"。

    ![Zendesk 匹配組屬性](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. 要配置範圍篩選器，請按照[範圍界定篩選器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的說明進行操作。

15. 要為 Zendesk 啟用 Azure AD 預配服務，在 **"設置"** 部分中，將**預配狀態**更改為 **"打開**"。

    ![Zendesk 預配狀態](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. 定義要預配到 Zendesk 的使用者或組。 在 **"設置"** 部分中，在 **"範圍**"中選擇所需的值。

    ![Zendesk 範圍](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. 準備好預配時，請選擇"**保存**"。

    ![Zendesk 保存](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

此操作將開始**在"設置"** 部分中定義"**範圍"** 中定義的所有使用者或組的初始同步。 初始同步執行的時間比以後同步長。 只要 Azure AD 預配服務運行，它們大約每 40 分鐘發生一次。 

您可以使用 **"同步詳細資訊"** 部分監視進度並關注指向預配活動報告的連結。 該報告描述了 Azure AD 預配服務在 Zendesk 上執行的所有操作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

* Zendesk 支援僅對具有**代理**角色的使用者使用組。 有關詳細資訊，請參閱[Zendesk 文檔](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups)。

* 將自訂角色指派給使用者或組時，Azure AD 自動使用者預配服務還會分配預設角色**代理**。 只有代理程式才能指派自訂角色。 有關詳細資訊，請參閱[Zendesk API 文檔](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests)。 

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
