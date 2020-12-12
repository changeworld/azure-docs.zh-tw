---
title: 應用程式開發最佳做法-適用於 PostgreSQL 的 Azure 資料庫
description: 瞭解使用適用於 PostgreSQL 的 Azure 資料庫建立應用程式的最佳做法。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/10/2020
ms.openlocfilehash: 6463f30bc79d937bd5a51a5c8c78fbdd72954b1e
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2020
ms.locfileid: "97364574"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-postgresql"></a>使用適用於 PostgreSQL 的 Azure 資料庫建立應用程式的最佳作法

以下是一些最佳作法，可協助您使用適用於 PostgreSQL 的 Azure 資料庫建立雲端就緒的應用程式。 這些最佳作法可以減少應用程式的開發時間。

## <a name="configuration-of-application-and-database-resources"></a>應用程式和資料庫資源的設定

### <a name="keep-the-application-and-database-in-the-same-region"></a>將應用程式和資料庫保留在相同區域
在 Azure 中部署應用程式時，請確定您所有的相依性都在相同的區域中。 分配跨區域或可用性區域的實例會建立網路延遲，這可能會影響應用程式的整體效能。

### <a name="keep-your-postgresql-server-secure"></a>保護您的于 postgresql 伺服器安全
將您的于 postgresql 伺服器設定為 [安全](./concepts-security.md) 且無法公開存取。 您可以使用下列其中一個選項來保護您的伺服器：
- [防火牆規則](./concepts-firewall-rules.md)
- [虛擬網路](./concepts-data-access-and-security-vnet.md)
- [Azure Private Link](./concepts-data-access-and-security-private-link.md)

基於安全性，您必須一律透過 SSL 連線到您的于 postgresql 伺服器，並將您的于 postgresql 伺服器和應用程式設定為使用 TLS 1.2。 請參閱 [如何設定 SSL/TLS](./concepts-ssl-connection-security.md)。

### <a name="tune-your-server-parameters"></a>微調您的伺服器參數
針對大量讀取的工作負載，微調伺服器參數， `tmp_table_size` 並 `max_heap_table_size` 可協助優化以提升效能。 若要計算這些變數所需的值，請查看每個連接記憶體值和基本記憶體的總計。 每個連接記憶體參數的總和，但不包括 `tmp_table_size` ，結合伺服器總記憶體的基本記憶體帳戶。

