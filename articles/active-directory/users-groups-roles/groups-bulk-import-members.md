---
title: 大量匯入上傳以將成員加入群組 - Azure 活動目錄 |微軟文件
description: 在 Azure 活動目錄管理中心批量添加組成員。
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
ms.openlocfilehash: 15960caa55274f06159263c1af4a6c8280e83f4e
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533461"
---
# <a name="bulk-import-group-members-in-azure-active-directory"></a>Azure 活動目錄中的批次匯入群組成員

使用 Azure 活動目錄 (Azure AD) 門戶,可以使用逗號分隔的值 (CSV) 檔批量導入組成員,將大量成員添加到組。

## <a name="to-bulk-import-group-members"></a>大量匯入群組成員

1. 使用組織中的使用者管理員帳戶登入[Azure 門戶](https://portal.azure.com)。 組擁有者還可以批量導入其擁有的組的成員。
1. 在 Azure AD 中,選擇 **「對所有** > **組進行**群組」。
1. 打開要向其添加成員的組,然後選擇 **「成員**」。。
1. 在 **「成員」** 頁上,選擇 **「導入成員**」。
1. 在**大量匯入組成員**頁上,選擇 **「下載**」以獲取具有所需組成員屬性的 CSV 檔範本。

    ![「匯入成員」指令位於群組的設定檔頁上](./media/groups-bulk-import-members/import-panel.png)

1. 打開 CSV 檔,並為要導入到組中的每個組成員添加一行(所需值為**成員物件 ID**或**用戶主體名稱**)。 然後儲存檔案。

   ![CSV 檔包含要匯入的成員名稱和指示](./media/groups-bulk-import-members/csv-file.png)

1. 在**批量匯入組成員**頁面上,在 **「上傳 csv 檔**」下,瀏覽到該檔。 選擇該檔時,將開始驗證 CSV 檔。
1. 驗證檔案內容後,批次匯入頁面會顯示**已成功上傳的檔案**。 如果發生錯誤，您必須修正這些錯誤，才能提交作業。
1. 檔案通過驗證後,選擇 **「提交」** 以啟動將組成員匯入組的 Azure 批量操作。
1. 匯入操作完成後,您將看到批量操作成功的通知。

## <a name="check-import-status"></a>檢查匯入狀態

您可以在 **「批量操作結果」** 頁中查看所有掛起的批量請求的狀態。

[![](media/groups-bulk-import-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-import-members/bulk-center.png#lightbox)

有關批次操作中每個行項目的詳細資訊,請選擇 **「成功****」、「 失敗**」 或 **「總請求」** 欄位的值。 如果發生失敗，將會列出失敗的原因。

## <a name="bulk-import-service-limits"></a>批次匯入服務限制

匯入組成員清單的每個批量活動最多可以運行一小時。 這允許輸入至少 40,000 名成員的名單。

## <a name="next-steps"></a>後續步驟

- [大量群組成員](groups-bulk-remove-members.md)
- [下載群組成員](groups-bulk-download-members.md)
- [下載所有群組清單](groups-bulk-download.md)
