---
title: 邏輯解碼-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 描述適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中變更資料捕捉的邏輯解碼和 wal2json
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/22/2020
ms.openlocfilehash: 4ab4a64fa395c105ced8e47cdcec019373f7f835
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708606"
---
# <a name="logical-decoding"></a>邏輯解碼
 
[于 postgresql 中的邏輯解碼](https://www.postgresql.org/docs/current/logicaldecoding.html) 可讓您將資料變更串流至外部取用者。 邏輯解碼是用於事件串流處理和變更資料捕獲案例的也就一般。

邏輯解碼會使用輸出外掛程式將 Postgres 的 write log (WAL) 轉換為可讀取的格式。 適用於 PostgreSQL 的 Azure 資料庫提供輸出外掛程式 [wal2json](https://github.com/eulerto/wal2json)、 [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) 和 pgoutput。 pgoutput 可從 Postgres 10 版和更新版本中 Postgres 取得。

如需 Postgres 邏輯解碼運作方式的總覽，請 [造訪我們的 blog](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/change-data-capture-in-postgres-how-to-use-logical-decoding-and/ba-p/1396421)。 

> [!NOTE]
> 邏輯解碼在適用於 PostgreSQL 的 Azure 資料庫單一伺服器上處於公開預覽狀態。


## <a name="set-up-your-server"></a>設定您的伺服器 
邏輯解碼和 [讀取複本](concepts-read-replicas.md) 都取決於 Postgres 寫入的記錄 (WAL) 取得資訊。 這兩個功能需要來自 Postgres 的不同記錄層級。 邏輯解碼需要比讀取複本更高層級的記錄。

若要設定正確的記錄層級，請使用 Azure 複寫支援參數。 Azure 複寫支援有三個設定選項：

* **Off** -將最少的資訊放在 WAL 中。 這項設定無法在大部分的適用於 PostgreSQL 的 Azure 資料庫伺服器上使用。  
* **複本**-更**詳細的資訊。** 這是 [讀取複本](concepts-read-replicas.md) 正常運作所需的最小記錄層級。 這項設定在大部分伺服器上是預設值。
* **邏輯** 更詳細的資訊比 **複本**更詳細。 這是要讓邏輯解碼正常運作的最小記錄層級。 讀取複本也可在此設定中運作。

此參數變更之後，必須重新開機伺服器。 就內部而言，此參數會設定 Postgres 參數 `wal_level` 、 `max_replication_slots` 和 `max_wal_senders` 。

### <a name="using-azure-cli"></a>使用 Azure CLI

1. 將 azure.replication_support 設定為 `logical` 。
   ```
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ``` 

2. 重新開機伺服器以套用變更。
   ```
   az postgres server restart --resource-group mygroup --name myserver
   ```

### <a name="using-azure-portal"></a>使用 Azure 入口網站

1. 將 Azure 複寫支援設定為 **邏輯**。 選取 [儲存]****。

   :::image type="content" source="./media/concepts-logical/replication-support.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫複寫-Azure 複寫支援":::

2. 選取 **[是]**，重新開機伺服器以套用變更。

   :::image type="content" source="./media/concepts-logical/confirm-restart.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫複寫-Azure 複寫支援":::


## <a name="start-logical-decoding"></a>啟動邏輯解碼

邏輯解碼可透過串流通訊協定或 SQL 介面取用。 這兩種方法 [都使用複寫](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS)位置。 位置代表單一資料庫的變更資料流程。

使用複寫位置需要 Postgres 的複寫許可權。 目前，只有伺服器的系統管理員使用者可以使用複寫許可權。 

### <a name="streaming-protocol"></a>串流通訊協定
通常最好使用串流處理通訊協定來使用變更。 您可以建立自己的取用者/連接器，或使用 [Debezium](https://debezium.io/)之類的工具。 

如需搭配 [pg_recvlogical 使用串流通訊協定的範例](https://github.com/eulerto/wal2json#pg_recvlogical)，請參閱 wal2json 檔。


### <a name="sql-interface"></a>SQL 介面
在下列範例中，我們使用 SQL 介面搭配 wal2json 外掛程式。
 
1. 建立位置。
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. 發出 SQL 命令。 例如：
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. 使用變更。
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   輸出看起來會像這樣：
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. 使用完畢後，請卸載位置。
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```


## <a name="monitoring-slots"></a>監視插槽

您必須監視邏輯解碼。 任何未使用的複寫位置都必須卸載。 位置會保留 Postgres WAL 記錄和相關的系統目錄，直到取用者讀取變更為止。 如果您的取用者失敗或未正確設定，未耗用的記錄將會堆積並填滿您的儲存體。 此外，未耗用的記錄也會增加交易識別碼環繞的風險。 這兩種情況可能會導致伺服器無法使用。 因此，請務必持續耗用邏輯複寫位置。 如果不再使用邏輯複寫位置，請立即卸載。

Pg_replication_slots view 中的 [作用中] 資料行會指出是否有取用者連接到某個位置。
```SQL
SELECT * FROM pg_replication_slots;
```

[設定](howto-alert-on-metric.md) 使用的 *儲存體* 警示和 *複本計量的最大延隔* 時間，以在值增加超過一般閾值時通知您。 

> [!IMPORTANT]
> 您必須卸載未使用的複寫位置。 如果無法這麼做，可能會導致伺服器無法使用。

## <a name="how-to-drop-a-slot"></a>如何卸載插槽
如果您不主動耗用複寫位置，則應該將它卸載。

若要卸載使用 SQL 呼叫的複寫位置 `test_slot` ：
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> 如果您停止使用邏輯解碼，請將 azure.replication_support 變更回 `replica` 或 `off` 。 所保留的 WAL 詳細資料 `logical` 會更詳細，而且應該在邏輯解碼未使用時停用。 

 
## <a name="next-steps"></a>後續步驟

* 請造訪 Postgres 檔，以 [深入瞭解邏輯解碼](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html)。
* 如果您有關于邏輯解碼的問題，請與 [我們的小組](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) 聯繫。
* 深入瞭解 [讀取複本](concepts-read-replicas.md)。

