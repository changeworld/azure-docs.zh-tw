---
title: 邏輯解碼 - Azure 資料庫,用於 PostgreSQL - 單一伺服器
description: 描述邏輯解碼和 wal2json,用於在 Azure 資料庫中為 PostgreSQL - 單伺服器擷取變更資料
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 1213b38f2b67e8fed179cfda4308943808893e1b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522143"
---
# <a name="logical-decoding"></a>邏輯解碼
 
[PostgreSQL 中的邏輯解碼](https://www.postgresql.org/docs/current/logicaldecoding.html)允許您將數據更改流式傳輸到外部消費者。 邏輯解碼通常用於事件流和更改數據捕獲方案。

邏輯解碼使用輸出外掛程式將 Postgres 的寫入前日誌 (WAL) 轉換為可讀格式。 用於 PostgreSQL 的 Azure 資料庫提供兩個輸出外掛程式[:test_decoding](https://www.postgresql.org/docs/current/test-decoding.html)與[wal2json](https://github.com/eulerto/wal2json)。
 

> [!NOTE]
> 邏輯解碼在 Azure 資料庫上的公共預覽版中,用於 PostgreSQL - 單伺服器。


## <a name="set-up-your-server"></a>設定伺服器
要開始使用邏輯解碼,請使伺服器保存和流式傳輸 WAL。 

1. 將 azure.replication_support`logical`設置為使用 Azure CLI。 
   ```
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ```

   > [!NOTE]
   > 如果使用讀取副本,azure.replication_support 設置`logical`為 也允許運行副本。 如果停止使用邏輯解碼,則會設定變更回`replica`。 


2. 重新啟動伺服器以套用變更。
   ```
   az postgres server restart --resource-group mygroup --name myserver
   ```

## <a name="start-logical-decoding"></a>開始邏輯解碼

邏輯解碼可以通過流式處理協定或 SQL 介面使用。 這兩種方法都使用[複製槽](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS)。 槽表示來自單個資料庫的更改流。

使用複製槽需要 Postgres 的複製許可權。 此時,複製許可權僅適用於伺服器的管理員使用者。 

### <a name="streaming-protocol"></a>流式處理協定
使用流式處理協議進行更改通常更可取。 您可以創建自己的消費者/連接器,或使用[像Debezium](https://debezium.io/)這樣的工具。 

存取 wal2json 文件,瞭解[使用串流協定與pg_recvlogical 的範例](https://github.com/eulerto/wal2json#pg_recvlogical)。


### <a name="sql-interface"></a>SQL 介面
在下面的示例中,我們將 SQL 介面與 wal2json 外掛程式一起使用。
 
1. 創建插槽。
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

3. 使用更改。
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   輸出將如下所示:
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

4. 使用完成後,將插槽放下。
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```


## <a name="monitoring-slots"></a>監控插槽

您必須監視邏輯解碼。 必須刪除任何未使用的複製槽。 插槽會保留 Postgres WAL 日誌和相關系統目錄,直到消費者讀取更改。 如果使用者失敗或未正確配置,則未使用的日誌將堆積並填滿您的存儲。 此外,未消耗的日誌會增加事務 ID 環繞的風險。 這兩種情況都可能導致伺服器不可用。 因此,持續使用邏輯複製槽至關重要。 如果不再使用邏輯複製槽,則立即將其刪除。

pg_replication_slots檢視中的「活動」列將指示是否有使用者連接到插槽。
```SQL
SELECT * FROM pg_replication_slots;
```

在*使用的儲存*上[設定警報](howto-alert-on-metric.md),並在*副本指標上設置最大延遲*,以便在值超過正常閾值時通知您。 

> [!IMPORTANT]
> 必須刪除未使用的複製槽。 否則可能導致伺服器不可用。

## <a name="how-to-drop-a-slot"></a>如何放置插槽
如果不主動使用複製槽,則應將其丟棄。

要刪除使用 SQL`test_slot`調用的複製槽,
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> 如果停止使用邏輯解碼,則將 azure.replication_support更改`replica``off`回 或 。 保留的`logical`WAL 詳細資訊更加詳細,在不使用邏輯解碼時應禁用。 

 
## <a name="next-steps"></a>後續步驟

* 存取 Postgres 文件,[瞭解有關邏輯解碼的詳細資訊](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html)。
* 如果您對邏輯解碼有疑問,則與[我們的團隊](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)聯繫。
* 瞭解有關[讀取副本](concepts-read-replicas.md)的更多內容。

