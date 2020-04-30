---
title: 在 Azure Active Directory 入口網站中大量刪除使用者 |Microsoft Docs
description: 在 Azure Active Directory 的 Azure 系統管理中心內大量刪除使用者
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca30d5b050a34000fa7c6465356aba206aeaa8e4
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203334"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Azure Active Directory 中的大量刪除使用者

使用 Azure Active Directory （Azure AD）入口網站，您可以使用逗號分隔值（CSV）檔案來大量刪除使用者，藉以移除群組中大量的成員。

## <a name="understand-the-csv-template"></a>瞭解 CSV 範本

下載並填入 CSV 範本，協助您成功刪除大量 Azure AD 使用者。 您下載的 CSV 範本可能會如下列範例所示：

![用於上傳和呼叫的試算表，說明每個資料列和資料行的目的和值](./media/users-bulk-delete/understand-template.png)

### <a name="csv-template-structure"></a>CSV 範本結構

下載的 CSV 範本中的資料列如下所示：

- **版本號碼**：包含版本號碼的第一個資料列必須包含在上傳 CSV 中。
- 資料**行標題**：資料行標題的格式為&lt;[必要] 或 [ &lt;*空白*&gt;] 的*專案名稱*&gt; [PropertyName]。 例如： `User name [userPrincipalName] Required` 。 某些較舊版本的範本可能會略有不同。
- **範例**資料列：我們在範本中包含每個資料行可接受值的一列範例。 您必須移除範例資料列，並將它取代為您自己的專案。

### <a name="additional-guidance"></a>其他指導方針

- 上傳範本的前兩個數據列不得移除或修改，或無法處理上傳。
- 首先會列出必要的資料行。
- 我們不建議您將新的資料行新增至範本。 您新增的任何其他資料行都會被忽略，而且不會處理。
- 我們建議您盡可能下載最新版本的 CSV 範本。

## <a name="to-bulk-delete-users"></a>大量刪除使用者

1. 使用組織中使用者系統管理員的帳戶登[入您的 Azure AD 組織](https://aad.portal.azure.com)。
1. 在 Azure AD 中，選取 [**使用者** > ] [**大量刪除**]。
1. 在 [**大量刪除使用者**] 頁面上，選取 [**下載**] 以接收使用者屬性的有效 CSV 檔案。

   ![選取您要刪除的使用者所在的本機 CSV 檔案](./media/users-bulk-delete/bulk-delete.png)

1. 開啟 CSV 檔案，並為您要刪除的每個使用者新增一行。 唯一必要的值是 [**使用者主體名稱**]。 然後儲存檔案。

   ![CSV 檔案包含要刪除之使用者的名稱和識別碼](./media/users-bulk-delete/delete-csv-file.png)

1. 在 [**大量刪除使用者**] 頁面上的 [**上傳您的 csv**檔案] 底下，流覽至檔案。 當您選取檔案並按一下 [提交] 時，CSV 檔案的驗證就會啟動。
1. 當檔案內容經過驗證後，您會看到「檔案已成功上傳」  。 如果發生錯誤，您必須修正這些錯誤，才能提交作業。
1. 當您的檔案通過驗證時，請選取 [**提交**] 來啟動刪除使用者的 Azure 大量操作。
1. 當刪除作業完成時，您會看到大量作業成功的通知。

如果發生錯誤，您可以在 [**大量作業結果**] 頁面上下載並查看結果檔案。 檔案包含每個錯誤的原因。

## <a name="check-status"></a>檢查狀態

您可以在 [**大量作業結果**] 頁面中查看所有擱置大量要求的狀態。

   [![](media/users-bulk-delete/bulk-center.png "Check delete status in the Bulk Operations Results page")](media/users-bulk-delete/bulk-center.png#lightbox)

接下來，您可以檢查您刪除的使用者是否存在於 Azure 入口網站中的 Azure AD 組織，或使用 PowerShell。

## <a name="verify-deleted-users-in-the-azure-portal"></a>確認 Azure 入口網站中已刪除的使用者

1. 使用組織中的「使用者管理員」帳戶來登入 Azure 入口網站。
1. 在導覽窗格中，選取 [Azure Active Directory]  。
1. 在 [管理]  底下選取 [使用者]  。
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
