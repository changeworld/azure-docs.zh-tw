---
title: 教學課程︰以 Azure Active Directory 設定 Cornerstone OnDemand 來自動佈建使用者 | Microsoft Docs
description: 瞭解如何將 Azure 活動目錄配置為自動預配使用者帳戶並將其預配到 Cornerstone OnDemand。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6212e74ecbf8327d3939138de2e92868f29b0f1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058415"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>教學課程︰設定 Cornerstone OnDemand 來自動佈建使用者

本教程演示了在基石按需和 Azure 活動目錄 （Azure AD） 中執行的步驟，以將 Azure AD 配置為自動預配使用者或組到基石按需。

> [!NOTE]
> 本教程介紹在 Azure AD 使用者預配服務之上構建的連接器。 有關此服務的作用、工作方式以及常見問題的資訊，請參閱[使用 Azure 活動目錄 自動預配和取消預配軟體即服務 （SaaS） 應用程式](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>Prerequisites

本教程中概述的方案假定您具有：

* Azure AD 租用戶。
* 基石按需租戶。
* 具有管理員許可權的 Cornerstone Demand 中的使用者帳戶。

> [!NOTE]
> Azure AD 預配集成依賴于基石[OnDemand Web 服務](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf)。 此服務可供基石點播團隊使用。

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>從 Azure 應用商店添加基石按需

在配置使用 Azure AD 自動使用者預配的基石按需之前，請將應用商店中的基石按需添加到託管 SaaS 應用程式清單中。

要從應用商店添加基石按需，請按照以下步驟操作。

1. 在[Azure 門戶](https://portal.azure.com)中，在左側的功能窗格中，選擇**Azure 活動目錄**。

    ![Azure 活動目錄圖示](common/select-azuread.png)

2. 轉到**企業應用程式**，然後選擇 **"所有應用程式**"。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請選取對話方塊頂端的 [新增應用程式]****。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜索框中，輸入 **"需求基石"** 並從結果面板中選擇 **"基石按需**"。 要添加應用程式，請選擇"**添加**"。

    ![結果清單中的 Cornerstone OnDemand](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>將使用者分配到基石按需

Azure 活動目錄使用稱為*分配*的概念來確定哪些使用者應接收對選定應用的存取權限。 在自動使用者預配的上下文中，只有分配給 Azure AD 中應用程式的使用者或組才會同步。

在配置和啟用自動使用者預配之前，請決定 Azure AD 中的哪些使用者或組需要訪問 Cornerstone OnDemand。 要將這些使用者或組分配給 Cornerstone OnDemand，請按照將[使用者或組分配給企業應用](../manage-apps/assign-user-or-group-access-portal.md)中的說明進行操作。

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>將使用者指派給 Cornerstone OnDemand 的重要秘訣

* 我們建議您將單個 Azure AD 使用者分配給 Cornerstone OnDemand 以測試自動使用者預配配置。 您可以稍後分配其他使用者或組。

* 將使用者分配給 Cornerstone OnDemand 時，請在分配對話方塊中選擇任何有效的特定于應用程式的角色（如果可用）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>將自動使用者預配配置為基石按需

本節將指導您完成配置 Azure AD 預配服務的步驟。 使用它根據 Azure AD 中的使用者或組分配在 Cornerstone OnDemand 中創建、更新和禁用使用者或組。

要在 Azure AD 中配置基礎按需的自動使用者預配，請按照以下步驟操作。

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式** > **所有應用程式** > **基石按需**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 **Cornerstone OnDemand**。

    ![應用程式清單中的基石按需連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![Cornerstone OnDemand 佈建](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. 將 [佈建模式]**** 設定為 [自動]****。

    ![基石按需預配模式](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. 在 **"管理員憑據"** 部分下，輸入 Cornerstone OnDemand 帳戶的管理員使用者名、管理員密碼和域：

    * 在 **"管理員使用者名"** 框中，填寫 Cornerstone OnDemand 租戶上的管理員帳戶的域或使用者名。 例如 contoso_admin。

    * 在 **"管理員密碼"** 框中，填寫與管理員使用者名對應的密碼。

    * 在 **"域"** 框中，填寫基石按需租戶的 Web 服務 URL。 例如，服務位於`https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`，對於 Contoso，域為`https://ws-contoso.csod.com/feed30/clientdataservice.asmx`。 有關如何檢索 Web 服務 URL 的詳細資訊，請參閱[此 pdf](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf)。

6. 填寫步驟 5 中所示的框後，選擇 **"測試連接**"以確保 Azure AD 可以連接到基石按需。 如果連接失敗，請確保您的 Cornerstone Demand 帳戶具有管理員許可權，然後重試。

    ![基石按需測試連接](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. 在 **"通知電子郵件"** 框中，輸入接收預配錯誤通知的個人或組的電子郵件地址。 選中"**在發生故障時發送電子郵件通知**"核取方塊。

    ![基石按需通知電子郵件](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. 選取 [儲存]****。

9. 在 [對應]**** 區段中，選取 [同步處理 Azure Active Directory 使用者至 Cornerstone OnDemand]****。

    ![基石按需同步](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. 在**屬性對應**部分中查看從 Azure AD 同步到基石按需的使用者屬性。 選取為 [比對]**** 屬性的屬性會用來比對 Cornerstone OnDemand 中的使用者帳戶以進行更新作業。 要保存任何更改，請選擇"**保存**"。

    ![基石按需屬性對應](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. 要配置範圍篩選器，請按照[範圍界定篩選器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的說明進行操作。

12. 要啟用"基礎按需"的 Azure AD 預配服務，在 **"設置"** 部分中，將**預配狀態**更改為**On**。

    ![基石按需預配狀態](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. 定義要預配到基石按需的使用者或組。 在 **"設置"** 部分中，在 **"範圍**"中選擇所需的值。

    ![基石按需範圍](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. 準備好預配時，請選擇"**保存**"。

    ![基石按需保存](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

此操作將開始**在"設置"** 部分中定義"**範圍"** 中定義的所有使用者或組的初始同步。 初始同步執行的時間比以後同步長。 只要 Azure AD 預配服務運行，它們大約每 40 分鐘發生一次。 

您可以使用 **"同步詳細資訊"** 部分監視進度並關注指向預配活動報告的連結。 該報告描述了 Azure AD 預配服務在 Cornerstone OnDemand 上執行的所有操作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

Cornerstone OnDemand **Position** 屬性預期的值是對應至 Cornerstone OnDemand 入口網站上的角色。 要獲取有效**位置**值的清單，請轉到"在基石按需門戶中**編輯使用者記錄>組織結構>位置**。

![基石按需預配 編輯使用者記錄](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![基石按需配置位置](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![基石按需配置位置清單](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
