---
title: 上傳 CSV 檔案以大容量移除群組成員-Azure Active Directory |Microsoft Docs
description: 在 Azure 系統管理中心移除大量作業中的群組成員。
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
ms.openlocfilehash: 2b3c6e471a8e44236baf9bfc2c8eb6c9d5526d72
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203437"
---
# <a name="bulk-remove-group-members-in-azure-active-directory"></a>Azure Active Directory 中的大量移除群組成員

使用 Azure Active Directory （Azure AD）入口網站，您可以使用逗號分隔值（CSV）檔案，從群組中移除大量成員，以大容量移除群組成員。

## <a name="understand-the-csv-template"></a>瞭解 CSV 範本

下載並填入大量上傳 CSV 範本，成功地將 Azure AD 群組成員大量新增。 您的 CSV 範本看起來可能像下列範例：

![用於上傳和呼叫的試算表，說明每個資料列和資料行的目的和值](./media/groups-bulk-remove-members/template-example.png)

### <a name="csv-template-structure"></a>CSV 範本結構

下載的 CSV 範本中的資料列如下所示：

- **版本號碼**：包含版本號碼的第一個資料列必須包含在上傳 CSV 中。
- 資料**行標題**：資料行標題的格式為&lt;[必要] 或 [ &lt;*空白*&gt;] 的*專案名稱*&gt; [PropertyName]。 例如： `Member object ID or user principal name [memberObjectIdOrUpn] Required` 。 某些較舊版本的範本可能會略有不同。 針對群組成員資格的變更，您可以選擇要使用的識別碼：成員物件識別碼或使用者主體名稱。
- **範例**資料列：我們在範本中包含每個資料行可接受值的一列範例。 您必須移除範例資料列，並將它取代為您自己的專案。

### <a name="additional-guidance"></a>其他指導方針

- 上傳範本的前兩個數據列不得移除或修改，或無法處理上傳。
- 首先會列出必要的資料行。
- 我們不建議您將新的資料行新增至範本。 您新增的任何其他資料行都會被忽略，而且不會處理。
- 我們建議您盡可能下載最新版本的 CSV 範本。

## <a name="to-bulk-remove-group-members"></a>大容量移除群組成員

1. 使用組織中的使用者系統管理員帳戶登入[Azure 入口網站](https://portal.azure.com)。 群組擁有者也可以大量移除他們所擁有之群組的成員。
1. 在 Azure AD 中，選取 [**群組** > ] [**所有群組**]。
1. 開啟您要移除其成員的群組，然後選取 [**成員**]。
1. 在 [**成員**] 頁面上，選取 [**移除成員**]。
1. 在 [**大量移除群組成員**] 頁面上，選取 [**下載**] 以取得具有必要群組成員屬性的 CSV 檔案範本。

   ![[移除成員] 命令位於群組的 [設定檔] 頁面上](./media/groups-bulk-remove-members/remove-panel.png)

1. 開啟 CSV 檔案，並為您要從群組中移除的每個群組成員新增一行（必要的值為 [成員物件識別碼] 或 [使用者主體名稱]）。 然後儲存檔案。

   ![CSV 檔案包含要移除之成員的名稱和識別碼](./media/groups-bulk-remove-members/csv-file.png)

1. 在 [**大量移除群組成員**] 頁面上，于 [**上傳您的 csv**檔案] 下，流覽至檔案。 當您選取檔案時，CSV 檔案的驗證就會啟動。
1. 驗證檔案內容時，[大量匯入] 頁面會顯示 [**已成功上傳**檔案]。 如果發生錯誤，您必須修正這些錯誤，才能提交作業。
1. 當您的檔案通過驗證時，請選取 [**提交**] 來啟動 Azure 大量作業，以從群組中移除群組成員。
1. 當移除作業完成時，您會看到大量作業成功的通知。

## <a name="check-removal-status"></a>檢查移除狀態

您可以在 [**大量作業結果**] 頁面中查看所有擱置大量要求的狀態。

[![](media/groups-bulk-remove-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-remove-members/bulk-center.png#lightbox)

如需大量作業中每個明細專案的詳細資料，請選取 [ **# Success**]、[**失敗**] 或 [**要求總數**] 資料行底下的值。 如果發生失敗，將會列出失敗的原因。

## <a name="bulk-removal-service-limits"></a>大量移除服務限制

從移除群組成員清單的每個大量活動，最多可執行一小時。 這可移除至少40000個成員的清單。

## <a name="next-steps"></a>後續步驟

- [大量匯入群組成員](groups-bulk-import-members.md)
- [下載群組的成員](groups-bulk-download-members.md)
- [下載所有群組的清單](groups-bulk-download.md)
