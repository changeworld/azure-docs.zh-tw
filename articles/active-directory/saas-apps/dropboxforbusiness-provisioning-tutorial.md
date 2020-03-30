---
title: 教程：為使用 Azure 活動目錄自動預配業務配置 Dropbox |微軟文檔
description: 瞭解如何將 Azure 活動目錄配置為自動預配和取消將使用者帳戶預配到業務 Dropbox。
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
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 3acc2c271e590bddb13aaa01498f404da4340036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058391"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>教學課程︰設定 Dropbox for Business 來自動佈建使用者

本教程的目的是演示在"業務和 Azure 活動目錄（Azure AD）Dropbox"中執行的步驟，以將 Azure AD 配置為自動預配和取消將使用者和/或組預配到業務放置框。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [商務租戶的放置箱](https://www.dropbox.com/business/pricing)
* 具有管理員許可權的"企業"Dropbox 中的使用者帳戶。

## <a name="add-dropbox-for-business-from-the-gallery"></a>從庫中添加商務信箱

在使用 Azure AD 為自動使用者預配配置業務 Dropbox 之前，需要將業務 Dropbox 從 Azure AD 應用程式庫添加到託管 SaaS 應用程式清單中。

**要從 Azure AD 應用程式庫添加業務刪除框，請執行以下步驟：**

1. 在**[Azure 門戶](https://portal.azure.com)** 中，在左側導航面板中，選擇**Azure 活動目錄**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 轉到**企業應用程式**，然後選擇 **"所有應用程式**"。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 要添加新應用程式，請選擇窗格頂部的 **"新建應用程式**"按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜索框中，在"**企業"中輸入"企業"的"Dropbox"，** 在結果面板中選擇 **"企業"的"Dropbox"，** 然後按一下"**添加**"按鈕以添加應用程式。

    ![結果清單中的商務用 Dropbox](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>將使用者指派至 Dropbox for Business

Azure 活動目錄使用稱為*分配*的概念來確定哪些使用者應接收對選定應用的存取權限。 在自動使用者預配的上下文中，只有分配給 Azure AD 中應用程式的使用者和/或組才會同步。

在配置和啟用自動使用者預配之前，應確定 Azure AD 中的哪些使用者和/或組需要訪問業務 Dropbox。 決定後，您可以按照此處的說明將這些使用者和/或組分配給 Dropbox 進行業務設置：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>將使用者指派給 Dropbox for Business 的重要秘訣

* 建議將單個 Azure AD 使用者分配給企業 Dropbox 以測試自動使用者預配配置。 其他使用者及/或群組可能會稍後再指派。

* 將使用者分配給"業務"Dropbox 時，必須在分配對話方塊中選擇任何有效的特定于應用程式的角色（如果可用）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>為業務配置自動使用者預配到 Dropbox 

本節將指導您完成將 Azure AD 預配服務配置為根據 Azure AD 中的使用者和/或組分配在"企業 Dropbox"中創建、更新和禁用使用者和/或組的步驟。

> [!TIP]
> 您也可以根據["企業單一登入指南的 Dropbox"](dropboxforbusiness-tutorial.md)中提供的說明，選擇啟用基於 SAML 的單一登入業務。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>要為 Azure AD 中的業務設置拖放箱配置自動使用者預配：

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式**，然後選擇**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Dropbox for Business]****。

    ![應用程式清單中的商務用 Dropbox](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![預配選項卡](common/provisioning.png)

4. 將**預配模式**設置為 **"自動**"。

    ![預配選項卡](common/provisioning-automatic.png)

5. 在 [系統管理員認證]**** 區段下，按一下 [授權]****。 會在新的瀏覽器視窗中開啟 Dropbox for Business 對話方塊。

    ![佈建 ](common/provisioning-oauth.png)

6. 在 **"企業登錄到 Dropbox"上，使用 Azure AD**對話方塊連結，登錄到"企業租戶的 Dropbox"並驗證您的身份。

    ![商務登錄的投遞箱](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. 完成步驟 5 和 6 後，按一下 **"測試連接**"以確保 Azure AD 可以連接到業務投遞箱。 如果連接失敗，請確保您的"業務帳戶 Dropbox"具有管理員許可權，然後重試。

    ![Token](common/provisioning-testconnection-oauth.png)

8. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

9. 按一下 [儲存]****。

10. 在 **"映射"** 部分下，選擇**將 Azure 活動目錄使用者同步到放置框**。

    ![放置框使用者映射](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. 查看**屬性對應**部分中從 Azure AD 同步到 Dropbox 的使用者屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 Dropbox 中的使用者帳戶以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![放置框使用者屬性](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. 在 **"映射**"部分下，選擇**將 Azure 活動目錄組同步到放置框**。

    ![放置框組映射](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. 查看**屬性對應**部分中從 Azure AD 同步到 Dropbox 的組屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 Dropbox 中的組以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![放置框組屬性](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

15. 要為 Dropbox 啟用 Azure AD 預配服務，在 **"設置"** 部分將**預配狀態**更改為 **"打開**"。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

16. 通過在 **"設置"** 部分中選擇"**範圍"** 中所需的值，定義要預配到 Dropbox 的使用者和/或組。

    ![佈建範圍](common/provisioning-scope.png)

17. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 可以使用 **"同步詳細資訊"** 部分監視進度並關注指向預配活動報告的連結，該報表描述 Azure AD 預配服務在 Dropbox 上執行的所有操作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制
 
* Dropbox 不支援暫停邀請的使用者。 如果邀請的使用者被掛起，該使用者將被刪除。

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

