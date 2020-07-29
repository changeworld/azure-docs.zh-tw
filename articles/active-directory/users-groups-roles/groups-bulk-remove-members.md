---
title: 上傳 CSV 檔案以大量移除群組成員 - Azure Active Directory | Microsoft Docs
description: 在 Azure 系統管理中心以大量作業移除群組成員。
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
ms.openlocfilehash: e9084b486681ded0c194c93f07a404f5f5e88fa6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84728464"
---
# <a name="bulk-remove-group-members-in-azure-active-directory"></a>在 Azure Active Directory 中大量移除群組成員

使用 Azure Active Directory (Azure AD) 入口網站，您可以藉由使用逗號分隔值 (CSV) 檔案來大量移除群組成員，以從群組中移除大量成員。

## <a name="understand-the-csv-template"></a>了解 CSV 範本

下載並填寫大量上傳 CSV 範本，以協助您成功大量新增 Azure AD 群組成員。 您的 CSV 範本可能如下列範例所示：

![要上傳的試算表，以及說明每列與每行之用途與值的圖說文字](./media/groups-bulk-remove-members/template-example.png)

### <a name="csv-template-structure"></a>CSV 範本結構

下載的 CSV 範本中的列如下所示：

- **版本號碼**：包含版本號碼的第一個列必須包含在上傳 CSV 中。
- **欄標題**：欄標題的格式為 &lt;項目名稱&gt; [PropertyName] &lt;必要或空白&gt;。 例如： `Member object ID or user principal name [memberObjectIdOrUpn] Required` 。 某些舊版的範本可能會略有不同。 針對群組成員資格變更，您可以選擇要使用的識別碼：成員物件識別碼或使用者主體名稱。
- **範例列**：我們在範本中隨附了每欄可接受之值的範例列。 您必須移除範例列，並將其取代為您自己的項目。

### <a name="additional-guidance"></a>其他指導方針

- 上傳範本的前兩列不可移除或修改，否則將無法處理上傳。
- 首先會列出必要的欄。
- 我們不建議您將新的欄新增至範本。 您額外新增的任何欄都會被忽略，且不會進行處理。
- 我們建議您盡可能頻繁下載最新版的 CSV 範本。

## <a name="to-bulk-remove-group-members"></a>大量移除群組成員

1. 使用組織中的使用者系統管理員帳戶來登入 [Azure 入口網站](https://portal.azure.com)。 群組擁有者也可以大量移除其所擁有群組的成員。
1. 在 Azure AD 中，選取 [群組] > [所有群組]。
1. 開啟您要移除成員的群組，然後選取 [成員]。
1. 在 [成員] 頁面上，選取 [移除成員]。
1. 在 [大量移除群組成員] 頁面上，選取 [下載]，以取得具有必要群組成員屬性的 CSV 檔案範本。

   ![[移除成員] 命令位於群組的設定檔頁面](./media/groups-bulk-remove-members/remove-panel.png)

1. 開啟 CSV 檔案，並為您要從群組中移除的每個群組成員新增一行 (必要值為成員物件識別碼或使用者主體名稱)。 然後儲存檔案。

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="CSV 檔案包含所要移除群組成員的名稱和識別碼":::

1. 在 [大量移除群組成員] 頁面上的 [上傳您的 CSV 檔案] 底下，瀏覽至該檔案。 當您選取檔案時，就會開始驗證 CSV 檔案。
1. 當檔案內容經過驗證後，大量匯入頁面就會顯示「已成功上傳檔案」。 如果發生錯誤，您必須修正這些錯誤，才能提交作業。
1. 當您的檔案通過驗證時，選取 [提交] 以啟動可從群組中移除群組成員的 Azure 大量作業。
1. 當移除作業完成時，您將會看到大量作業成功的通知。

## <a name="check-removal-status"></a>檢查移除狀態

您可以在 [大量作業結果] 頁面中，查看所有待決之大量要求的狀態。

[![](media/groups-bulk-remove-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-remove-members/bulk-center.png#lightbox)

如需大量作業中每行項目的詳細資料，請選取 [成功數目]、[失敗數目] 或 [要求總計] 欄底下的值。 如果發生失敗，將會列出失敗的原因。

## <a name="bulk-removal-service-limits"></a>大量移除服務的限制

每個要從中移除群組成員清單的大量活動最多可執行一小時。 這可移除有至少 40,000 個成員的清單。

## <a name="next-steps"></a>後續步驟

- [大量匯入群組成員](groups-bulk-import-members.md)
- [下載群組的成員](groups-bulk-download-members.md)
- [下載所有群組的清單](groups-bulk-download.md)