### <a name="use-environment-variables-for-connection-information"></a>使用環境變數取得連接資訊
請勿將您的資料庫認證儲存在您的應用程式程式碼中。 視前端應用程式而定，請遵循設定環境變數的指導方針。如需 App service 的使用方式，請參閱如何設定[應用程式設定](../app-service/configure-common.md#configure-app-settings) 和 Azure Kuberentes service 的詳細資訊，請參閱 [如何使用 Kubernetes 秘密](https://kubernetes.io/docs/concepts/configuration/secret/)。

## <a name="performance-and-resiliency"></a>效能和復原能力
以下是一些您可以用來協助您偵測應用程式效能問題的工具和做法。

### <a name="use-connection-pooling"></a>使用連接共用
使用連接共用時，系統會在啟動時建立一組固定的連接，並加以維護。 這也有助於減少伺服器上由資料庫伺服器上建立的動態新連接所造成的記憶體分散情形。 如果應用程式架構或資料庫驅動程式支援連線共用，就可以在應用程式端設定連接共用。 如果不支援，另一個建議選項是利用 proxy 連線共用器服務，例如在應用程式外部執行的 [pgbouncer) ](https://pgbouncer.github.io/) 或 [Pgpool](https://pgpool.net/mediawiki/index.php/Main_Page) ，並連接至資料庫伺服器。 Pgbouncer) 和 Pgpool 都是可搭配適用於 PostgreSQL 的 Azure 資料庫使用的和工具。

### <a name="retry-logic-to-handle-transient-errors"></a>處理暫時性錯誤的重試邏輯
您的應用程式可能會遇到暫時性的錯誤，也就是資料庫連接中斷或中斷的情況。 在這種情況下，伺服器會在5到10秒內啟動並執行兩次重試。 理想的作法是在第一次重試前等待5秒。 然後以逐漸增加的等候次數（最多60秒），以每次重試的方式執行。 限制應用程式認為作業失敗的最大重試次數，讓您可以進一步調查。 若要深入瞭解，請參閱 [如何疑難排解連接錯誤](./concepts-connectivity.md) 。

### <a name="enable-read-replication-to-mitigate-failovers"></a>啟用讀取複寫以減少容錯移轉
您可以使用容錯移轉案例的 [資料輸入複寫](./concepts-read-replicas.md) 。 當您使用讀取複本時，不會發生來源與複本伺服器之間的自動容錯移轉。您會發現來源與複本之間的延遲，因為複寫是非同步。 網路延遲可能會受到許多因素影響，例如在來源伺服器上執行的工作負載大小，以及資料中心之間的延遲。 在大部分的情況下，複本延遲範圍從幾秒鐘到幾分鐘。


## <a name="database-deployment"></a>資料庫部署

### <a name="configure-cicd-deployment-pipeline"></a>設定 CI/CD 部署管線
有時候，您需要將變更部署至您的資料庫。 在這種情況下，您可以使用持續整合 (CI) 透過 [GitHub 動作](https://github.com/Azure/postgresql/blob/master/README.md) ，讓您的于 postgresql 伺服器對其執行自訂腳本來更新資料庫。

### <a name="define-manual-database-deployment-process"></a>定義手動資料庫部署流程
在手動部署資料庫時，請遵循下列步驟來將停機時間降到最低，或降低部署失敗的風險：

- 使用 pg_dump，在新的資料庫上建立生產資料庫的複本。
- 以新的架構變更或資料庫所需的更新，更新新的資料庫。
- 讓生產資料庫處於唯讀狀態。 在部署完成之前，您不應該在生產資料庫上有寫入作業。
- 使用步驟1中新更新的資料庫測試您的應用程式。
- 部署您的應用程式變更，並確定應用程式現在使用新的資料庫，其中包含最新的更新。
- 保留舊的生產資料庫，讓您可以復原變更。 然後，您可以評估為刪除舊的生產資料庫，或視需要將它匯出至 Azure 儲存體。

>  [!NOTE]
> 如果應用程式與電子商務應用程式類似，而且您無法將其設為唯讀狀態，請在進行備份之後，直接在生產資料庫上部署變更。 這些變更應該會在離峰時段發生，並將流量降至最低，以將影響降至最低，因為某些使用者可能會遇到失敗的要求。 請確定您的應用程式程式碼也會處理任何失敗的要求。

## <a name="database-schema-and-queries"></a>資料庫架構和查詢
當您建立資料庫架構和查詢時，請記住以下幾個秘訣。

### <a name="use-bigint-or-uuid-for-primary-keys"></a>針對主要金鑰使用 BIGINT 或 UUID
建立自訂應用程式或某些架構時，可能會使用 `INT` 而不是 `BIGINT` 主鍵。 當您使用時 ```INT``` ，您會執行資料庫中的值可能超過資料類型之儲存容量的風險 ```INT``` 。 對現有的實際執行應用程式進行這種變更，可能需要花費更多時間來進行開發。 另一個選項是使用 [UUID](https://www.postgresql.org/docs/current/datatype-uuid.html) 作為主要金鑰。例如，此識別碼會使用自動產生的128位字串 ```a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11``` 。 深入瞭解 [于 postgresql 資料類型](https://www.postgresql.org/docs/8.1/datatype.html)。

### <a name="use-indexes"></a>使用索引

Postgres 中有許多類型的 [索引](https://www.postgresql.org/docs/9.1/indexes.html) ，可以用不同的方式來使用。 使用索引可協助伺服器尋找和抓取特定的資料列，速度快于沒有索引的情況。 但是，索引也會增加資料庫伺服器的負擔，因此請避免有太多索引。

### <a name="use-autovacuum"></a>使用自動資料清理
您可以使用適用於 PostgreSQL 的 Azure 資料庫伺服器上的自動資料清理來優化您的伺服器。 于 postgresql 允許較大的資料庫並行，但每次更新都會導致插入和刪除。 若為刪除，則會以軟標示記錄，之後將會清除這些記錄。 為了執行這些工作，PostgreSQL 會執行資料清理作業。 如果您沒有定時執行資料清理，累積無效 Tuple 可能導致：

- 資料膨脹，如較大的資料庫和資料表。
- 較大且非最佳化的索引。
- I/O 增加。

深入瞭解 [如何使用自動資料清理進行優化](howto-optimize-autovacuum.md)。

### <a name="use-pg_stats_statements"></a>使用 pg_stats_statements
Pg_stat_statements 是適用於 PostgreSQL 的 Azure 資料庫中依預設啟用的 PostgreSQL 擴充功能。 此擴充功能提供了相關機制，可對伺服器執行的所有 SQL 陳述式追蹤執行統計資料。 請參閱 [如何使用 pg_statement](howto-optimize-query-stats-collection.md)。


### <a name="use-the-query-store"></a>使用查詢存放區
在適用於 PostgreSQL 的 Azure 資料庫中，[查詢存放區](./concepts-query-store.md)功能提供更有效率的方法來追蹤查詢統計資料。 我們建議使用這項功能替代 pg_stats_statements。

### <a name="optimize-bulk-inserts-and-use-transient-data"></a>優化大量插入並使用暫時性資料
如果您有工作負載作業涉及暫時性資料或大量插入大型資料集，應考慮使用未記錄的資料表。 根據預設，其提供不可部分完成的作業特性和耐久性。 Atomicity (不可部分完成的作業)、Consistency (一致性)、Isolation (隔離性) 及 Durability (耐久性) 組成 ACID 屬性。 瞭解 [如何優化大量插入](howto-optimize-bulk-inserts.md)。

## <a name="next-steps"></a>後續步驟
[Postgres 指南](http://postgresguide.com/)
