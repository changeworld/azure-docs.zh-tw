---
title: 大量使用 Azure 的目錄門戶中建立使用者 |微軟文件
description: Azure 的活動目錄中的 Azure AD 管理中心批次新增使用者
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3a8b9cb9701288d24534ab08940f6dbd4a698ad
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532917"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>批次在 Azure 活動目錄中建立使用者

Azure 活動目錄 (Azure AD) 支援大量使用者建立和刪除操作、批量邀請來賓,並支援下載使用者、組和組成員的清單。

## <a name="required-permissions"></a>所需的權限

為了在管理門戶中批量創建使用者,您必須以全域管理員或使用者管理員身份登錄。

## <a name="to-create-users-in-bulk"></a>批次建立使用者

1. 使用組織中的使用者管理員的帳戶[登入 Azure AD 組織](https://aad.portal.azure.com)。
1. 在 Azure AD 中,選擇 **「使用者** > **批量創建**」 。
1. 在 **「批量創建使用者**」頁上,選擇 **「下載」** 以接收使用者屬性的有效逗號分隔值 (CSV) 檔案,然後添加要創建的添加使用者。

   ![選擇本地 CSV 檔案,其中列出要新增的使用者](./media/users-bulk-add/upload-button.png)

1. 開啟 CSV 檔案,並為要建立的每個使用者添加一行。 唯一需要的值是**名稱**、**用戶主體名稱**,**初始密碼**和**阻止登錄(是/否)。** 然後儲存檔案。

   ![CSV 檔包含要建立的使用者名稱和指示](./media/users-bulk-add/add-csv-file.png)

1. 在 **「批量創建使用者**」頁上,在「上傳 CSV 檔」下,瀏覽到該檔。 當您選擇該檔並按下「**提交**」時,將啟動 CSV 檔的驗證。
1. 驗證檔案內容後,您將看到**已成功上傳的檔案**。 如果發生錯誤，您必須修正這些錯誤，才能提交作業。
1. 檔案通過驗證後,選擇 **「提交」** 以啟動導入新使用者的 Azure 批量操作。
1. 匯入操作完成後,您將看到批量操作作業狀態的通知。

如果存在錯誤,則可以在 **「批量操作結果**」頁上下載並查看結果檔。 該檔包含每個錯誤的原因。

## <a name="check-status"></a>檢查狀態

您可以在 **「批量操作結果」** 頁中查看所有掛起的批量請求的狀態。

   [![](media/users-bulk-add/bulk-center.png "Check create status in the Bulk Operations Results page")](media/users-bulk-add/bulk-center.png#lightbox)

接下來,可以檢查所創建的使用者是否存在於 Azure 門戶或使用 PowerShell 中的 Azure AD 組織中。

## <a name="verify-users-in-the-azure-portal"></a>驗證 Azure 門戶中的使用者

1. 使用組織中的使用者管理員的帳戶[登入 Azure AD 管理中心](https://aad.portal.azure.com)。
1. 在導覽窗格中，選取 [Azure Active Directory]****。
1. 在 [管理]  底下選取 [使用者]  。
1. 在 **「顯示**」下,選擇 **「所有使用者**」並驗證是否列出了您創建的使用者。

### <a name="verify-users-with-powershell"></a>使用 PowerShell 驗證使用者

執行以下命令：

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

您應該會看到列出您創建的使用者。

## <a name="bulk-import-service-limits"></a>批次匯入服務限制

創建使用者的每個批量活動最多可以運行一小時。 這允許批量創建至少 50,000 個使用者。

## <a name="next-steps"></a>後續步驟

- [大量刪除使用者](users-bulk-delete.md)
- [下載使用者清單](users-bulk-download.md)
- [批次還原使用者](users-bulk-restore.md)
