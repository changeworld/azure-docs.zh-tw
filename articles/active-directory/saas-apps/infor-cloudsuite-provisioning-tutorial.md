---
title: 教程：配置 Infor CloudSuite 以使用 Azure 活動目錄進行自動使用者預配 |微軟文檔
description: 瞭解如何將 Azure 活動目錄配置為自動預配和取消向 Infor CloudSuite 預配使用者帳戶。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 3ea430bb-86a7-4bb4-8315-95434a660e88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 7b91b8418580717afaf8ddf176f934b3ff1d0c60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057534"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>教程：為自動使用者預配配置 Infor CloudSuite

本教程的目的是演示在 Infor CloudSuite 和 Azure 活動目錄 （Azure AD） 中執行的步驟，以將 Azure AD 配置為自動預配和取消向 Infor CloudSuite 預配使用者和/或組。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [Infor 雲套件租戶](https://www.infor.com/products/infor-os)
* Infor CloudSuite 中的使用者帳戶，具有管理員許可權。

## <a name="assigning-users-to-infor-cloudsuite"></a>將使用者分配給 Infor CloudSuite

Azure 活動目錄使用稱為*分配*的概念來確定哪些使用者應接收對選定應用的存取權限。 在自動使用者預配的上下文中，只有分配給 Azure AD 中應用程式的使用者和/或組才會同步。

在配置和啟用自動使用者預配之前，應決定 Azure AD 中的哪些使用者和/或組需要訪問 Infor CloudSuite。 一旦確定，您可以按照此處的說明將這些使用者和/或組分配給 Infor CloudSuite：
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>將使用者分配給 Infor CloudSuite 的重要提示

* 建議將單個 Azure AD 使用者分配給 Infor CloudSuite 以測試自動使用者預配配置。 其他使用者及/或群組可能會稍後再指派。

* 將使用者分配給 Infor CloudSuite 時，必須在分配對話方塊中選擇任何有效的特定于應用程式的角色（如果可用）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>設置 Infor CloudSuite 進行預配

1. 登錄到您的[Infor CloudSuite 管理主控台](https://www.infor.com/customer-center)。 按一下使用者圖示，然後導航到**使用者管理**。

    ![Infor CloudSuite 管理主控台](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  按一下螢幕左上角的功能表圖示。 按一下"**管理**"。

    ![Infor CloudSuite 添加 SCIM](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  導航到**SCIM 帳戶**。

    ![Infor CloudSuite SCIM 帳戶](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  通過按一下加號圖示添加管理員使用者。 提供**SCIM 密碼**，並在 **"確認密碼**"下鍵入相同的密碼。 按一下資料夾圖示以保存密碼。 然後，您將看到為管理員使用者生成的**使用者識別碼**。

    ![Infor 雲套件管理員使用者](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Infor CloudSuite 密碼](media/infor-cloudsuite-provisioning-tutorial/password.png)

    ![Infor CloudSuite 識別碼](media/infor-cloudsuite-provisioning-tutorial/identifier.png)

5. 要生成無記名權杖，請複製**使用者識別碼**和**SCIM 密碼**。 將它們粘貼到記事本*中，這些筆記本由冒號分隔。 通過導航到**外掛程式> MIME 工具>基本64編碼，對字串值進行編碼**。 

    ![Infor CloudSuite 識別碼](media/infor-cloudsuite-provisioning-tutorial/token.png)

3.  複製無記名權杖。 此值將在 Azure 門戶中的 Infor CloudSuite 應用程式的"預配"選項卡中的"機密權杖"欄位中輸入。

## <a name="add-infor-cloudsuite-from-the-gallery"></a>從庫中添加 Infor 雲套件

在配置 Infor CloudSuite 以使用 Azure AD 進行自動使用者預配之前，您需要將 Infor CloudSuite 從 Azure AD 應用程式庫添加到託管 SaaS 應用程式清單中。

**要從 Azure AD 應用程式庫添加 Infor CloudSuite，請執行以下步驟：**

1. 在**[Azure 門戶](https://portal.azure.com)** 中，在左側導航面板中，選擇**Azure 活動目錄**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 轉到**企業應用程式**，然後選擇 **"所有應用程式**"。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 要添加新應用程式，請選擇窗格頂部的 **"新建應用程式**"按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜索框中，在"結果"面板中選擇**Infor CloudSuite，** 然後按一下"**添加**"按鈕以添加應用程式。 **Infor CloudSuite**

    ![結果清單中的 Infor CloudSuite](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>將自動使用者預配配置配置到 Infor CloudSuite 

本節將指導您完成將 Azure AD 預配服務配置為根據 Azure AD 中的使用者和/或組分配在 Infor CloudSuite 中創建、更新和禁用使用者和/或組的步驟。

> [!TIP]
> 您也可以選擇啟用基於 SAML 的單一登入為 Infor CloudSuite，按照[Infor CloudSuite 單一登入教程](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial)中提供的說明。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

> [!NOTE]
> 要瞭解有關 Infor CloudSuite SCIM 終結點的詳細資訊，請參閱[此](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#)。

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>要在 Azure AD 中配置 Infor CloudSuite 的自動使用者預配：

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式**，然後選擇**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Infor CloudSuite]****。

    ![應用程式清單中的 [Infor CloudSuite] 連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![預配選項卡](common/provisioning.png)

4. 將**預配模式**設置為 **"自動**"。

    ![預配選項卡](common/provisioning-automatic.png)

5. 在 **"管理認證"** 部分下`https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim`，在**租戶 URL**中輸入 。 輸入在**秘密權杖**中檢索的承載權杖值。 按一下 **"測試連接**"以確保 Azure AD 可以連接到 Infor 雲套件。 如果連接失敗，請確保您的 Infor CloudSuite 帳戶具有管理員許可權，然後重試。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [儲存]****。

8. 在 **"映射**"部分下，選擇**將 Azure 活動目錄使用者同步到 Infor CloudSuite**。

    ![Infor CloudSuite 使用者映射](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. 在**屬性對應**部分中查看從 Azure AD 同步到 Infor CloudSuite 的使用者屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 Infor CloudSuite 中的使用者帳戶以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![Infor CloudSuite 使用者屬性](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. 在 **"映射**"部分下，選擇**將 Azure 活動目錄組同步到 Infor CloudSuite**。

    ![Infor CloudSuite 組映射](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. 在**屬性對應**部分中查看從 Azure AD 同步到 Infor CloudSuite 的組屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 Infor CloudSuite 中的組以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![Infor CloudSuite 組屬性](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 要為 Infor CloudSuite 啟用 Azure AD 預配服務，在 **"設置"** 部分將**預配狀態**更改為 **"打開**"。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 通過在 **"設置"** 部分中選擇"**範圍"** 中所需的值，定義要預配到 Infor CloudSuite 的使用者和/或組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 可以使用 **"同步詳細資訊"** 部分監視進度並關注指向預配活動報告的連結，該報表描述 Azure AD 預配服務在 Infor CloudSuite 上執行的所有操作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)