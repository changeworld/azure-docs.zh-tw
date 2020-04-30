---
title: 在 Azure Active Directory 入口網站中大量建立使用者 |Microsoft Docs
description: 在 Azure AD 系統管理中心的 Azure Active Directory 中加入大量使用者
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03f6e3d6edde51598b1d148469aceb1ff3b3d636
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203385"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>在 Azure Active Directory 中大量建立使用者

Azure Active Directory （Azure AD）支援大量使用者建立和刪除作業，並支援下載使用者清單。 只要填寫逗號分隔值（CSV）範本，您就可以從 Azure AD 入口網站下載。

## <a name="required-permissions"></a>所需的權限

若要在系統管理入口網站中大量建立使用者，您必須以全域管理員或使用者系統管理員的身分登入。

## <a name="understand-the-csv-template"></a>瞭解 CSV 範本

下載並填入大量上傳 CSV 範本，以協助您成功建立大量 Azure AD 使用者。 您下載的 CSV 範本可能會如下列範例所示：

![用於上傳和呼叫的試算表，說明每個資料列和資料行的目的和值](./media/users-bulk-add/create-template-example.png)

### <a name="csv-template-structure"></a>CSV 範本結構

下載的 CSV 範本中的資料列如下所示：

- **版本號碼**：包含版本號碼的第一個資料列必須包含在上傳 CSV 中。
- 資料**行標題**：資料行標題的格式為&lt;[必要] 或 [ &lt;*空白*&gt;] 的*專案名稱*&gt; [PropertyName]。 例如： `Name [displayName] Required` 。 某些較舊版本的範本可能會略有不同。
- **範例**資料列：我們在範本中包含每個資料行可接受值的一列範例。 您必須移除範例資料列，並將它取代為您自己的專案。

### <a name="additional-guidance"></a>其他指導方針

- 上傳範本的前兩個數據列不得移除或修改，或無法處理上傳。
- 首先會列出必要的資料行。
- 我們不建議您將新的資料行新增至範本。 您新增的任何其他資料行都會被忽略，而且不會處理。
- 我們建議您盡可能下載最新版本的 CSV 範本。

## <a name="to-create-users-in-bulk"></a>若要大量建立使用者

1. 使用組織中使用者系統管理員的帳戶登[入您的 Azure AD 組織](https://aad.portal.azure.com)。
1. 在 Azure AD 中，選取 [**使用者** > ] [**大量建立**]。
1. 在 [**大量建立使用者**] 頁面上，選取 [**下載**] 以接收使用者屬性的有效逗號分隔值（CSV）檔案，然後新增您想要建立的 [新增使用者]。

   ![選取您要加入的本機 CSV 檔案，其中列出您要新增的使用者](./media/users-bulk-add/upload-button.png)

1. 開啟 CSV 檔案，並為您想要建立的每個使用者新增一行。 唯一必要的值為 [**名稱**]、[**使用者主體名稱**]、[**初始密碼**] 和 [**封鎖登入] （是/否）**。 然後儲存檔案。

   [![](media/users-bulk-add/add-csv-file.png "The CSV file contains names and IDs of the users to create")](media/users-bulk-add/add-csv-file.png#lightbox)

1. 在 [**大量建立使用者**] 頁面上的 [上傳您的 CSV 檔案] 底下，流覽至檔案。 當您選取檔案並按一下 [**提交**] 時，CSV 檔案的驗證就會啟動。
1. 檔案內容經過驗證之後，您就會看到檔案**已成功上傳**。 如果發生錯誤，您必須修正這些錯誤，才能提交作業。
1. 當您的檔案通過驗證時，請選取 [**提交**] 來啟動匯入新使用者的 Azure 大量作業。
1. 當匯入作業完成時，您會看到大量操作工作狀態的通知。

如果發生錯誤，您可以在 [**大量作業結果**] 頁面上下載並查看結果檔案。 檔案包含每個錯誤的原因。

## <a name="check-status"></a>檢查狀態

您可以在 [**大量作業結果**] 頁面中查看所有擱置大量要求的狀態。

   [![](media/users-bulk-add/bulk-center.png "Check create status in the Bulk Operations Results page")](media/users-bulk-add/bulk-center.png#lightbox)

接下來，您可以檢查您所建立的使用者是否存在於 Azure 入口網站中的 Azure AD 組織，或使用 PowerShell。

## <a name="verify-users-in-the-azure-portal"></a>驗證 Azure 入口網站中的使用者

1. 使用組織中使用者系統管理員的帳戶登[入 Azure AD admin center](https://aad.portal.azure.com) 。
1. 在導覽窗格中，選取 [Azure Active Directory]  。
1. 在 [管理]  底下選取 [使用者]  。
1. 在 [**顯示**] 底下，選取 [**所有使用者**]，並確認已列出您所建立的使用者。

### <a name="verify-users-with-powershell"></a>使用 PowerShell 驗證使用者

執行下列命令：

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

您應該會看到列出您所建立的使用者。

## <a name="bulk-import-service-limits"></a>大量匯入服務限制

建立使用者的每個大量活動最多隻能執行一小時。 這可讓您大量建立至少50000個使用者。

## <a name="next-steps"></a>後續步驟

- [大量刪除使用者](users-bulk-delete.md)
- [下載使用者清單](users-bulk-download.md)
- [大量還原使用者](users-bulk-restore.md)
