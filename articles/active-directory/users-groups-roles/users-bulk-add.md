---
title: 批量在 Azure 活動目錄門戶中創建使用者（預覽） |微軟文檔
description: 在 Azure 活動目錄中的 Azure AD 管理中心大量新增使用者
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/30/2019
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: a10dfffa69652ee2b75053c04b97f6492c46811e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72174332"
---
# <a name="bulk-create-users-preview-in-azure-active-directory"></a>批量在 Azure 活動目錄中創建使用者（預覽）

Azure 活動目錄 （Azure AD） 支援批量使用者創建和刪除操作、批量邀請來賓，並支援下載使用者、組和組成員的清單。

## <a name="required-permissions"></a>所需的權限

為了在監管中心中批量創建使用者，您必須以全域管理員或使用者管理員身份登錄。

## <a name="to-create-users-in-bulk"></a>批量創建使用者

1. 使用組織中的使用者管理員的帳戶[登錄到 Azure AD 組織](https://aad.portal.azure.com)。
1. 在 Azure AD 中，選擇 **"使用者** > **批量創建**"。
1. 在 **"批量創建使用者**"頁上，選擇 **"下載"** 以接收使用者屬性的有效逗號分隔值 （CSV） 檔，然後添加要創建的添加使用者。

   ![選擇本地 CSV 檔，其中列出要添加的使用者](./media/users-bulk-add/upload-button.png)

1. 打開 CSV 檔，並為要創建的每個使用者添加一行。 唯一需要的值是**名稱**、**使用者主體名稱**、**初始密碼**和**阻止登錄（是/否）。** 然後儲存檔案。

   ![CSV 檔包含要創建的使用者的名稱和指示](./media/users-bulk-add/add-csv-file.png)

1. 在**批量創建使用者（預覽）** 頁面上，在"上傳 CSV 檔"下，流覽到該檔。 當您選擇該檔並按一下"**提交**"時，將啟動 CSV 檔的驗證。
1. 驗證檔內容後，您將看到**已成功上載的檔**。 如果發生錯誤，您必須修正這些錯誤，才能提交作業。
1. 檔通過驗證後，選擇 **"提交"** 以啟動導入新使用者的 Azure 批量操作。
1. 導入操作完成後，您將看到批量操作作業狀態的通知。

如果存在錯誤，則可以在 **"批量操作結果**"頁上下載並查看結果檔。 該檔包含每個錯誤的原因。

## <a name="check-status"></a>檢查狀態

您可以在**批量操作結果（預覽）** 頁中查看所有掛起的批量請求的狀態。

   ![在"批量操作結果"頁中檢查上載狀態](./media/users-bulk-add/bulk-center.png)

接下來，可以檢查所創建的使用者是否存在於 Azure 門戶或使用 PowerShell 中的 Azure AD 組織中。

## <a name="verify-users-in-the-azure-portal"></a>驗證 Azure 門戶中的使用者

1. 使用組織中的使用者管理員的帳戶[登錄到 Azure AD 管理中心](https://aad.portal.azure.com)。
1. 在導覽窗格中，選取 [Azure Active Directory]****。
1. 在 [管理]**** 底下選取 [使用者]****。
1. 在 **"顯示**"下，選擇 **"所有使用者**"並驗證是否列出了您創建的使用者。

### <a name="verify-users-with-powershell"></a>使用 PowerShell 驗證使用者

執行以下命令：

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

您應該會看到列出您創建的使用者。

## <a name="bulk-import-service-limits"></a>大量匯入服務限制

創建使用者的每個批量活動最多可以運行一小時。 這允許批量創建至少 50，000 個使用者。

## <a name="next-steps"></a>後續步驟

- [大量刪除使用者](users-bulk-delete.md)
- [下載使用者清單](users-bulk-download.md)
- [批量還原使用者](users-bulk-restore.md)
