---
title: 教程：使用 Azure 活動目錄配置 MerchLogix 以自動預配使用者 |微軟文檔
description: 瞭解如何將 Azure 活動目錄配置為自動預配和取消向 MerchLogix 預配使用者帳戶。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 9df4c7c5-9a58-478e-93b7-2f77aae12807
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4aa60fb565552961a3c85346c39c318a90c8adc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061289"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>教程：為自動使用者預配配置 MerchLogix

本教程的目的是演示在 MerchLogix 和 Azure 活動目錄 （Azure AD） 中執行的步驟，以將 Azure AD 配置為自動預配和取消向 MerchLogix 預配使用者和/或組。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* 默奇洛格克斯租戶
* MerchLogix 的技術連絡人，可提供使用者預配所需的 SCIM 終結點 URL 和金鑰權杖

## <a name="adding-merchlogix-from-the-gallery"></a>從圖庫添加 MerchLogix

在配置 MerchLogix 以使用 Azure AD 進行自動使用者預配之前，需要將 Azure AD 應用程式庫中的 MerchLogix 添加到託管 SaaS 應用程式清單中。

**要從 Azure AD 應用程式庫添加 MerchLogix，請執行以下步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]**** 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 導航到**企業應用程式** > **所有應用程式**。

    ![企業應用程式區段][2]

3. 要添加 MerchLogix，請按一下對話方塊頂部的 **"新建應用程式**"按鈕。

    ![新增應用程式按鈕][3]

4. 在搜索框中，鍵入**MerchLogix**。

5. 在結果面板中，選擇**MerchLogix**，然後按一下 **"添加**"按鈕將 MerchLogix 添加到 SaaS 應用程式清單中。

    ![默奇洛格克斯預配][4]

## <a name="assigning-users-to-merchlogix"></a>將使用者分配給 MerchLogix

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和/或群組會進行同步處理。 

在配置和啟用自動使用者預配之前，應決定 Azure AD 中的哪些使用者和/或組需要訪問 MerchLogix。 一旦確定，您可以按照此處的說明將這些使用者和/或組分配給 MerchLogix：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>將使用者分配給 MerchLogix 的重要提示

* 建議將單個 Azure AD 使用者分配給 MerchLogix 以測試初始自動使用者預配配置。 您可於稍後在測試成功之後指派其他使用者及/或群組。

* 將使用者分配給 MerchLogix 時，必須在分配對話方塊中選擇任何有效的特定于應用程式的角色（如果可用）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>將自動使用者預配配置配置為 MerchLogix 

本節將指導您完成將 Azure AD 預配服務配置為根據 Azure AD 中的使用者和/或組分配在 MerchLogix 中創建、更新和禁用使用者和/或組的步驟。

> [!TIP]
> 您也可以按照[MerchLogix 單一登入教程](merchlogix-tutorial.md)中提供的說明，選擇啟用基於 SAML 的單登錄。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>要在 Azure AD 中配置 MerchLogix 的自動使用者預配：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 [Azure Active Directory] > [企業應用程式] > [所有應用程式]****。

2. 從 SaaS 應用程式清單中選擇 MerchLogix。

3. 選擇 **"預配"** 選項卡。

4. 將**預配模式**設置為 **"自動**"。

    ![默奇洛格克斯預配](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. 在 **"管理認證"** 部分下：

    * 在 **"租戶 URL"** 欄位中，輸入 MerchLogix 技術連絡人提供的 SCIM 終結點 URL。

    * 在 **"秘密權杖"** 欄位中，輸入由您的 MerchLogix 技術連絡人提供的秘密權杖。

6. 填充步驟 5 中所示的欄位後，按一下 **"測試連接**"以確保 Azure AD 可以連接到 MerchLogix。 如果連接失敗，請確保您的 MerchLogix 帳戶具有管理員許可權，然後重試。

7. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

8. 按一下 [儲存]****。

9. 在 **"映射"** 部分下，選擇**將 Azure 活動目錄使用者同步到 MerchLogix**。

10. 在**屬性對應**部分中查看從 Azure AD 同步到 MerchLogix 的使用者屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 MerchLogix 中的使用者帳戶以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

11. 在 **"映射**"部分下，選擇**將 Azure 活動目錄組同步到 MerchLogix**。

12. 在**屬性對應**部分中查看從 Azure AD 同步到 MerchLogix 的組屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 MerchLogix 中的組以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

13. 要為 MerchLogix 啟用 Azure AD 預配服務，請將 **"設置"** 部分中的**預配狀態**更改為 **"打開**"。

14. 當您準備好要佈建時，按一下 [儲存]****。

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 可以使用 **"同步詳細資訊"** 部分監視進度並關注指向預配活動報告的連結，該報表描述 Azure AD 預配服務在 MerchLogix 上執行的所有操作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
