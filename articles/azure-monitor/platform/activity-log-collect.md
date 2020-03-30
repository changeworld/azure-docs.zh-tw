---
title: 在日誌分析工作區中收集 Azure 活動日誌
description: 在 Azure 監視器日誌中收集 Azure 活動日誌，並使用監視解決方案跨所有 Azure 訂閱分析和搜索 Azure 活動日誌。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/30/2019
ms.openlocfilehash: 407bff10e2480c5210d3057bcccd6c60e591c165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055318"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>在 Azure 監視器中的日誌分析工作區中收集和分析 Azure 活動日誌

> [!WARNING]
> 現在，您可以使用類似于收集資源日誌的診斷設置將活動日誌收集到日誌分析工作區中。 請參閱[在 Azure 監視器 中的日誌分析工作區中收集和分析 Azure 活動日誌](diagnostic-settings-legacy.md)。

[Azure 活動日誌](platform-logs-overview.md)提供 Azure 訂閱中發生的訂閱級事件的見解。 本文介紹如何將活動日誌收集到日誌分析工作區，以及如何使用活動日誌分析[監視解決方案](../insights/solutions.md)，該解決方案提供用於分析此資料的日誌查詢和視圖。 

將活動日誌連接到日誌分析工作區提供了以下好處：

- 將多個 Azure 訂閱的活動日誌合併到一個位置進行分析。
- 存儲活動日誌條目超過 90 天。
- 將活動日誌資料與 Azure 監視器收集的其他監視資料相關聯。
- 使用[日誌查詢](../log-query/log-query-overview.md)執行複雜的分析，並獲得對活動日誌條目的深入瞭解。

## <a name="connect-to-log-analytics-workspace"></a>連接到日誌分析工作區
單個工作區可以連接到同一 Azure 租戶中的多個訂閱的活動日誌。 有關跨多個租戶的集合，請參閱[跨不同 Azure 活動目錄租戶中的訂閱將 Azure 活動日誌收集到日誌分析工作區](activity-log-collect-tenants.md)中。

> [!IMPORTANT]
> 如果 Microsoft.運營見解和 Microsoft.運營管理資源供應商未註冊您的訂閱，您可能會收到以下過程的錯誤。 請參閱[Azure 資來源提供者和類型](../../azure-resource-manager/management/resource-providers-and-types.md)以註冊這些提供程式。

使用以下過程將活動日誌連接到日誌分析工作區：

1. 從 Azure 門戶中的 **"日誌分析"工作區**功能表中，選擇工作區以收集活動日誌。
1. 在工作區功能表的**工作區資料來源**部分中，選擇**Azure 活動日誌**。
1. 按一下要連接的訂閱。

    ![工作區](media/activity-log-export/workspaces.png)

1. 按一下 **"連接**"將訂閱中的活動日誌連接到所選工作區。 如果訂閱已連接到另一個工作區，請按一下"首先**斷開連接**"以斷開連接。

    ![連接工作區](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>在日誌分析工作區中分析
將活動日誌連接到日誌分析工作區時，條目將寫入工作區，該表名為**AzureActivity，** 您可以使用[日誌查詢](../log-query/log-query-overview.md)檢索該表。 此表的結構因[日誌條目的類別](activity-log-view.md#categories-in-the-activity-log)而異。 有關每個類別的說明，請參閱[Azure 活動日誌事件架構](activity-log-schema.md)。

## <a name="activity-logs-analytics-monitoring-solution"></a>活動日誌分析監視解決方案
Azure 日誌分析監視解決方案包括多個日誌查詢和視圖，用於分析日誌分析工作區中的活動日誌記錄。

### <a name="install-the-solution"></a>安裝解決方案
使用[安裝監視解決方案](../insights/solutions.md#install-a-monitoring-solution)中的過程安裝**活動日誌分析**解決方案。 無需其他配置。

### <a name="use-the-solution"></a>使用解決方案
監視解決方案從 Azure 門戶中的 **"監視器"** 功能表訪問。 在 **"見解"** 部分中選擇 **"更多**"以使用解決方案磁貼打開 **"概述"** 頁。 Azure**活動日誌**磁貼顯示工作區中**AzureActivity**記錄數的計數。

![Azure 活動記錄圖格](media/collect-activity-logs/azure-activity-logs-tile.png)


按一下**Azure 活動日誌**磁貼以打開**Azure 活動日誌**視圖。 視圖包括下表中的視覺化部分。 每個部件最多列出 10 個與指定時間範圍的零件條件相匹配的專案。 可以通過按一下"查看零件底部**的所有記錄**"來運行返回所有匹配記錄的日誌查詢。

![Azure 活動記錄儀表板](media/collect-activity-logs/activity-log-dash.png)

| 視覺化部分 | 描述 |
| --- | --- |
| Azure 活動記錄項目 | 顯示所選日期範圍的頂級 Azure 活動日誌條目記錄總計的橫條圖，並顯示前 10 個活動調用方的清單。 按一下長條圖即可執行 `AzureActivity` 的記錄搜尋。 按一下調用方項以運行日誌搜索，返回該專案的所有活動日誌條目。 |
| 依狀態列出的活動記錄 | 顯示所選日期範圍的 Azure 活動日誌狀態的環圈圖和前十個狀態記錄的清單。 按一下圖表可運行 的`AzureActivity | summarize AggregatedValue = count() by ActivityStatus`日誌查詢。 按一下狀態項以運行日誌搜索，返回該狀態記錄的所有活動日誌條目。 |
| 依資源列出的活動記錄 | 使用活動日誌顯示資源總數，並列出每個資源具有記錄計數的前十個資源。 按一下總計區域即可執行 `AzureActivity | summarize AggregatedValue = count() by Resource` 的記錄搜尋，這會顯示解決方案可用的所有 Azure 資源。 按一下資源以運行日誌查詢，返回該資源的所有活動記錄。 |
| 依資源提供者列出的活動記錄 | 顯示組建活動日誌的資來源提供者的總數，並列出前十名。 按一下總面積以運行 的`AzureActivity | summarize AggregatedValue = count() by ResourceProvider`日誌查詢，該查詢顯示所有 Azure 資來源提供者。 按一下資來源提供者以運行日誌查詢，返回提供程式的所有活動記錄。 |

## <a name="next-steps"></a>後續步驟

- 瞭解有關[活動日誌](platform-logs-overview.md)的更多內容。
- 瞭解有關 Azure[監視器資料平臺](data-platform.md)的更多資訊。
- 使用[日誌查詢](../log-query/log-query-overview.md)查看活動日誌中的詳細資訊。
