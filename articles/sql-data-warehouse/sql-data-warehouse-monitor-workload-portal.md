---
title: 監視工作負載-Azure 入口網站
description: 使用 Azure 入口網站監視 SQL 分析
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 7e93aee405d8a66d850a4e3f07f2e788f1004ef8
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197216"
---
# <a name="monitor-workload---azure-portal"></a>監視工作負載-Azure 入口網站

本文說明如何使用 Azure 入口網站來監視您的工作負載。 這包括使用適用于[SQL 分析](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/)的 log analytics，設定 Azure 監視器記錄來調查查詢執行和工作負載趨勢。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶：如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
- SQL 集區：我們將會收集 SQL 集區的記錄檔。 如果您尚未布建 SQL 集區，請參閱[建立 sql 集](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial)區中的指示。

## <a name="create-a-log-analytics-workspace"></a>建立 Log Analytics 工作區

流覽至 Log Analytics 工作區的流覽分頁，並建立工作區 

![Log Analytics 工作區](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![新增分析工作區](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![新增分析工作區](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

如需工作區的詳細資訊，請流覽下列[檔](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace#create-a-workspace)。

## <a name="turn-on-diagnostic-logs"></a>開啟診斷記錄

設定診斷設定，以從您的 SQL 集區發出記錄。 記錄包含的遙測資料檢視相當於最常使用的效能疑難排解 Dmv。 目前支援下列視圖：

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![啟用診斷記錄](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

記錄可以發出至 Azure 儲存體、串流分析或 Log Analytics。 在本教學課程中，請選取 [Log Analytics]。

![指定記錄檔](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>對 Log Analytics 執行查詢

流覽至您的 Log Analytics 工作區，您可以在其中執行下列動作：

- 使用記錄查詢分析記錄並儲存查詢以重複使用
- 儲存查詢以重複使用
- 建立記錄警示
- 將查詢結果釘選到儀表板

如需記錄查詢功能的詳細資訊，請流覽下列[檔](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)。

![Log Analytics 工作區編輯器](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![Log Analytics 工作區查詢](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>範例記錄查詢



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

既然您已設定並設定 Azure 監視器記錄，請[自訂 azure 儀表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)，以便在您的小組之間共用。
