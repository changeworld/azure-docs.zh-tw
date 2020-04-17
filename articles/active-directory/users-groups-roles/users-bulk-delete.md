---
title: 大量刪除 Azure 活動目錄門戶的使用者 |微軟文件
description: 在 Azure 活動目錄中的 Azure 管理中心批次刪除使用者
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: beb8b4f35dc5f02e59cced05a6bcfc235d42f996
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532798"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>批次刪除 Azure 活動目錄中的使用者

使用 Azure 活動目錄 (Azure AD) 門戶,可以使用逗號分隔的值 (CSV) 檔批量刪除使用者,將大量成員刪除到組。

## <a name="to-bulk-delete-users"></a>大量刪除使用者

1. 使用組織中的使用者管理員的帳戶[登入 Azure AD 組織](https://aad.portal.azure.com)。
1. 在 Azure AD 中,選擇 **「使用者** > **批量刪除**」 。
1. 在**批次刪除使用者**頁面上,選擇 **「下載」** 以接收使用者屬性的有效 CSV 檔。

   ![選擇要移除的使用者的本地 CSV 檔案](./media/users-bulk-delete/bulk-delete.png)

1. 開啟 CSV 檔,並為要刪除的每個使用者添加一行。 唯一必需的值是**使用者主體名稱**。 然後儲存檔案。

   ![CSV 檔包含要移除的使用者的姓名和指示](./media/users-bulk-delete/delete-csv-file.png)

1. 在**大量刪除使用者**頁面上,在 **「上傳您的 csv 檔案**」下,瀏覽到該檔。 當您選擇該檔並按一下「提交」時,CSV 檔的驗證將開始。
1. 當檔案內容經過驗證後，您會看到「檔案已成功上傳」****。 如果發生錯誤，您必須修正這些錯誤，才能提交作業。
1. 檔案通過驗證後,選擇 **「提交」** 以啟動刪除使用者的 Azure 批量操作。
1. 刪除操作完成後,您將看到批量操作成功的通知。

如果存在錯誤,則可以在 **「批量操作結果**」頁上下載並查看結果檔。 該檔包含每個錯誤的原因。

## <a name="check-status"></a>檢查狀態

您可以在 **「批量操作結果」** 頁中查看所有掛起的批量請求的狀態。

   [![](media/users-bulk-delete/bulk-center.png "Check delete status in the Bulk Operations Results page")](media/users-bulk-delete/bulk-center.png#lightbox)

接下來,可以檢查已刪除的使用者是否存在於 Azure 門戶或使用 PowerShell 中的 Azure AD 組織中。

## <a name="verify-deleted-users-in-the-azure-portal"></a>在 Azure 門戶中驗證已刪除的使用者

1. 使用組織中的「使用者管理員」帳戶來登入 Azure 入口網站。
1. 在導覽窗格中，選取 [Azure Active Directory]****。
1. 在 [管理]  底下選取 [使用者]  。
1. 在 **「顯示**」下,選擇 **「 所有使用者**」 並驗證您刪除的使用者是否不再列出。

### <a name="verify-deleted-users-with-powershell"></a>使用 PowerShell 驗證已移除的使用者

執行以下命令：

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

驗證已刪除的使用者是否不再列出。

## <a name="next-steps"></a>後續步驟

- [[大量新增使用者](users-bulk-add.md)
- [下載使用者清單](users-bulk-download.md)
- [批次還原使用者](users-bulk-restore.md)
