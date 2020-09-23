---
title: Audit 記錄-適用於 PostgreSQL 的 Azure 資料庫-彈性伺服器
description: 在適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器中 pgAudit audit 記錄的概念。
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 2fccf5431666990919faf7e6378b46c41d682437
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933865"
---
# <a name="audit-logging-in-azure-database-for-postgresql---flexible-server"></a>在適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器中進行 Audit 記錄

在適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器中，您可以透過於 postgresql Audit extension： [pgAudit](https://www.pgaudit.org/)，取得資料庫活動的審核記錄。 pgAudit 提供詳細的會話和/或物件審核記錄。

> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器處於預覽階段

如果您想要 Azure 資源層級的記錄來進行計算和儲存體調整等作業，請參閱 [Azure 活動記錄](../../azure-monitor/platform/platform-logs-overview.md)。

## <a name="usage-considerations"></a>使用考量
根據預設，pgAudit 記錄陳述式會與您的一般記錄陳述式一起發出，其方法是使用 Postgres 的標準記錄功能。 在適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器中，您可以將所有記錄設定為傳送至 Azure 監視器記錄存放區，以供 Log Analytics 中的後續分析之用。 如果您啟用 Azure 監視器資源記錄，將會根據您的選擇，將記錄以 JSON 格式自動傳送 () 到 Azure 儲存體、事件中樞和/或 Azure 監視器記錄。

若要瞭解如何設定記錄以 Azure 儲存體、事件中樞或 Azure 監視器記錄檔，請造訪 [server logs 文章](concepts-logging.md)的資源記錄區段。

## <a name="enabling-pgaudit"></a>啟用 pgAudit

若要啟用 pgAudit，您必須使用用戶端 (（例如 psql) ）連接到您的伺服器，然後執行下列命令來啟用 pgAudit 擴充功能：
```SQL
CREATE EXTENSION pgaudit;
```

## <a name="pgaudit-settings"></a>pgAudit 設定

pgAudit 可讓您設定會話或物件審核記錄。 [會話審核記錄](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) 會發出執行語句的詳細記錄。 [物件審核記錄](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) 是以特定關聯性為範圍的 audit。 您可以選擇設定一或兩種類型的記錄。 

> [!NOTE]
> pgAudit 設定是全域指定的，且不能在資料庫或角色層級指定。

[啟用 pgAudit](#enabling-pgaudit)之後，您可以設定其參數來開始記錄。 [PgAudit 檔](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings)會提供每個參數的定義。 先測試參數，並確認您取得的是預期的行為。

> [!NOTE]
> 將設定 `pgaudit.log_client` 為 ON 會將記錄重新導向至用戶端進程 (例如 psql) ，而不是寫入檔案。 此設定通常應保持停用狀態。 <br> <br>
> `pgaudit.log_level` 只有在開啟時，才會啟用 `pgaudit.log_client` 。

> [!NOTE]
> 在適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器中， `pgaudit.log` 無法使用 `-` (減去) 簽署快速鍵來設定，如 pgAudit 檔中所述。 所有必要的語句類別 (讀取、寫入等 ) 應該個別指定。

## <a name="audit-log-format"></a>稽核記錄檔格式
每個 audit 專案都會以 `AUDIT:` 接近記錄行開頭的方式來表示。 部分專案的格式詳細資訊請 [參閱 pgAudit 檔](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)。

## <a name="getting-started"></a>開始使用
若要快速開始使用，請將設定 `pgaudit.log` 為 `WRITE` ，並開啟您的伺服器記錄以檢查輸出。 

## <a name="viewing-audit-logs"></a>查看 audit 記錄
您存取記錄的方式取決於您選擇的端點。 如 Azure 儲存體，請參閱 [記錄儲存體帳戶](../../azure-monitor/platform/resource-logs-collect-storage.md) 的相關文章。 針對事件中樞，請參閱「 [串流 Azure 記錄](../../azure-monitor/platform/resource-logs-stream-event-hubs.md) 」一文。

針對 Azure 監視器記錄，會將記錄傳送至您選取的工作區。 Postgres 記錄會使用 **AzureDiagnostics** 收集模式，因此可以從 AzureDiagnostics 資料表進行查詢。 下表說明資料表中的欄位。 在 [Azure 監視器記錄查詢](../../azure-monitor/log-query/log-query-overview.md) 總覽中深入瞭解查詢和警示。

您可以使用此查詢來開始。 您可以根據查詢來設定警示。

在過去一天內搜尋特定伺服器之 Postgres 記錄中的所有 pgAudit 專案
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>下一步
- [瞭解如何在適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器中登入](concepts-logging.md)
- [瞭解如何在適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器中設定記錄，以及如何存取記錄](howto-configure-and-access-logs.md)