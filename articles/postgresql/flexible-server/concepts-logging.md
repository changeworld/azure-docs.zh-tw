---
title: 記錄-適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器
description: 描述適用於 PostgreSQL 的 Azure 資料庫彈性伺服器中的記錄設定、儲存和分析
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 852bce26e348c817b609d5ff837641741afe4461
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936603"
---
# <a name="logs-in-azure-database-for-postgresql---flexible-server"></a>適用於 PostgreSQL 的 Azure 資料庫-彈性伺服器中的記錄

> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器為預覽狀態

適用於 PostgreSQL 的 Azure 資料庫可讓您設定和存取 Postgres 的標準記錄。 這些記錄可用來識別、疑難排解和修復設定錯誤和次佳效能。 您可以設定和存取的記錄資訊包括錯誤、查詢資訊、自動資料清理記錄、連接和檢查點。 無法 (交易記錄檔的存取權) 。

您可以透過 Postgres 擴充功能取得審核記錄 `pgaudit` 。 若要深入瞭解，請造訪「 [審核概念](concepts-audit.md) 」一文。

## <a name="configure-logging"></a>設定記錄

您可以使用記錄伺服器參數在伺服器上設定 Postgres 標準記錄。 若要深入瞭解 Postgres 記錄參數，請造訪 Postgres 檔中的 [何時記錄](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) 和 [記錄](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) 檔區段。 大部分（但非全部） Postgres 記錄參數都可以在適用於 PostgreSQL 的 Azure 資料庫中進行設定。

若要瞭解如何在適用於 PostgreSQL 的 Azure 資料庫中設定參數，請參閱 [入口網站檔](howto-configure-server-parameters-using-portal.md) 或 [CLI 檔](howto-configure-server-parameters-using-cli.md)。

> [!NOTE]
> 設定大量記錄檔（例如語句記錄）可能會大幅增加效能。 

## <a name="accessing-logs"></a>存取記錄

適用於 PostgreSQL 的 Azure 資料庫已與 Azure 監視器診斷設定整合。 診斷設定可讓您將 JSON 格式的 Postgres 記錄傳送至 Azure 監視器記錄，以進行分析和警示、串流的事件中樞，以及用於封存的 Azure 儲存體。 

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


## <a name="next-steps"></a>後續步驟

- 深入瞭解如何 [設定和存取記錄](howto-configure-and-access-logs.md)。
- 深入瞭解 [Azure 監視器定價](https://azure.microsoft.com/pricing/details/monitor/)。
- 深入瞭解 [audit 記錄](concepts-audit.md)
