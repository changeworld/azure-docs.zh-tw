---
title: 在 Azure Active Directory 入口網站中大量建立使用者 | Microsoft Docs
description: 在 Azure AD 系統管理中心的 Azure Active Directory 中大量新增使用者
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/11/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8e6ab8fd726eaba309f0949020139901dd3712f
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92374838"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>在 Azure Active Directory 中大量建立使用者

Azure Active Directory (Azure AD) 支援大量使用者建立和刪除作業，並支援下載使用者清單。 只需填寫可從 Azure AD 入口網站下載的逗號分隔值 (CSV) 範本。

## <a name="required-permissions"></a>所需的權限

若要在系統管理入口網站中大量建立使用者，您必須以全域系統管理員或使用者系統管理員的身分登入。

## <a name="understand-the-csv-template"></a>了解 CSV 範本

下載並填寫大量上傳 CSV 範本，以協助您成功大量建立 Azure AD 使用者。 您下載的 CSV 範本可能如下列範例所示：

![要上傳的試算表，以及說明每列與每行之用途與值的圖說文字](./media/users-bulk-add/create-template-example.png)

> [!WARNING]
> 如果您只使用 CSV 範本新增一個專案，則必須保留資料列3，並將新的專案加入至資料列4。

### <a name="csv-template-structure"></a>CSV 範本結構

下載的 CSV 範本中的列如下所示：

- **版本號碼**：包含版本號碼的第一個列必須包含在上傳 CSV 中。
- **欄標題**：欄標題的格式為 &lt;項目名稱&gt; [PropertyName] &lt;必要或空白&gt;。 例如： `Name [displayName] Required` 。 某些舊版的範本可能會略有不同。
- **範例列**：我們在範本中隨附了每欄可接受之值的範例列。 您必須移除範例列，並將其取代為您自己的項目。

### <a name="additional-guidance"></a>其他指導方針

- 上傳範本的前兩列不可移除或修改，否則將無法處理上傳。
- 首先會列出必要的欄。
- 我們不建議您將新的欄新增至範本。 您額外新增的任何欄都會被忽略，且不會進行處理。
- 我們建議您盡可能頻繁下載最新版的 CSV 範本。
- 請務必確認任何欄位之前/之後都沒有非預期的空格。 針對 **使用者主體名稱**，具有這類空白字元會導致匯入失敗。

## <a name="to-create-users-in-bulk"></a>大量建立使用者

1. 使用組織中具有使用者系統管理員身分的帳戶來[登入您的 Azure AD 組織](https://aad.portal.azure.com)。
1. 在 Azure AD 中，選取 [使用者] > [大量建立]。
1. 在 [大量建立使用者] 頁面上，選取 [下載] 以接收使用者屬性的有效逗號分隔值 (CSV) 檔案，然後新增您想要建立的使用者。

   ![選取您會在其中列出想要新增之使用者的本機 CSV 檔案](./media/users-bulk-add/upload-button.png)

1. 開啟 CSV 檔案，並為您想要建立的每位使用者新增一行。 必要值只有**名稱**、**使用者主體名稱**、**初始密碼**和**封鎖登入 (是/否)** 。 然後儲存檔案。

   [![CSV 檔案包含要建立之使用者的名稱和識別碼](./media/users-bulk-add/add-csv-file.png)](./media/users-bulk-add/add-csv-file.png#lightbox)

1. 在 [大量建立使用者] 頁面的 [上傳您的 CSV 檔案] 底下，瀏覽至該檔案。 當您選取該檔案並按一下 [提交] 時，就會開始驗證 CSV 檔案。
1. 當檔案內容經過驗證後，您將會看到「已成功上傳檔案」。 如果發生錯誤，您必須修正這些錯誤，才能提交作業。
1. 當您的檔案通過驗證時，選取 [提交] 以啟動可匯入新使用者的 Azure 大量作業。
1. 當匯入作業完成時，您將會看到大量作業工作狀態的通知。

如果發生錯誤，您可以在 [大量作業結果] 頁面上，下載並檢視結果檔案。 此檔案包含每個錯誤的原因。 檔案提交必須符合提供的範本，並包含正確的欄名稱。

## <a name="check-status"></a>檢查狀態

您可以在 [大量作業結果] 頁面中，查看所有待決之大量要求的狀態。

   [![在 [大量作業結果] 頁面中檢查建立狀態](./media/users-bulk-add/bulk-center.png)](./media/users-bulk-add/bulk-center.png#lightbox)

接下來，您可以在 Azure 入口網站中或使用 PowerShell，來檢查您建立的使用者是否存在於 Azure AD 組織中。

## <a name="verify-users-in-the-azure-portal"></a>在 Azure 入口網站中驗證使用者

1. 使用組織中具有使用者系統管理員身分的帳戶來[登入 Azure AD 系統管理中心](https://aad.portal.azure.com)。
1. 在導覽窗格中，選取 [Azure Active Directory]。
1. 在 [管理] 底下選取 [使用者]。
1. 在 [顯示] 底下，選取 [所有使用者]，並確認已列出您建立的使用者。

### <a name="verify-users-with-powershell"></a>使用 PowerShell 確認使用者

執行以下命令：

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

您應該會看到已列出您建立的使用者。

## <a name="bulk-import-service-limits"></a>大量匯入服務限制

每個建立使用者的大量活動最多只能執行一小時。 這樣能夠大量建立至少 50,000 位使用者。

## <a name="next-steps"></a>後續步驟

- [大量刪除使用者](users-bulk-delete.md)
- [下載使用者清單](users-bulk-download.md)
- [大量還原使用者](users-bulk-restore.md)
