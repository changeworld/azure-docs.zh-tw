---
title: Audit 記錄-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 在適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中 pgAudit audit 記錄的概念。
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: efd4cb7c0c5db50729539373938ebccd689dee42
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708980"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>適用於 PostgreSQL 的 Azure 資料庫中的 Audit 記錄-單一伺服器

您可以透過於 postgresql Audit Extension： [pgAudit](https://www.pgaudit.org/)，取得適用於 PostgreSQL 的 Azure 資料庫單一伺服器中的資料庫活動的審核記錄。 pgAudit 提供詳細的會話和/或物件審核記錄。

> [!NOTE]
> pgAudit 在適用於 PostgreSQL 的 Azure 資料庫上處於預覽狀態。
> 擴充功能只能在一般用途和記憶體優化伺服器上啟用。

如果您想要 Azure 資源層級的記錄來進行計算和儲存體調整等作業，請參閱 [Azure 活動記錄](../azure-monitor/platform/platform-logs-overview.md)。

## <a name="usage-considerations"></a>使用考量
根據預設，pgAudit 記錄陳述式會與您的一般記錄陳述式一起發出，其方法是使用 Postgres 的標準記錄功能。 在適用於 PostgreSQL 的 Azure 資料庫中，可以透過 Azure 入口網站或 CLI 下載這些 .log 檔案。 檔案集合的儲存空間上限為 1 GB，每個檔案最多可供使用七天 (預設值為三天) 。 這項服務是短期的儲存選項。

或者，您可以將所有記錄設定為傳送至 Azure 監視器記錄存放區，以供 Log Analytics 中的後續分析之用。 如果您啟用 Azure 監視器資源記錄，將會根據您的選擇，將記錄以 JSON 格式自動傳送 () 到 Azure 儲存體、事件中樞和/或 Azure 監視器記錄。

啟用 pgAudit 會在伺服器上產生大量記錄，這會影響效能和記錄儲存體。 建議您使用提供長期儲存選項的 Azure 監視器記錄，以及分析和警示功能。 我們建議您關閉標準記錄功能，以降低其他記錄功能的效能影響：

   1. 將參數設 `logging_collector` 為 OFF。 
   2. 重新開機伺服器以套用此變更。

若要瞭解如何設定記錄以 Azure 儲存體、事件中樞或 Azure 監視器記錄檔，請造訪 [server logs 文章](concepts-server-logs.md)的資源記錄區段。

## <a name="installing-pgaudit"></a>安裝 pgAudit

若要安裝 pgAudit，您必須將它包含在伺服器的共用預先載入程式庫中。 變更 Postgres 的 `shared_preload_libraries` 參數需要伺服器重新開機才會生效。 您可以使用 [Azure 入口網站](howto-configure-server-parameters-using-portal.md)、 [Azure CLI](howto-configure-server-parameters-using-cli.md)或 [REST API](/rest/api/postgresql/configurations/createorupdate)來變更參數。

使用 [Azure 入口網站](https://portal.azure.com)：

   1. 選取適用於 PostgreSQL 的 Azure 資料庫伺服器。
   2. 在側邊欄上選取 [ **伺服器參數**]。
   3. 搜尋 `shared_preload_libraries` 參數。
   4. 選取 [ **pgaudit**]。
   5. 重新開機伺服器以套用變更。

   6. 使用用戶端 (（例如 psql) ）連接到您的伺服器，並啟用 pgAudit 擴充功能
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> 如果您看到錯誤，請確認您已在儲存之後重新開機伺服器 `shared_preload_libraries` 。

## <a name="pgaudit-settings"></a>pgAudit 設定

pgAudit 可讓您設定會話或物件審核記錄。 [會話審核記錄](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) 會發出執行語句的詳細記錄。 [物件審核記錄](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) 是以特定關聯性為範圍的 audit。 您可以選擇設定一或兩種類型的記錄。 

> [!NOTE]
> pgAudit 設定是 gloabally 指定的，且不能在資料庫或角色層級指定。

[安裝 pgAudit](#installing-pgaudit)之後，您可以設定其參數來開始記錄。 [PgAudit 檔](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings)會提供每個參數的定義。 先測試參數，並確認您取得的是預期的行為。

> [!NOTE]
> 將設定 `pgaudit.log_client` 為 ON 會將記錄重新導向至用戶端進程 (例如 psql) ，而不是寫入檔案。 此設定通常應保持停用狀態。 <br> <br>
> `pgaudit.log_level` 只有在開啟時，才會啟用 `pgaudit.log_client` 。

> [!NOTE]
> 在適用於 PostgreSQL 的 Azure 資料庫中， `pgaudit.log` 無法使用 `-` (減去) 簽署快速鍵來設定，如 pgAudit 檔中所述。 所有必要的陳述式類別 (讀取、寫入等) 都應該個別指定。

### <a name="audit-log-format"></a>稽核記錄檔格式
每個 audit 專案都會以 `AUDIT:` 接近記錄行開頭的方式來表示。 部分專案的格式詳細資訊請 [參閱 pgAudit 檔](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)。

如果您需要任何其他欄位來滿足您的審核需求，請使用 Postgres 參數 `log_line_prefix` 。 `log_line_prefix` 這是在每個 Postgres 記錄行開頭輸出的字串。 例如，下列設定會 `log_line_prefix` 提供時間戳記、使用者名稱、資料庫名稱和處理序識別碼：

```
t=%m u=%u db=%d pid=[%p]:
```

若要深入瞭解 `log_line_prefix` ，請造訪 [于 postgresql 檔](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX)。

### <a name="getting-started"></a>開始使用
若要快速開始使用，請將設定 `pgaudit.log` 為 `WRITE` ，並開啟您的記錄來檢查輸出。 

## <a name="viewing-audit-logs"></a>查看 audit 記錄
如果您使用的是 .log 檔案，則您的 audit 記錄檔將會包含在於 postgresql 錯誤記錄檔的相同檔案中。 您可以從 Azure [入口網站](howto-configure-server-logs-in-portal.md) 或 [CLI](howto-configure-server-logs-using-cli.md)下載記錄檔。 

如果您使用 Azure 資源記錄，您存取記錄的方式取決於您選擇的端點。 如 Azure 儲存體，請參閱 [記錄儲存體帳戶](../azure-monitor/platform/resource-logs-collect-storage.md) 的相關文章。 針對事件中樞，請參閱「 [串流 Azure 記錄](../azure-monitor/platform/resource-logs-stream-event-hubs.md) 」一文。

針對 Azure 監視器記錄，會將記錄傳送至您選取的工作區。 Postgres 記錄會使用 **AzureDiagnostics** 收集模式，因此可以從 AzureDiagnostics 資料表進行查詢。 下表說明資料表中的欄位。 在 [Azure 監視器記錄查詢](../azure-monitor/log-query/log-query-overview.md) 總覽中深入瞭解查詢和警示。

您可以使用此查詢來開始。 您可以根據查詢來設定警示。

在過去一天內搜尋特定伺服器的所有 Postgres 記錄
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>後續步驟
- [瞭解適用於 PostgreSQL 的 Azure 資料庫中的登入](concepts-server-logs.md)
- 瞭解如何使用 [Azure 入口網站](howto-configure-server-parameters-using-portal.md)、 [Azure CLI](howto-configure-server-parameters-using-cli.md)或 [REST API](/rest/api/postgresql/configurations/createorupdate)來設定參數。
