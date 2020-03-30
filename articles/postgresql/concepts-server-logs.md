---
title: 日誌 - Azure 資料庫，用於後格雷SQL - 單個伺服器
description: 在 Azure 資料庫中描述日誌配置、存儲和分析，用於 PostgreSQL - 單伺服器
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 2636e9a225002148e4cd79bb2176e0883aed623a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280491"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>在 Azure 資料庫中的日誌，用於 PostgreSQL - 單個伺服器
用於 PostgreSQL 的 Azure 資料庫允許您配置和訪問 Postgres 的標準日誌。 日誌可用於識別、排除和修復配置錯誤以及性能不理想。 您可以配置和訪問的日誌記錄資訊包括錯誤、查詢資訊、自動真空記錄、連接和檢查點。 （訪問事務日誌不可用）。

稽核記錄記錄通過 Postgres 擴展（pgaudit）提供。 要瞭解更多資訊，請訪問[審核概念](concepts-audit.md)一文。


## <a name="configure-logging"></a>設定記錄 
您可以使用日誌記錄伺服器參數在伺服器上配置 Postgres 標準日誌記錄。 在每個 Azure 資料庫上，用於 PostgreSQL 伺服器，`log_checkpoints`預設情況下`log_connections`處於打開狀態。 有其他可供您調整來符合您記錄需求的參數： 

![適用於 PostgreSQL 的 Azure 資料庫 - 記錄參數](./media/concepts-server-logs/log-parameters.png)

要瞭解有關 Postgres 日誌參數的更多內容，請訪問 Postgres 文檔的"[何時登錄](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN)"和["日誌內容"](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT)部分。 大多數（但不是全部）Postgres 日誌記錄參數可用於在用於 PostgreSQL 的 Azure 資料庫中進行配置。

要瞭解如何在 PostgreSQL 的 Azure 資料庫中配置參數，請參閱[門戶文檔](howto-configure-server-parameters-using-portal.md)或[CLI 文檔](howto-configure-server-parameters-using-cli.md)。 

> [!NOTE]
> 配置大量日誌（例如語句日誌記錄）可能會增加顯著的性能開銷。 

## <a name="access-log-files"></a>訪問 .日誌檔
PostgreSQL 的 Azure 資料庫中的預設日誌格式為 .log。 此日誌中的示例行如下所示：

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

PostgreSQL 的 Azure 資料庫為 .log 檔提供了短期存儲位置。 新檔每 1 小時或 100 MB 開始一次，以先到者為准。 日誌將追加到當前檔中，因為它們是從 Postgres 發出的。  

您可以使用`log_retention_period`參數設置此短期日誌存儲的保留期。 預設值為 3 天；最大值為 7 天。 短期存儲位置最多可以容納 1 GB 的日誌檔。 1 GB 後，將刪除最舊的檔，無論保留期如何，以便為新日誌騰出空間。 

為了長期保留日誌和日誌分析，您可以下載 .log 檔並將其移動到協力廠商服務。 您可以使用[Azure 門戶](howto-configure-server-logs-in-portal.md) [Azure CLI](howto-configure-server-logs-using-cli.md)下載檔案。 或者，您可以配置 Azure 監視器診斷設置，這些設置會自動將日誌（JSON 格式）發射到長期位置。 在下面的部分瞭解有關此選項的更多內容。 

通過將參數`logging_collector`設置為 OFF，可以停止生成 .log 檔。 如果使用 Azure 監視器診斷設置，建議關閉 .log 檔生成。 此配置將減少其他日誌記錄的性能影響。

## <a name="diagnostic-logs"></a>診斷記錄
用於 PostgreSQL 的 Azure 資料庫與 Azure 監視器診斷設置集成。 診斷設置允許您將以 JSON 格式的 Postgres 日誌發送到 Azure 監視器日誌以進行分析和警報、用於資料流的事件中心以及用於存檔的 Azure 存儲。 

