---
title: 性能建議 - Azure 資料庫，用於 PostgreSQL - 單個伺服器
description: 本文介紹了 Azure 資料庫中的性能建議功能，用於 PostgreSQL - 單伺服器。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: a30af0c8bef47a37fe3439e885d3895a2c826225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768464"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Azure 資料庫中針對 PostgreSQL - 單伺服器的性能建議

**適用于：** 用於 PostgreSQL 的 Azure 資料庫 - 單伺服器版本 9.6、10、11

性能建議功能分析資料庫以創建自訂建議以提高性能。 要生成建議，分析將查看各種資料庫特徵，包括架構。 使[伺服器上的查詢存儲](concepts-query-store.md)充分利用性能建議功能。 實現任何性能建議後，應測試性能以評估這些更改的影響。 

## <a name="permissions"></a>權限
需要**擁有者**或**參與者**權限，才能使用 [效能建議] 功能執行分析。

## <a name="performance-recommendations"></a>效能建議
[效能建議](concepts-performance-recommendations.md)功能可分析整部伺服器的工作負載，來找出可能可以改善效能的索引。

從 PostgreSQL 伺服器 Azure 門戶頁上的功能表列的**智慧性能**部分打開**性能建議**。

![[效能建議] 登陸頁面](./media/concepts-performance-recommendations/performance-recommendations-page.png)

選擇 **"分析**並選擇一個資料庫"，該資料庫將開始分析。 根據您的工作負載，分析可能需要幾分鐘才能完成。 分析完成後，入口網站中會有通知。 分析對資料庫進行深度檢查。 我們建議您在非尖峰時間執行分析。 

如果找到任何建議，"**建議"** 視窗將顯示建議清單。

![效能建議新頁面](./media/concepts-performance-recommendations/performance-recommendations-result.png)

建議不會自動應用。 要應用建議，請複製查詢文本並從您選擇的用戶端運行它。 記得測試和監視以評估建議。 

## <a name="recommendation-types"></a>建議類型

目前，支援兩種類型的建議：*創建索引*和*刪除索引*。

### <a name="create-index-recommendations"></a>建立索引建議
*創建索引*建議建議新的索引，以加快工作負載中最常運行或耗時的查詢。 此建議類型需要啟用[查詢存儲](concepts-query-store.md)。 查詢存儲收集查詢資訊，並提供分析用於生成建議的詳細查詢運行時和頻率統計資訊。

### <a name="drop-index-recommendations"></a>卸除索引建議
除了檢測缺少的索引外，PostgreSQL 的 Azure 資料庫還分析現有索引的性能。 如果索引很少使用或冗余，分析器建議將其刪除。

## <a name="considerations"></a>考量
* 性能建議不適用於[讀取副本](concepts-read-replicas.md)。
## <a name="next-steps"></a>後續步驟
- 深入了解在適用於 PostgreSQL 的 Azure 資料庫中進行[監視和微調](concepts-monitoring.md)。

