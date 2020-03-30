---
title: 教程：使用 Azure 活動目錄配置標牌以自動預配使用者 |微軟文檔
description: 瞭解如何將 Azure 活動目錄配置為自動預配和取消將使用者帳戶預配到 Signagelive。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: bbd0d3c60c5cc4056d5cbadfc7c6d90ae2a37ca6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063232"
---
# <a name="tutorial-configure-signagelive--for-automatic-user-provisioning"></a>教程：為自動使用者預配配置標牌

本教程的目的是演示在 Signagelive 和 Azure 活動目錄 （Azure AD） 中執行的步驟，以將 Azure AD 配置為自動預配和取消將使用者和/或組預配到 Signagelive。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶。
* [標牌活租戶](https://signagelive.com/pricing/)
* 具有管理員許可權的 Signagelive 中的使用者帳戶。

## <a name="assigning-users-to-signagelive"></a>將使用者分配給簽名直播   

Azure 活動目錄使用稱為*分配*的概念來確定哪些使用者應接收對選定應用的存取權限。 在自動使用者預配的上下文中，只有分配給 Azure AD 中應用程式的使用者和/或組才會同步。

在配置和啟用自動使用者預配之前，應決定 Azure AD 中的哪些使用者和/或組需要訪問 Signagelive。 一旦確定，您可以按照此處的說明將這些使用者和/或組分配給 Signagelive：
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-signagelive"></a>將使用者分配給 Signagelive 的重要提示   

* 建議將單個 Azure AD 使用者分配到 Signagelive 以測試自動使用者預配配置。 其他使用者及/或群組可能會稍後再指派。

* 將使用者分配給 Signagelive 時，必須在分配對話方塊中選擇任何有效的特定于應用程式的角色（如果可用）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="setup-signagelive--for-provisioning"></a>用於預配的設置標牌

在使用 Azure AD 配置 Signagelive 以自動預配使用者之前，需要在 Signagelive 上啟用 SCIM 預配。

  聯繫[Signagelive](mailto:development@signagelive.com)以獲取配置 SCIM 預配所需的金鑰權杖。

## <a name="add-signagelive-from-the-gallery"></a>從資源庫新增 Signagelive

要配置 Signagelive 以使用 Azure AD 進行自動使用者預配，需要將標識live從 Azure AD 應用程式庫添加到託管 SaaS 應用程式清單中。

**要從 Azure AD 應用程式庫添加 Signagelive，請執行以下步驟：**

1. 在**[Azure 門戶](https://portal.azure.com)** 中，在左側導航面板中，選擇**Azure 活動目錄**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 轉到**企業應用程式**，然後選擇 **"所有應用程式**"。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 要添加新應用程式，請選擇窗格頂部的 **"新建應用程式**"按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜索框中，輸入 **"標牌直播"，** 在結果面板中選擇 **"標牌"，** 然後按一下"**添加**"按鈕以添加應用程式。

    ![結果清單中的標牌](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-signagelive"></a>將自動使用者預配配置為標牌    

本節將指導您完成將 Azure AD 預配服務配置為根據 Azure AD 中的使用者和/或組分配在 Signagelive 中創建、更新和禁用使用者和/或組的步驟。

> [!TIP]
>  您也可以按照[Signagelive 單點登錄教程](Signagelive-tutorial.md)中提供的說明，選擇啟用基於 SAML 的單登錄。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-signagelive--in-azure-ad"></a>要在 Azure AD 中配置標牌的即時使用者自動預配：

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式**，然後選擇**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Signagelive]****。

    ![應用程式清單中的 Signagelive 連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![預配選項卡](common/provisioning.png)

4. 將**預配模式**設置為 **"自動**"。

    ![預配選項卡](common/provisioning-automatic.png)

5. 在"管理認證"部分下` https://samlapi.signagelive.com/scim/v2`，在**租戶 URL**中輸入 。 在 **"機密權杖"** 欄位中，輸入工程開發團隊提供的**無記名權杖**值。 按一下 **"測試連接**"以確保 Azure AD 可以連接到標牌直播。 如果連接失敗，請確保您的 Signagelive 帳戶具有管理員許可權，然後重試。
    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [儲存]****。

8. 在 **"映射"** 部分下，選擇**將 Azure 活動目錄使用者同步到標牌。**

    ![標牌即時使用者映射](media/signagelive-provisioning-tutorial/usermapping.png)

9. 在**屬性對應**部分中查看從 Azure AD 同步到 Signagelive 的使用者屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 Signagelive 中的使用者帳戶以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![標牌使用者屬性](media/signagelive-provisioning-tutorial/userattribute.png)

10. 在 **"映射**"部分下，選擇**將 Azure 活動目錄組同步到標牌。**

    ![標牌即時使用者映射](media/signagelive-provisioning-tutorial/groupmapping.png)

11. 在**屬性對應**部分中查看從 Azure AD 同步到 Signagelive 的組屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 Signagelive 中的群組帳戶以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![標牌使用者屬性](media/signagelive-provisioning-tutorial/groupattribute.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 要啟用"簽名"的 Azure AD 預配服務，請將 **"設置**"部分中的 **"預配狀態**更改為**打開**"。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 通過在 **"設置"** 部分中選擇"**範圍"** 中所需的值，定義要預配到 Signagelive 的使用者和/或組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步執行的時間比後續同步長。 有關使用者和/或組預配需要多長時間的詳細資訊，請參閱[預配使用者需要多長時間](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)。 

可以使用"**目前狀態**"部分監視進度並遵循指向預配活動報告的連結，該報表描述 Azure AD 預配服務在 Signagelive 上執行的所有操作。 有關詳細資訊，請參閱[檢查使用者預配的狀態](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 要讀取 Azure AD 預配日誌，請參閱[報告自動使用者帳戶預配](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