> [!IMPORTANT]
> 伺服器日誌的此診斷功能僅在通用和記憶體優化[定價層中](concepts-pricing-tiers.md)可用。


### <a name="configure-diagnostic-settings"></a>設定診斷設定
您可以使用 Azure 門戶、CLI、REST API 和 Powershell 為 Postgres 伺服器啟用診斷設置。 要選擇的日誌類別是**PostgreSQLLogs**。 （如果使用[查詢存儲](concepts-query-store.md)，則可以配置其他日誌。

要使用 Azure 門戶啟用診斷日誌，請使用以下因素：

   1. 在門戶中，轉到 Postgres 伺服器導航功能表中的 *"診斷設置*"。
   2. 選擇 *"添加診斷設置*"。
   3. 命名此設置。 
   4. 選擇您首選的終結點（存儲帳戶、事件中心、日誌分析）。 
   5. 選擇日誌類型**PostgreSQLLogs**。
   7. 儲存您的設定。

要使用電源外殼、CLI 或 REST API 啟用診斷日誌，請訪問[診斷設置](../azure-monitor/platform/diagnostic-settings.md)一文。

### <a name="access-diagnostic-logs"></a>存取診斷記錄

訪問日誌的方式取決於您選擇的終結點。 有關 Azure 存儲，請參閱[日誌存儲帳戶](../azure-monitor/platform/resource-logs-collect-storage.md)一文。 有關事件中心，請參閱[流 Azure 日誌](../azure-monitor/platform/resource-logs-stream-event-hubs.md)一文。

對於 Azure 監視器日誌，日誌將發送到您選擇的工作區。 Postgres 日誌使用**Azure 診斷**收集模式，因此可以從 Azure 診斷表查詢它們。 下表中的欄位。 在[Azure 監視器日誌查詢](../azure-monitor/log-query/log-query-overview.md)概述中瞭解有關查詢和警報的詳細資訊。

以下是您可以嘗試入門的查詢。 您可以根據查詢配置警報。

在最後一天搜索特定伺服器的所有 Postgres 日誌
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
```

搜索所有非本地主機連接嘗試
```
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```
上面的查詢將顯示此工作區中任何 Postgres 伺服器登錄在過去 6 小時內的結果。

### <a name="log-format"></a>日誌格式

下表描述了**PostgreSQLLogs**類型的欄位。 視您選擇的輸出端點而定，所含欄位及其出現順序可能會有所不同。 

|**領域** | **描述** |
|---|---|
| TenantId | 您的租用戶識別碼 |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | 以 UTC 記錄記錄時的時間戳記 |
| 類型 | 記錄的類型。 一律為 `AzureDiagnostics` |
| SubscriptionId | 伺服器所屬訂用帳戶的 GUID |
| ResourceGroup | 伺服器所屬資源群組的名稱 |
| ResourceProvider | 資源提供者名稱。 一律為 `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | 資源 URI |
| 資源 | 伺服器的名稱 |
| 類別 | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | 記錄層級，範例：LOG、ERROR、NOTICE |
| 訊息 | 主要記錄訊息 | 
| 網域 | 伺服器版本，範例：postgres-10 |
| 詳細資料 | 次要記錄訊息 (如果適用) |
| ColumnName | 資料行的名稱 (如果適用) |
| SchemaName | 結構描述的名稱 (如果適用) |
| DatatypeName | 資料類型的名稱 (如果適用) |
| LogicalServerName | 伺服器的名稱 | 
| _ResourceId | 資源 URI |
| 前置詞 | 日誌行的首碼 |


## <a name="next-steps"></a>後續步驟
- 了解如何從 [Azure 入口網站](howto-configure-server-logs-in-portal.md) 或 [Azure CLI](howto-configure-server-logs-using-cli.md) 存取記錄。
- 深入了解 [Azure 監視器價格](https://azure.microsoft.com/pricing/details/monitor/)。
- 瞭解有關[稽核記錄的更多資訊](concepts-audit.md)
