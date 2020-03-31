---
title: 性能建議 - MariaDB 的 Azure 資料庫
description: 本文介紹 MariaDB Azure 資料庫中的性能建議功能
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 838a4123bd5007f987f27674862409445967a2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528093"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>適用於 MariaDB 的 Azure 資料庫中的效能建議

**適用于：** MariaDB 的 Azure 資料庫 10.2

性能建議功能分析資料庫以創建自訂建議以提高性能。 要生成建議，分析將查看各種資料庫特徵，包括架構。 使[伺服器上的查詢存儲](concepts-query-store.md)充分利用性能建議功能。 如果性能架構為"關閉"，則打開查詢存儲可啟用performance_schema以及該功能所需的性能架構工具子集。 實現任何性能建議後，應測試性能以評估這些更改的影響。

## <a name="permissions"></a>權限

需要**擁有者**或**參與者**權限，才能使用 [效能建議] 功能執行分析。

## <a name="performance-recommendations"></a>效能建議

[效能建議](concepts-performance-recommendations.md)功能可分析整部伺服器的工作負載，來找出可能可以改善效能的索引。

從 MariaDB 伺服器 Azure 門戶頁上的功能表列的**智慧性能**部分打開**性能建議**。

![[效能建議] 登陸頁面](./media/concepts-performance-recommendations/performance-recommendations-page.png)

選擇 **"分析**並選擇一個資料庫"，該資料庫將開始分析。 根據您的工作負載，分析可能需要幾分鐘才能完成。 分析完成後，入口網站中會有通知。 分析對資料庫進行深度檢查。 我們建議您在非尖峰時間執行分析。

如果找到任何建議，則 **"建議"** 視窗將顯示建議清單以及生成此建議的相關查詢 ID。 使用查詢 ID，可以使用[mysql.query_store](concepts-query-store.md#mysqlquery_store)視圖瞭解有關查詢的更多內容。

![效能建議新頁面](./media/concepts-performance-recommendations/performance-recommendations-result.png)

建議不會自動應用。 要應用建議，請複製查詢文本並從您選擇的用戶端運行它。 記得測試和監視以評估建議。

## <a name="recommendation-types"></a>建議類型

目前，僅支援*創建索引*建議。

### <a name="create-index-recommendations"></a>建立索引建議

*創建索引*建議建議新的索引，以加快工作負載中最常運行或耗時的查詢。 此建議類型需要啟用[查詢存儲](concepts-query-store.md)。 查詢存儲收集查詢資訊，並提供分析用於生成建議的詳細查詢運行時和頻率統計資訊。

## <a name="next-steps"></a>後續步驟

- 詳細瞭解有關在 MariaDB 的 Azure 資料庫中[監視和調優](concepts-monitoring.md)的問題。