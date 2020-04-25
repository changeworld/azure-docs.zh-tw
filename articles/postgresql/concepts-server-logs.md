---
title: 記錄-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 描述適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中的記錄設定、儲存和分析
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 70520b464bcb26ff8f1ea10f87bbf30537dc58a0
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82131226"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中的記錄

適用於 PostgreSQL 的 Azure 資料庫可讓您設定和存取 Postgres 的標準記錄。 記錄可用來識別、疑難排解和修復設定錯誤和效能較佳。 您可以設定和存取的記錄資訊包括錯誤、查詢資訊、自動資料清理記錄、連接和檢查點。 （不提供交易記錄檔的存取權）。

您可以透過 Postgres 延伸模組 pgaudit 取得 Audit 記錄。 若要深入瞭解，請造訪[審核概念](concepts-audit.md)一文。


## <a name="configure-logging"></a>設定記錄 
您可以使用記錄伺服器參數，在您的伺服器上設定 Postgres 標準記錄。 在每個適用於 PostgreSQL 的 Azure 資料庫伺服器`log_checkpoints`上`log_connections` ，預設為開啟。 有其他可供您調整來符合您記錄需求的參數： 

![適用於 PostgreSQL 的 Azure 資料庫 - 記錄參數](./media/concepts-server-logs/log-parameters.png)

若要深入瞭解 Postgres 記錄參數，請流覽 Postgres 檔中的 [[何時記錄](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN)] 和 [[記錄哪些內容](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT)] 區段。 大部分（但非全部）的 Postgres 記錄參數都可以在適用於 PostgreSQL 的 Azure 資料庫中設定。

若要瞭解如何在適用於 PostgreSQL 的 Azure 資料庫中設定參數，請參閱[入口網站檔](howto-configure-server-parameters-using-portal.md)或[CLI 檔](howto-configure-server-parameters-using-cli.md)。 

> [!NOTE]
> 設定大量的記錄檔（例如語句記錄）可能會增加顯著的效能負擔。 

## <a name="access-log-files"></a>存取 .log 檔案
適用於 PostgreSQL 的 Azure 資料庫中的預設記錄格式為 .log。 此記錄檔中的範例行會如下所示：

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

適用於 PostgreSQL 的 Azure 資料庫會針對 .log 檔案提供短期儲存位置。 新檔案會每隔1小時或 100 MB 開始（以先發生者為准）。 記錄會附加到目前的檔案，因為它們是從 Postgres 發出的。  

您可以使用`log_retention_period`參數來設定此短期記錄儲存體的保留期限。 預設值為 3 天；最大值為 7 天。 短期儲存位置最多可以包含 1 GB 的記錄檔。 1 GB 之後，將會刪除最舊的檔案（不論保留期間），以騰出空間給新的記錄。 

針對記錄和記錄分析的長期保留，您可以下載 .log 檔案，並將它們移至協力廠商服務。 您可以使用[Azure 入口網站](howto-configure-server-logs-in-portal.md) [Azure CLI](howto-configure-server-logs-using-cli.md)下載檔案。 或者，您可以設定 Azure 監視器診斷設定，以自動將您的記錄（JSON 格式）發出至較長的位置。 在下一節中深入瞭解此選項。 

您可以藉由將參數`logging_collector`設定為 OFF，停止產生 .log 檔案。 如果您使用 Azure 監視器診斷設定，則建議您關閉 [記錄檔產生]。 此設定可降低額外記錄的效能影響。

## <a name="resource-logs"></a>資源記錄

適用於 PostgreSQL 的 Azure 資料庫已與 Azure 監視器診斷設定整合。 診斷設定可讓您將 JSON 格式的 Postgres 記錄傳送至 Azure 監視器記錄，以進行分析和警示、事件中樞以進行串流處理，以及 Azure 儲存體進行封存。 

> [!IMPORTANT]
> 此診斷功能適用于伺服器記錄，僅適用于一般用途和記憶體優化[定價層](concepts-pricing-tiers.md)。


### <a name="configure-diagnostic-settings"></a>設定診斷設定

您可以使用 [Azure 入口網站]、[CLI]、[REST API] 和 [Powershell] 來啟用 Postgres 伺服器的診斷設定。 要選取的記錄類別為**PostgreSQLLogs**。 （如果您使用[查詢存放區](concepts-query-store.md)，還有其他記錄可供您設定）。

若要使用 Azure 入口網站啟用資源記錄：

   1. 在入口網站中，移至 Postgres 伺服器導覽功能表中的 [*診斷設定*]。
   2. 選取 [*新增診斷設定*]。
   3. 將此設定命名為。 
   4. 選取您慣用的端點（儲存體帳戶、事件中樞、log analytics）。 
   5. 選取記錄類型**PostgreSQLLogs**。
   7. 儲存您的設定。

若要使用 Powershell、CLI 或 REST API 來啟用資源記錄，請造訪[診斷設定](../azure-monitor/platform/diagnostic-settings.md)一文。

### <a name="access-resource-logs"></a>存取資源記錄

您存取記錄的方式取決於您選擇的端點。 如 Azure 儲存體，請參閱[記錄儲存體帳戶](../azure-monitor/platform/resource-logs-collect-storage.md)一文。 如事件中樞，請參閱[串流 Azure 記錄](../azure-monitor/platform/resource-logs-stream-event-hubs.md)一文。

針對 Azure 監視器記錄檔，記錄會傳送至您選取的工作區。 Postgres 記錄會使用**AzureDiagnostics**收集模式，因此可以從 AzureDiagnostics 資料表進行查詢。 資料表中的欄位如下所述。 深入瞭解[Azure 監視器記錄查詢](../azure-monitor/log-query/log-query-overview.md)總覽中的查詢和警示。

以下是您可以嘗試開始的查詢。 您可以根據查詢來設定警示。

在過去一天內搜尋特定伺服器的所有 Postgres 記錄
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
```

搜尋所有非 localhost 的連接嘗試
```
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```
上述查詢會針對此工作區中的任何 Postgres 伺服器記錄顯示過去6小時內的結果。

### <a name="log-format"></a>記錄格式

下表描述**PostgreSQLLogs**類型的欄位。 視您選擇的輸出端點而定，所含欄位及其出現順序可能會有所不同。 

|**欄位** | **描述** |
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
| 前置詞 | 記錄行的前置詞 |


## <a name="next-steps"></a>後續步驟
- 了解如何從 [Azure 入口網站](howto-configure-server-logs-in-portal.md) 或 [Azure CLI](howto-configure-server-logs-using-cli.md) 存取記錄。
- 深入了解 [Azure 監視器價格](https://azure.microsoft.com/pricing/details/monitor/)。
- 深入瞭解[audit 記錄](concepts-audit.md)
