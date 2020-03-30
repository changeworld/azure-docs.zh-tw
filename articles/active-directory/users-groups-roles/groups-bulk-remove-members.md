---
title: 通過上傳 csv 檔大量刪除組成員 - Azure 活動目錄 |微軟文檔
description: 在 Azure 管理中心大量新增使用者。
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 09/11/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d384ea4749e2d0bc7edf8df7ac0508566f2f76b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517101"
---
# <a name="bulk-remove-group-members-preview-in-azure-active-directory"></a>在 Azure 活動目錄中大量刪除組成員（預覽）

使用 Azure 活動目錄 （Azure AD） 門戶，可以使用逗號分隔的值 （CSV） 檔大量刪除組成員，從群組移除大量成員。

## <a name="to-bulk-remove-group-members"></a>大量刪除組成員

1. 使用組織中的使用者管理員帳戶登錄到[Azure 門戶](https://portal.azure.com)。 組擁有者還可以大量刪除其擁有的組的成員。
1. 在 Azure AD 中，選擇 **"對所有** > **組進行**分組"。
1. 打開要從中刪除成員的組，然後選擇 **"成員**"。
1. 在 **"成員"** 頁上，選擇 **"刪除成員**"。
1. 在**大量刪除組成員（預覽）** 頁上，選擇 **"下載**"以獲取具有所需組成員屬性的 CSV 檔範本。

   !["刪除成員"命令位於組的設定檔頁面上](./media/groups-bulk-remove-members/remove-panel.png)

1. 打開 CSV 檔，並為要從群組移除的每個組成員添加一行（所需值為成員物件識別碼 或使用者主體名稱）。 然後儲存檔案。

   ![CSV 檔包含要刪除的成員的名稱和指示](./media/groups-bulk-remove-members/csv-file.png)

1. 在**大量刪除組成員（預覽）** 頁上，在 **"上傳 csv 檔**"下，流覽到該檔。 當您選取檔案時，就會開始驗證 .csv 檔案。
1. 驗證檔內容後，大量匯入頁面將顯示**已成功上載的檔**。 如果發生錯誤，您必須修正這些錯誤，才能提交作業。
1. 檔通過驗證後，選擇 **"提交"** 以啟動 Azure 批量操作，從群組移除組成員。
1. 刪除操作完成後，您將看到批量操作成功的通知。

## <a name="check-removal-status"></a>檢查刪除狀態

您可以在**批量操作結果（預覽）** 頁中查看所有掛起的批量請求的狀態。

   ![批量操作結果頁顯示批量請求狀態](./media/groups-bulk-remove-members/bulk-center.png)

有關批量操作中每個行項的詳細資訊，請選擇 **"成功****"、" 失敗**"或 **"總請求"** 列下的值。 如果發生失敗，將會列出失敗的原因。

## <a name="bulk-removal-service-limits"></a>批量拆卸服務限制

從中刪除組成員清單的每個批量活動最多可以運行一個小時。 這允許刪除至少 40，000 個成員的清單。

## <a name="next-steps"></a>後續步驟

- [大量匯入組成員](groups-bulk-import-members.md)
- [下載組成員](groups-bulk-download-members.md)
- [下載所有組的清單](groups-bulk-download.md)
