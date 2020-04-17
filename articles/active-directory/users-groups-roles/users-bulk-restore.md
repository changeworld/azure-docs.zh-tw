---
title: Azure 活動目錄門戶中的大量還原已刪除使用者 |微軟文件
description: 在 Azure 的活動目錄中的 Azure AD 管理中心批次還原已刪除的使用者
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
ms.openlocfilehash: f75fe224491c2853f819a45db678e87849dc72d1
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532696"
---
# <a name="bulk-restore-deleted-users-in-azure-active-directory"></a>批次還原 Azure 的目錄中已移除的使用者

Azure 活動目錄 (Azure AD) 支援大量使用者建立和刪除操作、批量邀請來賓,並支援下載使用者、組和組成員的清單。

## <a name="to-bulk-restore-users"></a>批次還原使用者

1. 使用 Azure AD 組織中的使用者管理員的帳戶[登錄到 Azure AD](https://aad.portal.azure.com)組織。
1. 在 Azure AD 中選擇**已刪除****的使用者** > 。
1. 在 **「已刪除使用者」** 頁上,選擇**大量的**使用者的屬性的有效 CSV 檔。

   ![在「已刪除使用者」 頁上選擇批量還原命令](./media/users-bulk-restore/bulk-restore.png)

1. 打開 CSV 檔,並為要還原的每個使用者添加一行。 唯一必需的值是**ObjectID**。 然後儲存檔案。

   ![選擇本地 CSV 檔案,其中列出要新增的使用者](./media/users-bulk-restore/upload-button.png)

1. 在**批次還原**頁上,**在「上傳 csv 檔**」 下,瀏覽到該檔。 當您選擇該檔並按下「**提交**」時,將啟動 CSV 檔的驗證。
1. 當檔案內容經過驗證後，您會看到「檔案已成功上傳」****。 如果發生錯誤，您必須修正這些錯誤，才能提交作業。
1. 檔案通過驗證後,選擇 **「提交」** 以啟動還原使用者的 Azure 批量操作。
1. 還原操作完成後,您將看到批量操作成功的通知。

如果存在錯誤,則可以在 **「批量操作結果**」頁上下載並查看結果檔。 該檔包含每個錯誤的原因。

## <a name="check-status"></a>檢查狀態

您可以在 **「批量操作結果」** 頁中查看所有掛起的批量請求的狀態。

[![](media/users-bulk-restore/bulk-center.png "Check status in the Bulk Operations Results page")](media/users-bulk-restore/bulk-center.png#lightbox)

接下來,可以檢查還原的使用者是否存在 Azure 門戶或使用 PowerShell 的 Azure AD 組織。

## <a name="view-restored-users-in-the-azure-portal"></a>在 Azure 門戶中查看還原的使用者

1. 使用組織中的使用者管理員的帳戶[登入 Azure AD 管理中心](https://aad.portal.azure.com)。
1. 在導覽窗格中，選取 [Azure Active Directory]****。
1. 在 [管理]  底下選取 [使用者]  。
1. 在 **「顯示**」下,選擇 **「所有使用者**」並驗證已列出還原的使用者。

### <a name="view-users-with-powershell"></a>使用 PowerShell 檢視使用者

執行以下命令：

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

您應該會看到列出您還原的使用者。

## <a name="next-steps"></a>後續步驟

- [批次匯入使用者](users-bulk-add.md)
- [大量刪除使用者](users-bulk-delete.md)
- [下載使用者清單](users-bulk-download.md)
