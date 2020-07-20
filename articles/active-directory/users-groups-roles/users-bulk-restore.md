---
title: 在 Azure Active Directory 入口網站中大量還原已刪除的使用者 | Microsoft Docs
description: 在 Azure AD 系統管理中心的 Azure Active Directory 中大量還原已刪除的使用者
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
ms.openlocfilehash: 2800119f1a88172ea7c329fc53359bf1565a0705
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84731268"
---
# <a name="bulk-restore-deleted-users-in-azure-active-directory"></a>大量還原 Azure Active Directory 中已刪除的使用者

Azure Active Directory (Azure AD) 支援大量使用者還原作業，並支援下載使用者、群組和群組成員的清單。

## <a name="understand-the-csv-template"></a>了解 CSV 範本

下載並填寫 CSV 範本，以協助您成功大量還原 Azure AD 使用者。 您下載的 CSV 範本可能如下列範例所示：

![要上傳的試算表，以及說明每列與每行之用途與值的圖說文字](./media/users-bulk-restore/understand-template.png)

### <a name="csv-template-structure"></a>CSV 範本結構

下載的 CSV 範本中的列如下所示：

- **版本號碼**：包含版本號碼的第一個列必須包含在上傳 CSV 中。
- **欄標題**：欄標題的格式為 &lt;項目名稱&gt; [PropertyName] &lt;必要或空白&gt;。 例如： `Object ID [objectId] Required` 。 某些舊版的範本可能會略有不同。
- **範例列**：我們在範本中隨附了每欄可接受之值的範例列。 您必須移除範例列，並將其取代為您自己的項目。

### <a name="additional-guidance"></a>其他指導方針

- 上傳範本的前兩列不可移除或修改，否則將無法處理上傳。
- 首先會列出必要的欄。
- 我們不建議您將新的欄新增至範本。 您額外新增的任何欄都會被忽略，且不會進行處理。
- 我們建議您盡可能頻繁下載最新版的 CSV 範本。

## <a name="to-bulk-restore-users"></a>大量還原使用者

1. 使用 Azure AD 組織中具有使用者系統管理員身分的帳戶來[登入您的 Azure AD 組織](https://aad.portal.azure.com)。
1. 在 Azure AD 中，選取 [使用者] > [刪除]。
1. 在 [已刪除的使用者] 頁面上，選取 [大量還原] 來上傳所要還原使用者屬性的有效 CSV 檔案。

    ![在 [已刪除的使用者] 頁面上選取 [大量還原] 命令](./media/users-bulk-restore/bulk-restore.png)

1. 開啟 CSV 範本，並為您想要還原的每位使用者新增一行。 唯一必要的值是 **ObjectID**。 然後儲存檔案。

    :::image type="content" source="./media/users-bulk-restore/upload-button.png" alt-text="選取您會在其中列出想要新增之使用者的本機 CSV 檔案":::

1. 在 [大量還原] 頁面的 [上傳 CSV 檔案] 底下，瀏覽至該檔案。 當您選取該檔案並按一下 [提交] 時，就會開始驗證 CSV 檔案。
1. 當檔案內容經過驗證後，您會看到「檔案已成功上傳」。 如果發生錯誤，您必須修正這些錯誤，才能提交作業。
1. 當您的檔案通過驗證時，選取 [提交] 以啟動可還原使用者的 Azure 大量作業。
1. 當還原作業完成時，您將會看到大量作業成功的通知。

如果發生錯誤，您可以在 [大量作業結果] 頁面上，下載並檢視結果檔案。 此檔案包含每個錯誤的原因。

## <a name="check-status"></a>檢查狀態

您可以在 [大量作業結果] 頁面中，查看所有待決之大量要求的狀態。

[![](media/users-bulk-restore/bulk-center.png "Check status in the Bulk Operations Results page")](media/users-bulk-restore/bulk-center.png#lightbox)

接下來，您可以在 Azure 入口網站中或使用 PowerShell，來檢查您還原的使用者是否存在於 Azure AD 組織中。

## <a name="view-restored-users-in-the-azure-portal"></a>在 Azure 入口網站中檢視已還原的使用者

1. 使用組織中具有使用者系統管理員身分的帳戶來[登入 Azure AD 系統管理中心](https://aad.portal.azure.com)。
1. 在導覽窗格中，選取 [Azure Active Directory]。
1. 在 [管理] 底下選取 [使用者]。
1. 在 [顯示] 底下選取 [所有使用者]，並確認已列出您還原的使用者。

### <a name="view-users-with-powershell"></a>使用 PowerShell 來檢視使用者

執行以下命令：

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

您應該會看到已列出您還原的使用者。

## <a name="next-steps"></a>後續步驟

- [大量匯入使用者](users-bulk-add.md)
- [大量刪除使用者](users-bulk-delete.md)
- [下載使用者清單](users-bulk-download.md)
