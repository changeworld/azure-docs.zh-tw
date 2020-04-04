---
title: 監視工作負荷 - Azure 門戶
description: 使用 Azure 門戶監視同步 SQL
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 9eacb813c3ddce028fcd9b24c86c6d32ed7a7584
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633215"
---
# <a name="monitor-workload---azure-portal"></a>監視工作負荷 - Azure 門戶

本文介紹如何使用 Azure 門戶監視工作負荷。 這包括設置 Azure 監視器日誌,以便使用[Synapse SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/)的日誌分析來調查查詢執行和工作負載趨勢。

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶：如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
- SQL 池:我們將為 SQL 池收集日誌。 如果沒有預配 SQL 池,請參閱[創建 SQL 池](load-data-from-azure-blob-storage-using-polybase.md)中的說明。

## <a name="create-a-log-analytics-workspace"></a>建立 Log Analytics 工作區

導覽到紀錄分析工作區的瀏覽邊欄選項卡,並建立工作區

![Log Analytics 工作區](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![新增分析工作區](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![新增分析工作區](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

關於工作區的詳細資訊,請造訪以下[文件](../../azure-monitor/learn/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace)。

## <a name="turn-on-diagnostic-logs"></a>開啟診斷紀錄

將診斷設置配置為從 SQL 池發出日誌。 日誌由遙測視圖組成,等效於最常用的性能故障排除 DMV。 目前支援以下檢視:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)

![啟用診斷記錄](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

日誌可以發送到 Azure 儲存、流分析或日誌分析。 對於本教程,選擇日誌分析。

![指定紀錄](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>依紀錄分析執行查詢

導覽到紀錄分析工作區,您可以在其中執行以下操作:

- 使用紀錄查詢分析紀錄並儲存查詢以供重用
- 儲存查詢以供重用
- 建立記錄警示
- 將查詢結果固定到儀表板

關於紀錄查詢功能的詳細資訊,請造訪以下[文件](../../azure-monitor/log-query/query-language.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。

![紀錄分析工作區編輯器](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![紀錄分析工作區查詢](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>範例紀錄查詢

```Kusto
//List all queries
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```

```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart
```

```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits"
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```

## <a name="next-steps"></a>後續步驟

現在,您已經設置並配置了 Azure 監視器日誌,[請自定義 Azure 儀表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)以跨團隊共用。
