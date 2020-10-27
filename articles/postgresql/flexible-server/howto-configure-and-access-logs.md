---
title: 設定及存取記錄-具彈性的伺服器-適用於 PostgreSQL 的 Azure 資料庫
description: 如何存取適用於 PostgreSQL 的 Azure 資料庫彈性伺服器的資料庫記錄
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 319b7a40a87d29454b22a26088df313ef524dc56
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545815"
---
# <a name="configure-and-access-logs-in-azure-database-for-postgresql---flexible-server"></a>在適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器中設定和存取記錄

> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器為預覽狀態

于 postgresql 記錄可在彈性伺服器的每個節點上使用。 您可以將記錄傳送至儲存體伺服器或分析服務。 這些記錄可用來識別、疑難排解和修復設定錯誤和次佳效能。

## <a name="configure-diagnostic-settings"></a>設定診斷設定

您可以使用 Azure 入口網站、CLI、REST API 和 PowerShell 來啟用 Postgres 伺服器的診斷設定。 要選取的記錄類別為 **PostgreSQLLogs** 。

若要使用 Azure 入口網站啟用資源記錄：

1. 在入口網站中，移至 Postgres 伺服器的導覽功能表中的 [ *診斷設定* ]。
   
2. 選取 [ *新增診斷設定* ]。
   :::image type="content" source="media/howto-logging/diagnostic-settings.png" alt-text="[新增診斷設定] 按鈕":::

3. 為此設定命名。 

4. 選取您慣用的端點 (儲存體帳戶、事件中樞、log analytics) 。 

5. 選取記錄類型 **PostgreSQLLogs** 。
   :::image type="content" source="media/howto-logging/diagnostic-create-setting.png" alt-text="[新增診斷設定] 按鈕":::

7. 儲存您的設定。

若要使用 PowerShell、CLI 或 REST API 來啟用資源記錄，請造訪 [診斷設定](../../azure-monitor/platform/diagnostic-settings.md) 文章。

### <a name="access-resource-logs"></a>存取資源記錄

您存取記錄的方式取決於您選擇的端點。 如 Azure 儲存體，請參閱 [記錄儲存體帳戶](../../azure-monitor/platform/resource-logs.md#send-to-azure-storage) 的相關文章。 針對事件中樞，請參閱「 [串流 Azure 記錄](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs) 」一文。

針對 Azure 監視器記錄，會將記錄傳送至您選取的工作區。 Postgres 記錄會使用 **AzureDiagnostics** 收集模式，因此可以從 AzureDiagnostics 資料表進行查詢。 下表說明資料表中的欄位。 在 [Azure 監視器記錄查詢](../../azure-monitor/log-query/log-query-overview.md) 總覽中深入瞭解查詢和警示。

以下是您可以嘗試開始使用的查詢。 您可以根據查詢來設定警示。

在過去一天內搜尋特定伺服器的所有 Postgres 記錄

```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where Category == "PostgreSQLLogs"
| where TimeGenerated > ago(1d) 
```

搜尋所有非 localhost 連接嘗試

```kusto
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```

上述查詢會針對此工作區中的任何 Postgres 伺服器記錄，顯示過去6小時內的結果。

## <a name="next-steps"></a>下一步

- [開始使用 log analytics 查詢](../../azure-monitor/log-query/get-started-portal.md)
- 瞭解 [Azure 事件中樞](../../event-hubs/event-hubs-about.md)