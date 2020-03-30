---
title: 稽核記錄記錄 - Azure 資料庫，用於 PostgreSQL - 單個伺服器
description: 在 Azure 資料庫中用於 PostgreSQL - 單伺服器的 pgAudit 稽核記錄記錄的概念。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 45490e398abd8b5bd3c10adb95b56e1019d2bb94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842464"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>在 Azure 資料庫中稽核記錄記錄 PostgreSQL - 單個伺服器

通過 PostgreSQL 審核擴展[：pgAudit，](https://www.pgaudit.org/)可通過 PostgreSQL - 單一伺服器 Azure 資料庫中的資料庫活動的稽核記錄記錄。 pgAudit 提供詳細的會話和/或物件稽核記錄記錄。

> [!NOTE]
> pgAudit 是在 Azure 資料庫的預覽版。
> 擴展只能在通用和記憶體優化伺服器上啟用。

如果希望 Azure 資源級日誌用於計算和存儲擴展等操作，請參閱[Azure 活動日誌](../azure-monitor/platform/platform-logs-overview.md)。

## <a name="usage-considerations"></a>使用考量
根據預設，pgAudit 記錄陳述式會與您的一般記錄陳述式一起發出，其方法是使用 Postgres 的標準記錄功能。 在適用於 PostgreSQL 的 Azure 資料庫中，可以透過 Azure 入口網站或 CLI 下載這些 .log 檔案。 檔集合的最大存儲量為 1 GB，每個檔最多可用 7 天（預設值為 3 天）。 此服務是短期存儲選項。

或者，您可以將所有日誌配置為發送到 Azure 監視器的診斷日誌服務。 如果啟用 Azure 監視器診斷日誌記錄，則日誌將自動發送到 Azure 存儲、事件中心和/或 Azure 監視器日誌（以 JSON 格式），具體取決於您的選擇。

啟用 pgAudit 會在伺服器上產生大量記錄，這會影響效能和記錄儲存體。 建議您使用 Azure 診斷記錄服務，它會提供長期儲存選項，以及分析和警示功能。 我們建議您關閉標準記錄功能，以降低其他記錄功能的效能影響：

   1. 將參數`logging_collector`設置為 OFF。 
   2. 重新開機伺服器以應用此更改。

要瞭解如何將日誌記錄設置為 Azure 存儲、事件中心或 Azure 監視器日誌，請訪問[伺服器日誌文章](concepts-server-logs.md)的診斷日誌部分。

## <a name="installing-pgaudit"></a>安裝 pgAudit

要安裝 pgAudit，您需要將其包含在伺服器的共用預載入庫中。 對 Postgres 參數的`shared_preload_libraries`更改需要重新開機伺服器才能生效。 可以使用[Azure 門戶](howto-configure-server-parameters-using-portal.md)Azure [CLI](howto-configure-server-parameters-using-cli.md)或 REST [API](/rest/api/postgresql/configurations/createorupdate)更改參數。

使用[Azure 門戶](https://portal.azure.com)：

   1. 選取適用於 PostgreSQL 的 Azure 資料庫伺服器。
   2. 在側邊欄上，選擇**伺服器參數**。
   3. 搜尋 `shared_preload_libraries` 參數。
   4. 選擇**pgaudit**。
   5. 重新開機伺服器以應用更改。

   6. 使用用戶端（如 psql）連接到伺服器，並啟用 pgAudit 擴展
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> 如果看到錯誤，請確認在保存`shared_preload_libraries`後重新開機伺服器。

## <a name="pgaudit-settings"></a>pgAudit 設置

pgAudit 允許您配置會話或物件稽核記錄記錄。 [會話稽核記錄記錄](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging)發出已執行語句的詳細日誌。 [物件稽核記錄記錄](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging)的審核範圍為特定關係。 您可以選擇設置一種或兩種類型的日誌記錄。 

> [!NOTE]
> pgAudit 設置是 gloaba 指定的，不能在資料庫或角色級別指定。

[安裝 pgAudit](#installing-pgaudit)後，可以配置其參數以開始日誌記錄。 [pgAudit 文檔](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings)提供了每個參數的定義。 首先測試參數，確認您正在獲得預期的行為。

> [!NOTE]
> 設置為`pgaudit.log_client`ON 會將日誌重定向到用戶端進程（如 psql），而不是寫入檔。 此設定通常應保持停用狀態。 <br> <br>
> `pgaudit.log_level`僅在打開時`pgaudit.log_client`啟用。

> [!NOTE]
> 在 PostgreSQL 的`pgaudit.log`Azure 資料庫中，無法使用`-`pgAudit 文檔中所述的（減）符號快捷方式進行設置。 所有必要的陳述式類別 (讀取、寫入等) 都應該個別指定。

### <a name="audit-log-format"></a>稽核記錄格式
每個審核條目都位於`AUDIT:`日誌行的開頭附近。 條目的其餘部分的格式在[pgAudit 文檔中](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)詳細說明。

如果您需要任何其他欄位來滿足審核要求，請使用 Postgres 參數`log_line_prefix`。 `log_line_prefix`是在每個 Postgres 日誌行開頭輸出的字串。 例如，以下`log_line_prefix`設置提供時間戳記、使用者名、資料庫名稱和進程 ID：

```
t=%m u=%u db=%d pid=[%p]:
```

要瞭解有關詳細資訊`log_line_prefix`，請訪問[PostgreSQL 文檔](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX)。

### <a name="getting-started"></a>開始使用
要快速入門，請設置為`pgaudit.log``WRITE`，然後打開日誌以查看輸出。 

## <a name="viewing-audit-logs"></a>查看稽核記錄
如果使用 .log 檔，則稽核記錄將包含在與 PostgreSQL 錯誤日誌相同的檔中。 可以從 Azure[門戶](howto-configure-server-logs-in-portal.md)或[CLI](howto-configure-server-logs-using-cli.md)下載日誌檔。 

如果使用 Azure 診斷日誌記錄，則訪問日誌的方式取決於您選擇的終結點。 有關 Azure 存儲，請參閱[日誌存儲帳戶](../azure-monitor/platform/resource-logs-collect-storage.md)一文。 有關事件中心，請參閱[流 Azure 日誌](../azure-monitor/platform/resource-logs-stream-event-hubs.md)一文。

對於 Azure 監視器日誌，日誌將發送到您選擇的工作區。 Postgres 日誌使用**Azure 診斷**收集模式，因此可以從 Azure 診斷表查詢它們。 下表中的欄位。 在[Azure 監視器日誌查詢](../azure-monitor/log-query/log-query-overview.md)概述中瞭解有關查詢和警報的詳細資訊。

您可以使用此查詢開始。 您可以根據查詢配置警報。

在最後一天搜索特定伺服器的所有 Postgres 日誌
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>後續步驟
- [瞭解有關在 Azure 資料庫中登錄 PostgreSQL 的詳細資訊](concepts-server-logs.md)
- 瞭解如何使用[Azure 門戶](howto-configure-server-parameters-using-portal.md)Azure [CLI](howto-configure-server-parameters-using-cli.md)或 REST [API](/rest/api/postgresql/configurations/createorupdate)設置參數。
