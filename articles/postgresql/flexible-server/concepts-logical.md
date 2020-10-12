---
title: 邏輯複寫和邏輯解碼-適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器
description: 瞭解如何在適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器中使用邏輯複寫和邏輯解碼
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: b6689220873aaeb65337ba480e346e5d2c8020ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91707858"
---
# <a name="logical-replication-and-logical-decoding-in-azure-database-for-postgresql---flexible-server"></a>適用於 PostgreSQL 的 Azure 資料庫-彈性伺服器中的邏輯複寫和邏輯解碼

> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器為預覽狀態

針對 Postgres 第11版，適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器支援于 postgresql 的邏輯複寫和邏輯解碼功能。

## <a name="comparing-logical-replication-and-logical-decoding"></a>比較邏輯複寫和邏輯解碼
邏輯複寫和邏輯解碼有幾個相似之處。 兩者都是
* 允許您從 Postgres 複寫資料
* 使用 [預先寫入記錄 (WAL) ](https://www.postgresql.org/docs/current/wal.html) 作為變更的來源
* 使用 [邏輯](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS) 複寫位置來送出資料。 位置代表變更的資料流程。
* 使用資料表的 [複本識別屬性](https://www.postgresql.org/docs/current/sql-altertable.html#SQL-CREATETABLE-REPLICA-IDENTITY) 來判斷哪些變更可以送出
* 不要複寫 DDL 變更


這兩種技術有其差異：邏輯複寫 
* 可讓您指定要複寫的資料表或資料表集合
* 在於 postgresql 實例之間複寫資料

邏輯解碼 
* 針對資料庫中的所有資料表解壓縮變更 
* 無法在於 postgresql 實例之間直接傳送資料


## <a name="pre-requisites-for-logical-replication-and-logical-decoding"></a>邏輯複寫和邏輯解碼的先決條件

1. 將 server 參數設定 `wal_level` 為 `logical` 。
2. 重新開機伺服器以套用 `wal_level` 變更。
3. 確認您的于 postgresql 實例允許來自連線資源的網路流量。
4. 授與系統管理員使用者複寫許可權。
   ```SQL
   ALTER ROLE <adminname> WITH REPLICATION;
   ```


## <a name="using-logical-replication-and-logical-decoding"></a>使用邏輯複寫和邏輯解碼

### <a name="logical-replication"></a>邏輯複寫
邏輯複寫使用「發行者」和「訂閱者」等詞彙。 
* 發行者是您要 **從中**傳送資料的于 postgresql 資料庫。 
* 訂閱者是 **您要傳送**資料的于 postgresql 資料庫。

以下是您可以用來嘗試邏輯複寫的一些範例程式碼。

1. 連接到發行者資料庫。 建立資料表並加入一些資料。
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   INSERT INTO basic(name) VALUES ('apple');
   INSERT INTO basic(name) VALUES ('banana');
   ```

2. 建立資料表的發行集。
   ```SQL
   CREATE PUBLICATION pub FOR TABLE basic;
   ```

3. 連接到訂閱者資料庫。 使用與發行者端相同的架構來建立資料表。
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   ```

4. 建立將連接到您稍早建立之發行集的訂閱。
   ```SQL
   CREATE SUBSCRIPTION sub CONNECTION 'host=<server>.postgres.database.azure.com user=<admin> dbname=<dbname> password=<password>' PUBLICATION pub;
   ```

5. 您現在可以在「訂閱者」端查詢資料表。 您會看到它已收到來自發行者的資料。
   ```SQL
   SELECT * FROM basic;
   ```

您可以在發行者的資料表中加入更多資料列，並在訂閱者上查看變更。

請流覽于 postgresql 檔，以深入瞭解 [邏輯](https://www.postgresql.org/docs/current/logical-replication.html)複寫。

### <a name="logical-decoding"></a>邏輯解碼
邏輯解碼可透過串流通訊協定或 SQL 介面取用。 

#### <a name="streaming-protocol"></a>串流通訊協定
通常最好使用串流處理通訊協定來使用變更。 您可以建立自己的取用者/連接器，或使用協力廠商服務（例如 [Debezium](https://debezium.io/)）。 

如需搭配 [pg_recvlogical 使用串流通訊協定的範例](https://github.com/eulerto/wal2json#pg_recvlogical)，請參閱 wal2json 檔。

#### <a name="sql-interface"></a>SQL 介面 
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

請造訪于 postgresql 檔，以深入瞭解 [邏輯解碼](https://www.postgresql.org/docs/current/logicaldecoding.html)。


## <a name="monitoring"></a>監視
您必須監視邏輯解碼。 任何未使用的複寫位置都必須卸載。 位置會保留 Postgres WAL 記錄和相關的系統目錄，直到讀取變更為止。 如果您的訂閱者或取用者失敗或未正確設定，未使用的記錄將會堆積並填滿您的儲存體。 此外，未耗用的記錄也會增加交易識別碼環繞的風險。 這兩種情況可能會導致伺服器無法使用。 因此，請務必持續耗用邏輯複寫位置。 如果不再使用邏輯複寫位置，請立即卸載。

Pg_replication_slots view 中的 [作用中] 資料行會指出是否有取用者連接到某個位置。
```SQL
SELECT * FROM pg_replication_slots;
```

[設定](howto-alert-on-metrics.md)**使用的最大交易識別碼**和**儲存體**上的警示，並在值增加超過一般閾值時通知您。 

## <a name="limitations"></a>限制
* 已有彈性的伺服器目前不支援**讀取複本**-適用於 PostgreSQL 的 Azure 資料庫讀取複本。
* **插槽和 HA 容錯移轉** -在次要 AZ 的待命伺服器上無法使用主伺服器上的邏輯複寫位置。 如果您的伺服器使用區域多餘的高可用性選項，這會適用于您。 當容錯移轉至待命伺服器時，不會在待命上提供邏輯複寫位置。

## <a name="next-steps"></a>後續步驟
* 深入瞭解 [網路選項](concepts-networking.md)
* 瞭解彈性伺服器中可用的 [擴充](concepts-extensions.md) 功能
* 深入瞭解 [高可用性](concepts-high-availability.md)

