---
title: 在 Log Analytics 工作區中收集並分析 Azure 活動記錄 |Microsoft Docs
description: 收集 Azure 監視器記錄中的 Azure 活動記錄，並使用監視解決方案來分析和搜尋所有 Azure 訂用帳戶的 Azure 活動記錄。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/30/2019
ms.openlocfilehash: 044f974d83eba098820639e67412110329d5ad7d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78379383"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>在 Azure 監視器的 Log Analytics 工作區中收集並分析 Azure 活動記錄

> [!WARNING]
> 您現在可以使用與收集資源記錄的方式類似的診斷設定，將活動記錄收集到 Log Analytics 工作區。 請參閱[在 Azure 監視器中收集和分析 Log Analytics 工作區中的 Azure 活動記錄](diagnostic-settings-legacy.md)。

[Azure 活動記錄](platform-logs-overview.md)可讓您深入瞭解 azure 訂用帳戶中所發生的訂用帳戶層級事件。 本文說明如何將活動記錄檔收集到 Log Analytics 工作區，以及如何使用活動記錄分析[監視解決方案](../insights/solutions.md)，以提供記錄查詢和用於分析此資料的視圖。 

將活動記錄連接到 Log Analytics 工作區可提供下列優點：

- 將多個 Azure 訂用帳戶的活動記錄合併到一個位置以進行分析。
- 儲存活動記錄專案超過90天。
- 將活動記錄資料與 Azure 監視器所收集的其他監視資料相互關聯。
- 使用[記錄查詢](../log-query/log-query-overview.md)來執行複雜的分析，並取得活動記錄專案的深入見解。

## <a name="connect-to-log-analytics-workspace"></a>連接到 Log Analytics 工作區
單一工作區可以連接到相同 Azure 租使用者中多個訂用帳戶的活動記錄。 針對跨多個租使用者的集合，請參閱[將 Azure 活動記錄收集到不同 Azure Active Directory 租使用者中訂用帳戶之間的 Log Analytics 工作區](activity-log-collect-tenants.md)。

> [!IMPORTANT]
> 如果未針對您的訂用帳戶註冊 Microsoft.operationalinsights 和 Microsoft.operationsmanagement 資源提供者，您可能會收到下列程式的錯誤。 若要註冊這些提供者，請參閱[Azure 資源提供者和類型](../../azure-resource-manager/management/resource-providers-and-types.md)。

使用下列程式，將活動記錄連線到您的 Log Analytics 工作區：

1. 從 Azure 入口網站中的  **Log Analytics 工作區** 功能表，選取要收集活動記錄的工作區。
1. 在工作區功能表的 [**工作區資料來源**] 區段中，選取 [ **Azure 活動記錄**]。
1. 按一下您要連接的訂用帳戶。

    ![工作區](media/activity-log-export/workspaces.png)

1. 按一下 [連線 **]** ，將訂用帳戶中的活動記錄連接到選取的工作區。 如果訂用帳戶已連接到另一個工作區，請按一下 [先**中斷**連線] 將其中斷連接。

    ![連接工作區](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>在 Log Analytics 工作區中分析
當您將活動記錄檔連接到 Log Analytics 工作區時，專案會寫入至名為**AzureActivity**的資料表，您可以使用[記錄查詢](../log-query/log-query-overview.md)來加以抓取。 這個資料表的結構會根據[記錄專案的類別](activity-log-view.md#categories-in-the-activity-log)而有所不同。 如需每個類別的說明，請參閱[Azure 活動記錄事件架構](activity-log-schema.md)。

## <a name="activity-logs-analytics-monitoring-solution"></a>活動記錄分析監視解決方案
Azure Log Analytics 監視解決方案包含多個記錄查詢和視圖，可用於分析 Log Analytics 工作區中的活動記錄檔記錄。

### <a name="install-the-solution"></a>安裝解決方案
使用[安裝監視解決方案](../insights/solutions.md#install-a-monitoring-solution)中的程式來安裝**活動記錄分析**解決方案。 不需要進行其他設定。

### <a name="use-the-solution"></a>使用解決方案
監視解決方案可從 Azure 入口網站中的 [**監視**] 功能表存取。 在 [**深入**解析] 區段中選取 [**更多**]，以開啟具有方案磚的 [**總覽**] 頁面。 [ **Azure 活動記錄**] 圖格會顯示工作區中**AzureActivity**記錄的數目。

![Azure 活動記錄圖格](media/collect-activity-logs/azure-activity-logs-tile.png)


按一下 [ **Azure 活動記錄**] 圖格，以開啟 [ **azure 活動記錄**] 視圖。 此視圖包含下表中的視覺效果元件。 每個部分會列出最多10個專案，符合所指定時間範圍內的部分準則。 您可以按一下元件底部的 [**查看全部**]，來執行記錄查詢，以傳回所有相符的記錄。

![Azure 活動記錄儀表板](media/collect-activity-logs/activity-log-dash.png)

| 視覺效果元件 | 描述 |
| --- | --- |
| Azure 活動記錄項目 | 針對您所選取的日期範圍，顯示最上層 Azure 活動記錄專案記錄總計的橫條圖，並顯示前10個活動呼叫者的清單。 按一下長條圖即可執行 `AzureActivity` 的記錄搜尋。 按一下呼叫者專案來執行記錄搜尋，以傳回該專案的所有活動記錄專案。 |
| 依狀態列出的活動記錄 | 顯示所選日期範圍的 Azure 活動記錄狀態的環圈圖，以及前十筆狀態記錄的清單。 按一下圖表以執行 `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`的記錄查詢。 按一下狀態專案以執行記錄搜尋，以傳回該狀態記錄的所有活動記錄專案。 |
| 依資源列出的活動記錄 | 顯示具有活動記錄的總資源數，並列出每個資源具有記錄計數的前10個資源。 按一下總計區域即可執行 `AzureActivity | summarize AggregatedValue = count() by Resource` 的記錄搜尋，這會顯示解決方案可用的所有 Azure 資源。 按一下資源以執行記錄查詢，以傳回該資源的所有活動記錄。 |
| 依資源提供者列出的活動記錄 | 顯示產生活動記錄的資源提供者總數，並列出前十個。 按一下 [總計] 區域，以執行 `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`的記錄查詢，這會顯示所有的 Azure 資源提供者。 按一下資源提供者以執行記錄查詢，以傳回提供者的所有活動記錄。 |

## <a name="next-steps"></a>後續步驟

- 深入瞭解[活動記錄](platform-logs-overview.md)。
- 深入瞭解[Azure 監視器資料平臺](data-platform.md)。
- 使用[記錄查詢](../log-query/log-query-overview.md)，從您的活動記錄中查看詳細資訊。
