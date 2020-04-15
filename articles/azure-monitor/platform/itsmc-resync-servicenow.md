---
title: 如何手動修復 ServiceNow 同步問題
description: 重置與 ServiceNow 的連接,以便 Microsoft Azure 中的警報可以再次調用 ServiceNow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: f09f5010c18f5ea064b02f0fbbae107bf473e1f8
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313673"
---
# <a name="how-to-manually-fix-servicenow-sync-problems"></a>如何手動修復 ServiceNow 同步問題

Azure 監視器可以連接到第三方 IT 服務管理 (ITSM) 提供程式。 ServiceNow 是這些供應商之一。

出於安全原因,您可能需要刷新用於與 ServiceNow 連接的身份驗證令牌。
使用以下同步過程重新啟動連線並刷新權杖:


1. 在頂部搜尋橫幅中搜尋解決方案,然後選擇相關解決方案

    ![新增連線](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. 在解決方案螢幕中,在訂閱篩選器中選擇「全部」,然後按「服務台」進行篩選

    ![新增連線](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. 選擇 ITSM 連接的解決方案。
1. 在左側橫幅中選擇 ITSM 連接。

    ![新增連線](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. 從清單中選擇每個連接器。 
    1. 點選連接器名稱以對其進行設定
    1. 移除不再使用的任何連線器

    1. 根據合作夥伴類型根據[這些定義](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections)更新欄位

    1. 點選同步

       ![新增連線](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. 單擊"保存"

        ![新增連線](media/itsmc-resync-servicenow/save-8bit.png)

f.    檢視通知以檢視流程是否成功 

## <a name="next-steps"></a>後續步驟

瞭解有關[IT 服務管理連線](itsmc-connections.md)的更多
