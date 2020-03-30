---
title: 已知問題：從 PostgreSQL 到 Azure 資料庫的線上遷移，用於 PostgreSQL
titleSuffix: Azure Database Migration Service
description: 使用 Azure 資料庫移轉服務從 PostgreSQL 到 Azure 資料庫的 PostgreSQL 線上遷移，瞭解已知問題和遷移限制。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 3d1bc627ccb8814ab2dfb61fb0653ef0ac644038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235257"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql"></a>從 PostgreSQL 到 Azure DB 的連線遷移的已知問題/遷移限制，用於 PostgreSQL

下列小節描述從 PostgreSQL 線上移轉到適用於 PostgreSQL 的 Azure 資料庫的相關已知問題和限制。

## <a name="online-migration-configuration"></a>線上移轉組態

- 源 PostgreSQL 伺服器必須運行版本 9.4、9.5、9.6、10 或 11。 如需詳細資訊，請參閱[支援的 PostgreSQL 資料庫版本](../postgresql/concepts-supported-versions.md)一文。
- 僅支援對相同版本或更高版本的遷移。 例如，支援將 PostgreSQL 9.5 遷移到用於 PostgreSQL 9.6 或 10 的 Azure 資料庫，但不支援從 PostgreSQL 11 遷移到 PostgreSQL 9.6。
- 若要在**來源 PostgreSQL postgresql.conf** 檔案中啟用邏輯複寫，請設定下列參數：
  - **wal_level** = 邏輯
  - **max_replication_slots** = [至少用於遷移的資料庫數量];如果要遷移四個資料庫，請將該值設置為至少 4。
  - **max_wal_senders** = [要並行執行之資料庫的最大數目]；建議的值為 10
- 將 DMS 代理 IP 添加到源 PostgreSQL pg_hba.conf
  1. 完成 Azure 資料庫移轉服務實例的預配後，記下 DMS IP 位址。
  2. 將該 IP 位址新增到 pg_hba.conf 檔案，如下所示：

      ```
          host  all     172.16.136.18/10    md5
          host  replication postgres    172.16.136.18/10    md5
      ```

- 使用者必須在承載源資料庫的伺服器上具有複製角色。
- 源資料庫和目標資料庫架構必須匹配。
- PostgreSQL 單一伺服器的目標 Azure 資料庫中的架構不能具有外鍵。 請使用以下查詢來卸除外部索引鍵：

    ```
                                SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ', 
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema, 
        tc.constraint_name as foreignkey, 
        tc.table_name as tableName, 
        kcu.column_name, 
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name 
    FROM 
        information_schema.table_constraints AS tc 
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    
    ```

    在查詢結果中執行卸除外部索引鍵 (這是第二個資料行)。

- PostgreSQL 單一伺服器的目標 Azure 資料庫中的架構不能有任何觸發器。 請使用下列命令來停用目標資料庫中的觸發程序：

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>資料類型限制

  **限制**：如果表上沒有主鍵，則更改可能不會同步到目標資料庫。

  **因應措施**：暫時設定資料表的主索引鍵讓移轉繼續。 您可以在資料移轉完成之後，移除主索引鍵。

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>從 AWS RDS PostgreSQL 線上遷移時的限制

當您嘗試執行從 AWS RDS PostgreSQL 到 Azure 資料庫的 PostgreSQL 線上遷移時，可能會遇到以下錯誤。

- **錯誤**： 資料庫"{資料庫}"表中表"{\table}"中的"\c]"的預設值在源伺服器和目標伺服器上不同。 來源上為 '{value on source}'，而目標上為 '{value on target}'。

  **限制**：當列架構上的預設值在源資料庫和目標資料庫之間不同時，將發生此錯誤。
  **解決方法**：確保目標上的架構與源上的架構匹配。 有關遷移架構的詳細資訊，請參閱[Azure PostgreSQL 連線遷移文檔](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)。

- **錯誤**：目標資料庫"{資料庫}"具有"表數"表，其中源資料庫"{資料庫"具有"{表數"表。 來源和目標資料庫上的資料表數目應相符。

  **限制**：當源資料庫和目標資料庫之間的表數不同時，將發生此錯誤。

  **解決方法**：確保目標上的架構與源上的架構匹配。 有關遷移架構的詳細資訊，請參閱[Azure PostgreSQL 連線遷移文檔](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)。

- **錯誤：** 源資料庫 [資料庫] 為空。

  **限制**：當源資料庫為空時，將發生此錯誤。 這很可能是因為您選擇的資料庫錯誤作為源。

  **解決方法**：仔細檢查為遷移選擇的源資料庫，然後重試。

- **錯誤：** 目標資料庫 [資料庫] 為空。 請遷移結構描述。

  **限制**：當目標資料庫上沒有架構時，將發生此錯誤。 確保目標上的架構與源上的架構匹配。
  **解決方法**：確保目標上的架構與源上的架構匹配。 有關遷移架構的詳細資訊，請參閱[Azure PostgreSQL 連線遷移文檔](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)。

## <a name="other-limitations"></a>其他限制

- 資料庫名稱不能包含分號 (;)。
- 擷取資料表必須有主索引鍵。 如果資料表沒有主索引鍵，則 DELETE 和 UPDATE 記錄作業將會產生無法預期的結果。
- 系統會忽略主索引鍵片段的更新。 在這類情況下，套用這類更新會使目標將更新識別為沒有更新任何資料列，並導致系統將記錄寫入至例外狀況資料表。
- 移轉多個具相同名稱但大小寫不同的資料表 (如 table1、TABLE1 和 Table1) 可能會造成無法預期的行為，因此沒有受到支援。
- [創建] 的更改處理更改 |掉落 |不支援 TRUNCATE_ 表 DDL。
- 在 Azure 資料庫移轉服務中，單個遷移活動最多隻能容納四個資料庫。
