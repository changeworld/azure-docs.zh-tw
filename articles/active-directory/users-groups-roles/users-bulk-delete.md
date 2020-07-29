---
title: 在 Azure Active Directory 入口網站中大量刪除使用者 |Microsoft Docs
description: 在 Azure Active Directory 的 Azure 系統管理中心內大量刪除使用者
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fc393279aaa6b293c2eb29099be45385ad08d9a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84731491"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Azure Active Directory 中的大量刪除使用者

使用 Azure Active Directory （Azure AD）入口網站，您可以使用逗號分隔值（CSV）檔案來大量刪除使用者，藉以移除群組中大量的成員。

## <a name="understand-the-csv-template"></a>了解 CSV 範本

下載並填入 CSV 範本，協助您成功刪除大量 Azure AD 使用者。 您下載的 CSV 範本可能如下列範例所示：

![要上傳的試算表，以及說明每列與每行之用途與值的圖說文字](./media/users-bulk-delete/understand-template.png)

### <a name="csv-template-structure"></a>CSV 範本結構

下載的 CSV 範本中的列如下所示：

- **版本號碼**：包含版本號碼的第一個列必須包含在上傳 CSV 中。
- **欄標題**：欄標題的格式為 &lt;項目名稱&gt; [PropertyName] &lt;必要或空白&gt;。 例如： `User name [userPrincipalName] Required` 。 某些舊版的範本可能會略有不同。
- **範例列**：我們在範本中隨附了每欄可接受之值的範例列。 您必須移除範例列，並將其取代為您自己的項目。

### <a name="additional-guidance"></a>其他指導方針

- 上傳範本的前兩列不可移除或修改，否則將無法處理上傳。
- 首先會列出必要的欄。
- 我們不建議您將新的欄新增至範本。 您額外新增的任何欄都會被忽略，且不會進行處理。
- 我們建議您盡可能頻繁下載最新版的 CSV 範本。

## <a name="to-bulk-delete-users"></a>大量刪除使用者

1. 使用組織中具有使用者系統管理員身分的帳戶來[登入您的 Azure AD 組織](https://aad.portal.azure.com)。
1. 在 Azure AD 中，選取 [**使用者**] [  >  **大量刪除**]。
1. 在 [**大量刪除使用者**] 頁面上，選取 [**下載**] 以接收使用者屬性的有效 CSV 檔案。

   ![選取您要刪除的使用者所在的本機 CSV 檔案](./media/users-bulk-delete/bulk-delete.png)

1. 開啟 CSV 檔案，並為您要刪除的每個使用者新增一行。 唯一必要的值是 [**使用者主體名稱**]。 然後儲存檔案。

   ![CSV 檔案包含要刪除之使用者的名稱和識別碼](./media/users-bulk-delete/delete-csv-file.png)

1. 在 [**大量刪除使用者**] 頁面上的 [**上傳您的 csv**檔案] 底下，流覽至檔案。 當您選取檔案並按一下 [提交] 時，CSV 檔案的驗證就會啟動。
1. 當檔案內容經過驗證後，您會看到「檔案已成功上傳」。 如果發生錯誤，您必須修正這些錯誤，才能提交作業。
1. 當您的檔案通過驗證時，請選取 [**提交**] 來啟動刪除使用者的 Azure 大量操作。
1. 當刪除作業完成時，您會看到大量作業成功的通知。

如果發生錯誤，您可以在 [大量作業結果] 頁面上，下載並檢視結果檔案。 此檔案包含每個錯誤的原因。

## <a name="check-status"></a>檢查狀態

您可以在 [大量作業結果] 頁面中，查看所有待決之大量要求的狀態。

   [![](media/users-bulk-delete/bulk-center.png "Check delete status in the Bulk Operations Results page")](media/users-bulk-delete/bulk-center.png#lightbox)

接下來，您可以檢查您刪除的使用者是否存在於 Azure 入口網站中的 Azure AD 組織，或使用 PowerShell。

## <a name="verify-deleted-users-in-the-azure-portal"></a>確認 Azure 入口網站中已刪除的使用者

1. 使用組織中的「使用者管理員」帳戶來登入 Azure 入口網站。
1. 在導覽窗格中，選取 [Azure Active Directory]。
1. 在 [管理] 底下選取 [使用者]。
1. 在 [**顯示**] 底下，選取 [僅限**所有使用者**]，並確認您刪除的使用者已不再列出。

### <a name="verify-deleted-users-with-powershell"></a>使用 PowerShell 驗證已刪除的使用者

執行下列命令：

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

確認您刪除的使用者已不再列出。

## <a name="next-steps"></a>後續步驟

- [[大量新增使用者](users-bulk-add.md)
- [下載使用者清單](users-bulk-download.md)
- [大量還原使用者](users-bulk-restore.md)
