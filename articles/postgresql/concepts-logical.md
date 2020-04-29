---
title: 邏輯解碼-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 描述適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中變更資料捕獲的邏輯解碼和 wal2json
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 1213b38f2b67e8fed179cfda4308943808893e1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80522143"
---
# <a name="logical-decoding"></a>邏輯解碼
 
[于 postgresql 中的邏輯解碼](https://www.postgresql.org/docs/current/logicaldecoding.html)可讓您將資料變更串流至外部取用者。 邏輯解碼是用於事件串流處理和變更資料捕獲案例的也就一般。

邏輯解碼會使用輸出外掛程式，將 Postgres 的寫入前記錄檔（WAL）轉換為可讀取的格式。 適用於 PostgreSQL 的 Azure 資料庫提供兩個輸出外掛程式： [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html)和[wal2json](https://github.com/eulerto/wal2json)。
 

> [!NOTE]
> 適用於 PostgreSQL 的 Azure 資料庫-單一伺服器上的邏輯解碼處於公開預覽狀態。


## <a name="set-up-your-server"></a>設定伺服器
若要開始使用邏輯解碼，請讓您的伺服器儲存並串流 WAL。 

1. 將 azure. replication_support 設定`logical`為使用 Azure CLI。 
   ```
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ```

   > [!NOTE]
   > 如果您使用讀取複本，azure. replication_support 設定為`logical`也允許執行複本。 如果您停止使用邏輯解碼，請將設定變更回`replica`。 


2. 重新啟動伺服器以套用變更。
   ```
   az postgres server restart --resource-group mygroup --name myserver
   ```

## <a name="start-logical-decoding"></a>開始邏輯解碼

邏輯解碼可以透過串流通訊協定或 SQL 介面來取用。 這兩種方法[都使用複寫](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS)位置。 一個位置代表來自單一資料庫之變更的資料流程。

使用複寫位置需要 Postgres 的複寫許可權。 此時，複寫許可權僅適用于伺服器的系統管理員使用者。 

### <a name="streaming-protocol"></a>串流通訊協定
通常最好是使用串流通訊協定來耗用變更。 您可以建立自己的取用者/連接器，或使用類似[Debezium](https://debezium.io/)的工具。 

如需搭配[pg_recvlogical 使用串流通訊協定的範例](https://github.com/eulerto/wal2json#pg_recvlogical)，請造訪 wal2json 檔。


### <a name="sql-interface"></a>SQL 介面
在下列範例中，我們會使用 SQL 介面搭配 wal2json 外掛程式。
 
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

您必須監視邏輯解碼。 必須卸載任何未使用的複寫位置。 位置會持續 Postgres WAL 記錄和相關的系統目錄，直到取用者讀取變更為止。 如果您的取用者失敗或未正確設定，未使用的記錄將會堆積並填滿您的儲存體。 此外，未消耗的記錄會增加交易識別碼環繞的風險。 這兩種情況都會導致伺服器無法使用。 因此，請務必持續使用邏輯複寫位置。 如果已不再使用邏輯複寫位置，請立即將它卸載。

[Pg_replication_slots] 視圖中的 [作用中] 資料行會指出是否有取用者連接到某個位置。
```SQL
SELECT * FROM pg_replication_slots;
```

[設定](howto-alert-on-metric.md)[已*使用的儲存體*] 和 [*跨複本的延遲上限*] 計量的警示，以在值超過正常閾值時通知您。 

> [!IMPORTANT]
> 您必須卸載未使用的複寫位置。 如果無法這樣做，可能會導致伺服器無法用。

## <a name="how-to-drop-a-slot"></a>如何卸載位置
如果您不主動使用複寫位置，應該將它卸載。

若要使用 SQL 卸載名`test_slot`為的複寫位置：
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> 如果您停止使用邏輯解碼，請將 azure 變更 replication_support 回`replica`或`off`。 保留`logical`的 WAL 詳細資料較詳細，而且應該在未使用邏輯解碼時停用。 

 
## <a name="next-steps"></a>後續步驟

* 若要[深入瞭解邏輯解碼](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html)，請造訪 Postgres 檔。
* 如果您有關于邏輯解碼的問題，請與[我們的小組](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)聯繫。
* 深入瞭解[讀取複本](concepts-read-replicas.md)。

