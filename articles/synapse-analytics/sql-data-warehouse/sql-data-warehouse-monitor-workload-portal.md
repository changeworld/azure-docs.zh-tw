---
title: 監視工作負荷 - Azure 門戶
description: 使用 Azure 門戶監視 SQL 分析
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 344e2cd03408b5bd1e966abc8abb72bce6078acc
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350337"
---
# <a name="monitor-workload---azure-portal"></a>監視工作負荷 - Azure 門戶

本文介紹如何使用 Azure 門戶監視工作負荷。 這包括設置 Azure 監視器日誌，以便使用 SQL[分析](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/)的日誌分析來調查查詢執行和工作負載趨勢。

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶：如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
- SQL 池：我們將為 SQL 池收集日誌。 如果沒有預配 SQL 池，請參閱[創建 SQL 池](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial)中的說明。

## <a name="create-a-log-analytics-workspace"></a>建立 Log Analytics 工作區

導航到日誌分析工作區的流覽邊欄選項卡，並創建工作區 

![Log Analytics 工作區](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![添加分析工作區](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![添加分析工作區](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

有關工作區的更多詳細資訊，請訪問以下[文檔](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace#create-a-workspace)。

## <a name="turn-on-diagnostic-logs"></a>打開診斷日誌

將診斷設置配置為從 SQL 池發出日誌。 日誌由遙測視圖組成，等效于最常用的性能故障排除 DMV。 目前支援以下視圖：

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![啟用診斷記錄](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

日誌可以發送到 Azure 存儲、流分析或日誌分析。 對於本教程，選擇日誌分析。

![指定日誌](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>根據日誌分析執行查詢

導航到日誌分析工作區，您可以在其中執行以下操作：

- 使用日誌查詢分析日誌並保存查詢以供重用
- 保存查詢以供重用
- 建立記錄警示
- 將查詢結果固定到儀表板

有關日誌查詢功能的詳細資訊，請訪問以下[文檔](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)。

![日誌分析工作區編輯器](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)



![日誌分析工作區查詢](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>示例日誌查詢



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

現在，您已經設置並配置了 Azure 監視器日誌，[請自訂 Azure 儀表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)以跨團隊共用。
