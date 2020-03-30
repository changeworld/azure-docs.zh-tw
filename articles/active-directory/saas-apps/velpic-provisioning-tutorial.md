---
title: 教程：使用 Azure 活動目錄配置 Velpic 以自動預配使用者 |微軟文檔
description: 瞭解如何將 Azure 活動目錄配置為自動預配和取消向 Velpic 預配使用者帳戶。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b7a6c2c9b7ecb0b160f7481d95f7682f3f7a109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064116"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>教程：為自動使用者預配配置 Velpic

本教程的目的是向您展示需要在 Velpic 和 Azure AD 中執行的步驟，以便自動預配和取消預配使用者帳戶從 Azure AD 到 Velpic。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中說明的案例假設您已經具有下列項目：

* Azure Active Directory 租用戶
* 具有[企業計畫](https://www.velpic.com/pricing.html)或啟用更好的 Velpic 租戶
* 具有管理員許可權的 Velpic 中的使用者帳戶

## <a name="assigning-users-to-velpic"></a>將使用者分配給 Velpic

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者帳戶佈建的內容中，只有已「指派」至 Azure AD 中的應用程式之使用者和群組會進行同步處理。 

在配置和啟用預配服務之前，您需要決定 Azure AD 中的哪些使用者和/或組表示需要訪問 Velpic 應用的使用者。 一旦確定，您可以按照此處的說明將這些使用者分配給您的 Velpic 應用：

[將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>將使用者分配給 Velpic 的重要提示

* 建議將單個 Azure AD 使用者分配給 Velpic 以測試預配配置。 其他使用者及/或群組可能會稍後再指派。

* 將使用者分配給 Velpic 時，必須在分配對話方塊中選擇 **"使用者"** 角色或其他有效的特定于應用程式的角色（如果可用）。 請注意，**預設訪問**角色不適用於預配，將跳過這些使用者。

## <a name="configuring-user-provisioning-to-velpic"></a>將使用者預配配置為 Velpic

本節將 Azure AD 連接到 Velpic 的使用者帳戶預配 API，並根據 Azure AD 中的使用者和組分配配置配置預配服務以在 Velpic 中創建、更新和禁用分配的使用者帳戶。

> [!TIP]
> 您還可以根據[Azure 門戶](https://portal.azure.com)中提供的說明，選擇啟用基於 SAML 的單登錄。 可以獨立設定自動佈建的單一登入，雖然這兩個功能彼此補充。

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>要在 Azure AD 中配置自動使用者帳戶預配到 Velpic：

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至 [Azure Active Directory > 企業應用程式 > 所有應用程式]**** 區段。

2. 如果您已為單次登錄配置 Velpic，請使用搜索欄位搜索 Velpic 實例。 否則，在應用程式庫中選擇 **"添加**並搜索**Velpic"。** 從搜尋結果中選擇 Velpic，並將其添加到應用程式清單中。

3. 選擇 Velpic 實例，然後選擇 **"預配"** 選項卡。

4. 將**預配模式**設置為 **"自動**"。

    ![牛皮預配](./media/velpic-provisioning-tutorial/Velpic1.png)

5. 在 **"管理認證"** 部分下，輸入 **"租戶 URL"&** Velpic 的秘密權杖。（您可以在 Velpic 帳戶下找到這些值：**管理** > **集成** > **外掛程式** > **SCIM**）

    ![授權值](./media/velpic-provisioning-tutorial/Velpic2.png)

6. 在 Azure 門戶中，按一下 **"測試連接**"以確保 Azure AD 可以連接到 Velpic 應用。 如果連接失敗，請確保您的 Velpic 帳戶具有管理員許可權，然後重試步驟 5。

7. 在 [通知電子郵件]**** 欄位中輸入應收到佈建錯誤通知的個人或群組之電子郵件地址，然後勾選下列核取方塊。

8. 按一下 [儲存]****。

9. 在"映射"部分下，選擇**將 Azure 活動目錄使用者同步到 Velpic**。

10. 在 **"屬性對應"** 部分中，查看將從 Azure AD 同步到 Velpic 的使用者屬性。 請注意，選擇為 **"匹配屬性"** 的屬性將用於匹配 Velpic 中的使用者帳戶以進行更新操作。 選取 [儲存] 按鈕以認可任何變更。

11. 要為 Velpic 啟用 Azure AD 預配服務，在 **"設置"** 部分將**預配狀態**更改為 **"打開**"

12. 按一下 [儲存]****。

這將啟動"使用者和組"部分中分配給 Velpic 的任何使用者和/或組的初始同步。 請注意，初始同步處理會比後續同步處理花費更多時間執行，只要服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [同步處理詳細資料]**** 區段來監視進度，並遵循連結來佈建活動報告，報告中會描述佈建服務執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)