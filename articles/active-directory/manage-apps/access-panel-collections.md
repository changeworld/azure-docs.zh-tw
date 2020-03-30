---
title: 在 Azure 活動目錄中為"我的應用"門戶創建集合 |微軟文檔
description: 使用"我的應用"集合自訂"我的應用"頁面，為您的最終使用者提供更簡單的"我的應用"體驗。 將應用程式組織到具有單獨選項卡的組中。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2020
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: c91b9ffc9e3487e492c91cb0f5825d0b725f9410
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120092"
---
# <a name="create-collections-on-the-my-apps-portal"></a>在"我的應用"門戶上創建集合

您的使用者可以使用"我的應用"門戶查看和啟動他們有權訪問的基於雲的應用程式。 預設情況下，使用者可以訪問的所有應用程式都列在一頁上。 為了更好地為使用者組織此頁面，如果您有 Azure AD 高級 P1 或 P2 許可證，則可以設置集合。 使用集合，可以將相關應用程式（例如，按作業角色、任務或專案）組合在一起，並在單獨的選項卡上顯示它們。集合實質上將篩選器應用於使用者已經可以訪問的應用程式，因此使用者只能看到集合中分配給它們的應用程式。

> [!NOTE]
> 本文介紹管理員如何啟用和創建集合。 有關如何使用"我的應用"門戶和集合的最終使用者的資訊，請參閱[訪問和使用集合](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces)。

## <a name="enable-the-latest-my-apps-features"></a>啟用最新的"我的應用"功能

1. 打開[**Azure 門戶**](https://portal.azure.com/)，以使用者管理員或全域管理員身份登錄。

2. 轉到**Azure 活動目錄** > **使用者設置**。

3. 在 **"使用者要素預覽"** 下，選擇 **"管理使用者要素預覽設置**"。

4. 在 **"使用者可以為我的應用使用預覽功能"** 下，選擇以下選項之一：
   * **已選擇**- 啟用特定組的要素。 使用 **"選擇組**"選項選擇要為其啟用要素的組。  
   * **全部**- 啟用所有使用者的功能。

> [!NOTE]
> 要打開"我的應用"門戶，使用者可以使用組織的連結`https://myapps.microsoft.com`或自訂連結，例如`https://myapps.microsoft.com/contoso.com`。 啟用新的"我的應用"體驗後，"**更新的我的應用程式"體驗將**顯示在"我的應用"頁面頂部，使用者可以選擇 **"試用"** 以查看新體驗。 要停止使用新體驗，使用者可以從頁面頂部的 **"離開新體驗**"橫幅中選擇 **"是**"。

## <a name="create-a-collection"></a>建立集合

要創建集合，必須具有 Azure AD 高級 P1 或 P2 許可證。

1. 打開[**Azure 門戶**](https://portal.azure.com/)並使用 Azure AD 高級 P1 或 P2 許可證登錄管理員。

2. 轉到**Azure 活動目錄** > **企業應用程式**。

3. 在 **"管理**"下，選擇 **"集合**"。

4. 選擇 **"新建集合**"。 在 **"新建集合"** 頁中，輸入集合**的名稱**（我們建議不要在名稱中使用"集合"。 然後輸入**描述**。

   ![新集合頁](media/acces-panel-collections/new-collection.png)

5. 選擇"**應用程式**"選項卡。選擇 **" 添加應用程式**"，然後在 **"添加應用程式**"頁中，選擇要添加到集合中的所有應用程式，或使用 **"搜索**"框查找應用程式。

   ![將應用程式添加到集合](media/acces-panel-collections/add-applications.png)

6. 完成添加應用程式後，選擇 **"添加**"。 將顯示所選應用程式的清單。 您可以使用向上箭號更改清單中的應用程式順序。 要向下移動應用程式或從集合中刪除它，請選擇 **"更多**"功能表 **（...）。**

7. 選擇"**擁有者**"選項卡。選擇 **"添加使用者和組**"，然後在"**添加使用者和組**"頁中，選擇要向其分配擁有權的使用者或組。 完成選擇使用者和組後，**選擇"選擇**"。

9. 選擇"**使用者和組**"選項卡。選擇 **" 添加使用者和組**"，然後在"**添加使用者和組**"頁中，選擇要將集合分配給的使用者或組。 或者使用 **"搜索"** 框查找使用者或組。 完成選擇使用者和組後，**選擇"選擇**"。

   ![添加使用者和組](media/acces-panel-collections/add-users-and-groups.png)

11. 選擇 **"審閱 " 創建**。 將顯示新集合的屬性。


## <a name="view-audit-logs"></a>檢視稽核記錄

稽核記錄記錄"我的應用"集合操作，包括創建集合的最終使用者操作。 以下事件從"我的應用"生成：

* 創建集合
* 編輯集合
* 刪除集合
* 啟動應用程式（最終使用者）
* 自助服務應用程式添加（最終使用者）
* 自助服務應用程式刪除（最終使用者）

通過在"活動"部分中選擇**Azure 活動目錄** > **企業應用程式** > **稽核記錄**，可以在[Azure 門戶](https://portal.azure.com)中訪問稽核記錄。 對於**服務**，請選擇 **"我的應用**"。

## <a name="get-support-for-my-account-pages"></a>獲取對"我的帳戶"頁面的支援

在"我的應用"頁面中，使用者可以選擇 **"我的帳戶** > **查看我的帳戶"** 以打開其帳戶設置。 在 Azure AD**我的帳戶**頁上，使用者可以管理其安全資訊、設備、密碼等。 他們還可以訪問其 Office 帳戶設置。

如果需要提交 Azure AD 帳戶頁或 Office 帳戶頁的問題支援請求，請按照以下步驟操作，以便正確路由請求： 

* 對於 Azure **AD"我的帳戶"** 頁的問題，請從 Azure 門戶中打開支援請求。 轉到**Azure 門戶** > **Azure 活動目錄** > **新支援請求**。

* 對於**Office"我的帳戶"** 頁面的問題，請從 Microsoft 365 管理中心內打開支援請求。 轉到**微軟 365 管理中心** > **支援**。 

## <a name="next-steps"></a>後續步驟
[Azure Active Directory 中的應用程式使用者體驗](end-user-experiences.md)