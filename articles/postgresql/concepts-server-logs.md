---
title: 記錄-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 描述適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中的記錄設定、儲存和分析
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: c5515c21d89b01576bb6aae8c9d480b439dc1507
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90902710"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中的記錄

適用於 PostgreSQL 的 Azure 資料庫可讓您設定及存取 Postgres 的標準記錄。 這些記錄可用來識別、疑難排解和修復設定錯誤和次佳效能。 您可以設定和存取的記錄資訊包括錯誤、查詢資訊、自動資料清理記錄、連接和檢查點。 無法 (交易記錄檔的存取權) 。

您可以透過 Postgres 延伸模組（pgaudit）來取得審核記錄。 若要深入瞭解，請造訪「 [審核概念](concepts-audit.md) 」一文。


## <a name="configure-logging"></a>設定記錄 
您可以使用記錄伺服器參數在伺服器上設定 Postgres 標準記錄。 在每部適用於 PostgreSQL 的 Azure 資料庫伺服器上 `log_checkpoints` ， `log_connections` 預設為開啟。 有其他可供您調整來符合您記錄需求的參數： 

:::image type="content" source="./media/concepts-server-logs/log-parameters.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫 - 記錄參數":::

若要深入瞭解 Postgres 記錄參數，請造訪 Postgres 檔中的 [何時記錄](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) 和 [記錄](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) 檔區段。 大部分（但非全部） Postgres 記錄參數都可以在適用於 PostgreSQL 的 Azure 資料庫中進行設定。

若要瞭解如何在適用於 PostgreSQL 的 Azure 資料庫中設定參數，請參閱 [入口網站檔](howto-configure-server-parameters-using-portal.md) 或 [CLI 檔](howto-configure-server-parameters-using-cli.md)。 

> [!NOTE]
> 設定大量記錄檔（例如語句記錄）可能會大幅增加效能。 

## <a name="access-log-files"></a>存取 .log 檔
適用於 PostgreSQL 的 Azure 資料庫中的預設記錄格式為 .log。 此記錄檔中的範例行看起來像這樣：

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

適用於 PostgreSQL 的 Azure 資料庫提供記錄檔的短期儲存位置。 新檔案會每隔1小時或 100 MB 開始（以先達到者為准）。 記錄會附加至目前的檔案，因為它們是從 Postgres 發出。  

您可以使用參數設定此短期記錄檔儲存體的保留期限 `log_retention_period` 。 預設值為 3 天；最大值為 7 天。 短期儲存位置最多可容納 1 GB 的記錄檔。 1 GB 之後，將會刪除最舊的檔案（不論保留期限為何），以騰出空間給新的記錄。 

如需記錄檔和記錄分析的長期保留，您可以下載 .log 檔案，然後將它們移至協力廠商服務。 您可以使用 [Azure 入口網站](howto-configure-server-logs-in-portal.md) [Azure CLI](howto-configure-server-logs-using-cli.md)下載檔案。 或者，您可以設定 Azure 監視器診斷設定，以) 到長期位置的 JSON 格式，自動發出記錄 (。 在下一節中深入瞭解這個選項。 

您可以藉由將參數設定為 OFF，來停止產生 .log 檔。 `logging_collector` 如果您使用 Azure 監視器診斷設定，則建議您關閉記錄檔產生。 這項設定可降低其他記錄的效能影響。

## <a name="resource-logs"></a>資源記錄

適用於 PostgreSQL 的 Azure 資料庫已與 Azure 監視器診斷設定整合。 診斷設定可讓您將 JSON 格式的 Postgres 記錄傳送至 Azure 監視器記錄，以進行分析和警示、串流的事件中樞，以及用於封存的 Azure 儲存體。 

> [!IMPORTANT]
> 這項適用于伺服器記錄的診斷功能僅適用于一般用途和記憶體優化 [定價層](concepts-pricing-tiers.md)。


### <a name="configure-diagnostic-settings"></a>設定診斷設定

您可以使用 Azure 入口網站、CLI、REST API 和 Powershell 來啟用 Postgres 伺服器的診斷設定。 要選取的記錄類別為 **PostgreSQLLogs**。  (如果您使用 [查詢存放區](concepts-query-store.md)，也可以設定其他記錄。 ) 

若要使用 Azure 入口網站啟用資源記錄：

   1. 在入口網站中，移至 Postgres 伺服器的導覽功能表中的 [ *診斷設定* ]。
   2. 選取 [ *新增診斷設定*]。
   3. 為此設定命名。 
   4. 選取您慣用的端點 (儲存體帳戶、事件中樞、log analytics) 。 
   5. 選取記錄類型 **PostgreSQLLogs**。
   7. 儲存您的設定。

若要使用 Powershell、CLI 或 REST API 來啟用資源記錄，請造訪 [診斷設定](../azure-monitor/platform/diagnostic-settings.md) 文章。

### <a name="access-resource-logs"></a>存取資源記錄

您存取記錄的方式取決於您選擇的端點。 如 Azure 儲存體，請參閱 [記錄儲存體帳戶](../azure-monitor/platform/resource-logs-collect-storage.md) 的相關文章。 針對事件中樞，請參閱「 [串流 Azure 記錄](../azure-monitor/platform/resource-logs-stream-event-hubs.md) 」一文。

針對 Azure 監視器記錄，會將記錄傳送至您選取的工作區。 Postgres 記錄會使用 **AzureDiagnostics** 收集模式，因此可以從 AzureDiagnostics 資料表進行查詢。 下表說明資料表中的欄位。 在 [Azure 監視器記錄查詢](../azure-monitor/log-query/log-query-overview.md) 總覽中深入瞭解查詢和警示。

以下是您可以嘗試開始使用的查詢。 您可以根據查詢來設定警示。

在過去一天內搜尋特定伺服器的所有 Postgres 記錄
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where Category == "PostgreSQLLogs"
| where TimeGenerated > ago(1d) 
```

搜尋所有非 localhost 連接嘗試
```
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```
上述查詢會針對此工作區中的任何 Postgres 伺服器記錄，顯示過去6小時內的結果。

### <a name="log-format"></a>記錄格式

下表說明 **PostgreSQLLogs** 類型的欄位。 視您選擇的輸出端點而定，所含欄位及其出現順序可能會有所不同。 

|**欄位** | **說明** |
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


## <a name="next-steps"></a>下一步
- 了解如何從 [Azure 入口網站](howto-configure-server-logs-in-portal.md) 或 [Azure CLI](howto-configure-server-logs-using-cli.md) 存取記錄。
- 深入瞭解 [Azure 監視器定價](https://azure.microsoft.com/pricing/details/monitor/)。
- 深入瞭解 [audit 記錄](concepts-audit.md)
