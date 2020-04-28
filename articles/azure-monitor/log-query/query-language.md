---
title: Azure 監視器記錄檔查詢 |Microsoft Docs
description: 了解如何在 Azure 監視器中撰寫記錄查詢的資源參考。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2019
ms.openlocfilehash: 6b81aba553fc775821c80631aa83bbb3e8ac63b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80631789"
---
# <a name="azure-monitor-log-queries"></a>Azure 監視器記錄查詢

Azure 監視器記錄以 Azure 資料總管為基礎建置，且 Azure 監視器記錄查詢使用相同的 Kusto 查詢語言版本。 [Kusto 查詢語言文件](/azure/kusto/query)涵蓋語言的所有詳細資料，而且應該是您用來撰寫 Azure 監視器記錄查詢的主要資源。 此頁面所提供的其他資源連結可用來了解如何撰寫查詢，以及對語言進行 Azure 監視器實作的差異。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>開始使用

- [開始使用 Azure 監視器 Log Analytics](get-started-portal.md)是在 Azure 入口網站中撰寫查詢和使用結果的課程。
- [開始使用 Azure 監視器記錄查詢](get-started-queries.md)是一堂使用 Azure 監視器記錄資料撰寫查詢的課程。

## <a name="concepts"></a>概念

- [分析 Azure 監視器中的記錄資料](../../azure-monitor/log-query/log-query-overview.md)提供記錄查詢的簡短概觀，並說明建置 Azure 監視器記錄資料結構的方式。
- [在 Azure 監視器中檢視及分析記錄資料](../../azure-monitor/log-query/portals.md)說明您要在其中建立和執行記錄查詢的入口網站。

## <a name="reference"></a>參考

- [查詢語言參考](/azure/kusto/query)是適用於 Kusto 查詢語言的完整語言參考。
- [Azure 監視器記錄查詢語言差異](data-explorer-difference.md)說明 Kusto 查詢語言版本間的差異。
- [Azure 監視器記錄記錄中的標準屬性](../../azure-monitor/platform/log-standard-properties.md)說明所有 Azure 監視器記錄資料的標準屬性。
- [在 Azure 監視器中執行跨資源記錄搜尋](../../azure-monitor/log-query/cross-workspace-query.md)說明如何撰寫記錄查詢，這類查詢會使用來自多個 Log Analytics 工作區與 Application Insights 應用程式的資料。

## <a name="examples"></a>範例

- [Azure 監視器記錄查詢範例](examples.md)提供使用 Azure 監視器記錄資料的範例查詢。

## <a name="lessons"></a>課程

- [在 Azure 監視器記錄查詢中處理字串](string-operations.md)說明如何使用字串資料。
- [在 Azure 監視器記錄查詢中處理日期時間值](datetime-operations.md)說明如何使用日期和時間資料。
- [Azuire 監視器記錄查詢中的彙總](aggregations.md)和 [Azure 監視器記錄查詢中的進階彙總](advanced-aggregations.md)說明如何彙總及摘要資料。
- [Azure 監視器記錄查詢中的聯結](joins.md)說明如何聯結多個資料表的資料。
- [在 Azure 監視器記錄查詢中處理 JSON 與資料結構](json-data-structures.md)說明如何剖析 JSON 資料。
- [在 Azure 監視器中撰寫進階記錄查詢](advanced-query-writing.md)說明用來建立複雜查詢和重複使用程式碼的策略。
- [從 Azure 監視器記錄查詢建立圖表](charts.md)說明如何將記錄查詢的資料視覺化。

## <a name="cheatsheets"></a>速查表

- [從 SQL 到 Azure 監視器記錄查詢](sql-cheatsheet.md)可協助已熟悉 SQL 的使用者。
- [從 Splunk 到 Azure 監視器記錄查詢](splunk-cheatsheet.md)可協助已熟悉 Splunk 的使用者。

## <a name="next-steps"></a>後續步驟

- 存取完整的[資料總管查詢語言的參考文件](/azure/kusto/query/)。
