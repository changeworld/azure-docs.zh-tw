---
title: Azure Log Analytics 中的警示管理方案 | Microsoft Docs
description: Log Analytics 中的警示管理方案可協助您分析環境中的所有警示。  除了合併 Log Analytics 內產生的警示，此方案還會將連線的 System Center Operations Manager 管理群組中的警示匯入到 Log Analytics。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2018
ms.openlocfilehash: 48a825f31a1c5f2eab2fbb71b6f030b8acb5617d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77668378"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Azure Log Analytics 中的警示管理方案

![Alert Management icon](media/alert-management-solution/icon.png)

> [!NOTE]
>  Azure 監視器現在支援[大規模管理警示](https://aka.ms/azure-alerts-overview)的增強功能，包括[System Center Operations Manager、Zabbix 或 Nagios 等監視工具](https://aka.ms/managing-alerts-other-monitoring-services)所產生的功能。
>  


警示管理解決方案可協助您分析 Log Analytics 儲存機制中的所有警示。  這些警示可能來自各種來源，包括[由 Log Analytics 所建立](../../azure-monitor/platform/alerts-overview.md)或[從 Nagios 或 Zabbix 匯入](../../azure-monitor/learn/quick-collect-linux-computer.md)的來源。 此解決方案也會從所有[連線的 System Center Operations Manager 管理群組](../../azure-monitor/platform/om-agents.md)匯入警示。

## <a name="prerequisites"></a>先決條件
此解決方案將會使用 Log Analytics 中類型為**警示**的所有記錄，因此您必須執行收集這些記錄所需的所有設定。

- 針對 Log Analytics 警示，[建立警示規則](../../azure-monitor/platform/alerts-overview.md)，以直接在儲存機制中建立警示記錄。
- 針對 Nagios 和Zabbix 警示，[設定這些伺服器](../../azure-monitor/learn/quick-collect-linux-computer.md)以將警示傳送至 Log Analytics。
- 針對 System Center Operations Manager 警示，[將 Operations Manager 管理群組連線到 Log Analytics 工作區](../../azure-monitor/platform/om-agents.md)。  在 System Center Operations Manager 中建立的任何警示會匯入至記錄分析。  

## <a name="configuration"></a>設定
使用[新增方案](../../azure-monitor/insights/solutions.md)中所述的程序，將警示管理方案新增至您的 Log Analytics 工作區。 不需要進一步的組態。

## <a name="management-packs"></a>管理組件
如果 System Center Operations Manager 管理群組已連線到 Log Analytics 工作區，當您新增此解決方案時，下列管理組件會安裝在 System Center Operations Manager 中。  管理組件不需要任何設定或維護。

* Microsoft System Center Advisor 警示管理 (Microsoft.IntelligencePacks.AlertManagement)

如需有關方案管理組件如何更新的詳細資訊，請參閱 [將 Operations Manager 連接到 Log Analytics](../../azure-monitor/platform/om-agents.md)。

## <a name="data-collection"></a>資料集合
### <a name="agents"></a>代理程式
下表描述此方案支援的連接來源。

| 連接的來源 | 支援 | 描述 |
|:--- |:--- |:--- |
| [Windows 代理程式](agent-windows.md) | 否 |直接的 Windows 代理程式不會產生警示。  您可以從收集自 Windows 代理程式的事件和效能資料建立 Log Analytics 警示。 |
| [Linux 代理程式](../../azure-monitor/learn/quick-collect-linux-computer.md) | 否 |直接的 Linux 代理程式不會產生警示。  您可以從收集自 Linux 代理程式的事件和效能資料建立 Log Analytics 警示。  您可以從需要 Linux 代理程式的伺服器收集 Nagios 和 Zabbix 警示。 |
| [System Center Operations Manager 管理群組](../../azure-monitor/platform/om-agents.md) |是 |Operations Manager 代理程式上產生的警示會傳遞至管理群組，然後轉送到 Log Analytics。<br><br>不需要直接從 Operations Manager 代理程式連線到 Log Analytics。 警示資料會從管理群組轉送至 Log Analytics 儲存機制。 |


### <a name="collection-frequency"></a>收集頻率
- 將警示記錄儲存於儲存機制之後，方案隨即能夠使用這些記錄。
- 警示資料每 3 分鐘從 Operations Manager 管理群組傳送至 Log Analytics。  

## <a name="using-the-solution"></a>使用解決方案
當您將警示管理解決方案新增至 Log Analytics 工作區時，[警示管理]**** 圖格會新增至儀表板。  此圖格會顯示過去 24 小時內產生的目前作用中警示數目的計數和圖形表示。  您無法變更此時間範圍。

![Alert Management tile](media/alert-management-solution/tile.png)

按一下 [警示管理]**** 圖格以開啟 [警示管理]**** 儀表板。  此儀表板包含下表中的資料行。  每個資料行依計數列出前 10 個警示，這幾個警示符合該資料行中指定範圍和時間範圍的準則。  您可以按一下資料行底部的 [查看全部] **** ，或按一下資料行標頭，以執行記錄搜尋來提供完整清單。

| 資料行 | 描述 |
|:--- |:--- |
| 重大警示 |嚴重性為「重大」的所有警示 (依警示名稱分組)。  按一下警示名稱來執行記錄搜尋，以傳回該警示的所有記錄。 |
| 警告警示 |嚴重性為「警告」的所有警示 (依警示名稱分組)。  按一下警示名稱來執行記錄搜尋，以傳回該警示的所有記錄。 |
| 作用中 System Center Operations Manager 警示 |來自 Operations Manager 且狀態不為 [已關閉]** 的所有警示，並依產生此警示的來源分組。 |
| 所有作用中警示 |具有任何嚴重性的所有警示 (依警示名稱分組)。 只包含 [已關閉]** 以外任何狀態的 Operations Manager 警示。 |

如果您向右捲動，儀表板會列出數個常見的查詢，按一下即可執行警示資料的[記錄搜尋](../../azure-monitor/log-query/log-query-overview.md)。

![警示管理儀表板](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Log Analytics 記錄
警示管理方案會分析 **警示**類型的任何記錄。  方案不會直接收集由 Log Analytics 所建立或收集自 Nagios 或 Zabbix 的警示。

解決方案會從 System Center Operations Manager 匯入警示，並針對類型為**警示**且 SourceSystem 為 **OpsManager** 的每個警示建立對應的記錄。  這些記錄具有下表中的屬性：  

| 屬性 | 說明 |
|:--- |:--- |
| `Type` |*警示* |
| `SourceSystem` |*OpsManager* |
| `AlertContext` |造成產生警示的資料項目的詳細資料 (XML 格式)。 |
| `AlertDescription` |警示的詳細描述。 |
| `AlertId` |警示的 GUID。 |
| `AlertName` |警示的名稱。 |
| `AlertPriority` |警示的優先順序層級。 |
| `AlertSeverity` |警示的嚴重性層級。 |
| `AlertState` |警示的最新解決狀態。 |
| `LastModifiedBy` |上次修改警示的使用者名稱。 |
| `ManagementGroupName` |產生警示的管理群組名稱。 |
| `RepeatCount` |相同受監視物件的同一個警示自從解決後又產生的次數。 |
| `ResolvedBy` |解決警示的使用者名稱。 如果尚未解決警示，則為空白。 |
| `SourceDisplayName` |產生警示的監視物件的顯示名稱。 |
| `SourceFullName` |產生警示的監視物件的完整名稱。 |
| `TicketId` |如果 System Center Operations Manager 環境與指派警示票證的程序已整合，則此值為警示的票證識別碼。  如果未指派票證識別碼，則為空白。 |
| `TimeGenerated` |建立警示的日期和時間。 |
| `TimeLastModified` |上次變更警示的日期和時間。 |
| `TimeRaised` |產生警示的日期和時間。 |
| `TimeResolved` |解決警示的日期和時間。 如果尚未解決警示，則為空白。 |

## <a name="sample-log-searches"></a>記錄搜尋範例
下表提供此解決方案所收集的警示記錄的記錄搜尋範例： 

| 查詢 | 描述 |
|:---|:---|
| Alert &#124; where SourceSystem == "OpsManager" and AlertSeverity == "error" and TimeRaised > ago(24h) |過去 24 小時期間引發的重大警示 |
| Alert &#124; where AlertSeverity == "warning" and TimeRaised > ago(24h) |過去 24 小時期間引發的警告警示 |
| Alert &#124; where SourceSystem == "OpsManager" and AlertState != "Closed" and TimeRaised > ago(24h) &#124; summarize Count = count() by SourceDisplayName |來源和過去 24 小時期間引發的作用中警示 |
| Alert &#124; where SourceSystem == "OpsManager" and AlertSeverity == "error" and TimeRaised > ago(24h) and AlertState != "Closed" |過去 24 小時期間引發的重大且仍在作用中的警示 |
| Alert &#124; where SourceSystem == "OpsManager" and TimeRaised > ago(24h) and AlertState == "Closed" |過去 24 小時期間引發但現在已關閉的警示 |
| Alert &#124; where SourceSystem == "OpsManager" and TimeRaised > ago(1d) &#124; summarize Count = count() by AlertSeverity |過去 1 天期間引發的警示 (依嚴重性分組) |
| Alert &#124; where SourceSystem == "OpsManager" and TimeRaised > ago(1d) &#124; sort by RepeatCount desc |過去 1 天期間引發的警示 (依重複計數值排序) |



## <a name="next-steps"></a>後續步驟
* 如需有關從 Log Analytics 產生的警示的詳細資料，請深入了解 [Log Analytics 中的警示](../../azure-monitor/platform/alerts-overview.md) 。
