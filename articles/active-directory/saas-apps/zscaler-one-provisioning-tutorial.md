---
title: 教程：配置 Zscaler One，以便使用 Azure 活動目錄進行自動使用者預配 |微軟文檔
description: 瞭解如何將 Azure 活動目錄配置為自動預配使用者帳戶並將其預配到 Zscaler One。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 72f6ba2b-73ed-420a-863a-aff672f26fa3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 87413932acee576934ee50b59546371b03ceaf7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064167"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>教程：配置 Zscaler One 進行自動使用者預配

本教程演示了在 Zscaler One 和 Azure 活動目錄 （Azure AD） 中執行的步驟，以將 Azure AD 配置為自動預配使用者和組到 Zscaler One。

> [!NOTE]
> 本教程介紹在 Azure AD 使用者預配服務之上構建的連接器。 有關此服務的作用、工作方式以及常見問題的資訊，請參閱[使用 Azure 活動目錄 自動預配和取消預配軟體即服務 （SaaS） 應用程式](../active-directory-saas-app-provisioning.md)。


## <a name="prerequisites"></a>Prerequisites

本教程中概述的方案假定您具有：

* Azure AD 租用戶。
* Zscaler One 租戶。
* Zscaler One 中的具有管理員許可權的使用者帳戶。

> [!NOTE]
> Azure AD 預配集成依賴于 Zscaler One SCIM API。 此 API 可供 Zscaler One 開發人員使用企業包的帳戶。

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>從 Azure 應用商店添加 Zscaler One

在配置 Zscaler One 以使用 Azure AD 進行自動使用者預配之前，請將 Zscaler One 從 Azure 應用商店添加到託管 SaaS 應用程式清單中。

要從應用商店中添加 Zscaler One，請按照以下步驟操作。

1. 在[Azure 門戶](https://portal.azure.com)中，在左側的功能窗格中，選擇**Azure 活動目錄**。

    ![Azure 活動目錄圖示](common/select-azuread.png)

2. 轉到**企業應用程式**，然後選擇 **"所有應用程式**"。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請選取對話方塊頂端的 [新增應用程式]****。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜索框中，輸入**Zscaler One**並從結果面板中選擇**Zscaler One。** 要添加應用程式，請選擇"**添加**"。

    ![結果清單中的 Zscaler One](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>將使用者分配給 Zscaler One

Azure 活動目錄使用稱為*分配*的概念來確定哪些使用者應接收對選定應用的存取權限。 在自動使用者預配的上下文中，只有分配給 Azure AD 中應用程式的使用者或組才會同步。

在配置和啟用自動使用者預配之前，請決定 Azure AD 中的哪些使用者或組需要訪問 Zscaler One。 要將這些使用者或組分配給 Zscaler One，請按照將[使用者或組分配給企業應用](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)中的說明進行操作。

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>將使用者分配給 Zscaler One 的重要提示

* 我們建議您將單個 Azure AD 使用者分配給 Zscaler One 以測試自動使用者預配配置。 您可以稍後分配其他使用者或組。

* 將使用者分配給 Zscaler One 時，請在分配對話方塊中選擇任何有效的特定于應用程式的角色（如果可用）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>將自動使用者預配配置為 Zscaler One

本節將指導您完成配置 Azure AD 預配服務的步驟。 使用它基於 Azure AD 中的使用者或組分配在 Zscaler One 中創建、更新和禁用使用者或組。

> [!TIP]
> 您還可以為 Zscaler One 啟用基於 SAML 的單登錄。 按照[Zscaler 單一登入教程](zscaler-One-tutorial.md)中的說明進行操作。 單一登入可以獨立于自動使用者預配進行配置，儘管這兩個功能相輔相成。

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>在 Azure AD 中為 Zscaler One 配置自動使用者預配

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式** > **所有應用程式** > **Zscaler One**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Zscaler One]****。

    ![應用程式清單中的 Zscaler 一個連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![Zscaler 一個預配](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. 將**預配模式**設置為 **"自動**"。

    ![Zscaler 一種預配模式](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. 在 **"管理員憑據"** 部分下，填寫 **"租戶 URL"** 和 **"秘密權杖"** 框，並填寫步驟 6 中所述的 Zscaler One 帳戶的設置。

6. 要獲取租戶 URL 和金鑰權杖，請訪問 Zscaler One 門戶 UI 中的**管理** > **身份驗證設置**。 在 [驗證類型]**** 下方，選取 [SAML]****。

    ![Zscaler 一個身份驗證設置](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. 選擇 **"配置 SAML"** 以打開 **"配置 SAML"** 選項。

    ![Zscaler 一 配置 SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. 選擇**啟用基於 SCIM 的預配**，以獲取**基本 URL**和**承載權杖**中的設置。 然後保存設置。 將**基本 URL**設置複製到 Azure 門戶中的**租戶 URL。** 將**承載權杖**設置複製到 Azure 門戶中**的秘密權杖**。

7. 填寫步驟 5 中所示的框後，選擇 **"測試連接**"以確保 Azure AD 可以連接到 Zscaler One。 如果連接失敗，請確保您的 Zscaler One 帳戶具有管理員許可權，然後重試。

    ![Zscaler 一個測試連接](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. 在 **"通知電子郵件"** 框中，輸入接收預配錯誤通知的個人或組的電子郵件地址。 選中"**在發生故障時發送電子郵件通知**"核取方塊。

    ![Zscaler 一封通知電子郵件](./media/zscaler-one-provisioning-tutorial/notification.png)

9. 選取 [儲存]****。

10. 在 **"映射"** 部分下，選擇**將 Azure 活動目錄使用者同步到 Zscaler One**。

    ![Zscaler 一個使用者同步](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. 在**屬性對應**部分中查看從 Azure AD 同步到 Zscaler One 的使用者屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 Zscaler One 中的使用者帳戶以進行更新操作。 要保存任何更改，請選擇"**保存**"。

    ![Zscaler 一個匹配的使用者屬性](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. 在 **"映射"** 部分下，選擇**將 Azure 活動目錄組同步到 Zscaler One**。

    ![Zscaler 一個組同步](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. 在**屬性對應**部分中查看從 Azure AD 同步到 Zscaler One 的組屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 Zscaler One 中的組以進行更新操作。 要保存任何更改，請選擇"**保存**"。

    ![Zscaler 一個匹配組屬性](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. 要配置範圍篩選器，請按照[範圍界定篩選器教程](./../active-directory-saas-scoping-filters.md)中的說明進行操作。

15. 要為 Zscaler One 啟用 Azure AD 預配服務，在 **"設置"** 部分中，將**預配狀態**更改為**On**。

    ![Zscaler 一種預配狀態](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. 定義要預配到 Zscaler One 的使用者或組。 在 **"設置"** 部分中，在 **"範圍**"中選擇所需的值。

    ![茲卡爾默一個範圍](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. 準備好預配時，請選擇"**保存**"。

    ![茲卡爾瓦勒一保存](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

此操作將開始**在"設置"** 部分中定義"**範圍"** 中定義的所有使用者或組的初始同步。 初始同步執行的時間比以後同步長。 只要 Azure AD 預配服務運行，它們大約每 40 分鐘發生一次。 

您可以使用 **"同步詳細資訊"** 部分監視進度並關注指向預配活動報告的連結。 該報告描述了 Azure AD 預配服務在 Zscaler One 上執行的所有操作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../active-directory-saas-provisioning-reporting.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
