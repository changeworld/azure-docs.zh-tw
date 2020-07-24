---
title: 已知問題：從于 postgresql 到適用於 PostgreSQL 的 Azure 資料庫的線上遷移
titleSuffix: Azure Database Migration Service
description: 瞭解使用 Azure 資料庫移轉服務從于 postgresql 線上遷移至適用於 PostgreSQL 的 Azure 資料庫的已知問題和遷移限制。
services: database-migration
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 564581a102ac3fab504e82db00ef54b3e45d0c19
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87090734"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql"></a>從于 postgresql 至 Azure DB for 于 postgresql 進行線上遷移的已知問題/遷移限制

下列小節描述從 PostgreSQL 線上移轉到適用於 PostgreSQL 的 Azure 資料庫的相關已知問題和限制。

## <a name="online-migration-configuration"></a>線上移轉組態

- 來源於 postgresql 伺服器必須執行9.4、9.5、9.6、10或11版。 如需詳細資訊，請參閱[支援的 PostgreSQL 資料庫版本](../postgresql/concepts-supported-versions.md)一文。
- 僅支援遷移到相同或更高版本。 例如，支援將于 postgresql 9.5 遷移至適用於 PostgreSQL 的 Azure 資料庫9.6 或10，但不支援從于 postgresql 11 遷移至於 postgresql 9.6。
- 若要在**來源 PostgreSQL postgresql.conf** 檔案中啟用邏輯複寫，請設定下列參數：
  - **wal_level** = 邏輯
  - **max_replication_slots** = [最多可供遷移的資料庫數目];如果您想要遷移四個資料庫，請將值設定為至少4個。
  - **max_wal_senders** = [要並行執行之資料庫的最大數目]；建議的值為 10
- 將 DMS 代理程式 IP 新增至來源於 postgresql pg_hba. 會議
  1. 完成布建 Azure 資料庫移轉服務的實例之後，請記下 DMS IP 位址。
  2. 將該 IP 位址新增到 pg_hba.conf 檔案，如下所示：

      ```
          host  all     172.16.136.18/10    md5
          host  replication postgres    172.16.136.18/10    md5
      ```

- 使用者在裝載源資料庫的伺服器上必須擁有複寫角色。
- 來源和目標資料庫架構必須相符。
- 目標適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中的架構不能有外鍵。 請使用以下查詢來卸除外部索引鍵：

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

- 目標適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中的架構不能有任何觸發程式。 請使用下列命令來停用目標資料庫中的觸發程序：

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>資料類型限制

  **限制**：如果資料表上沒有主鍵，變更可能不會同步處理至目標資料庫。

  **因應措施**：暫時設定資料表的主索引鍵讓移轉繼續。 您可以在資料移轉完成之後，移除主索引鍵。

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>從 AWS RDS 于 postgresql 線上遷移時的限制

當您嘗試執行從 AWS RDS 于 postgresql 到適用於 PostgreSQL 的 Azure 資料庫的線上遷移時，您可能會遇到下列錯誤。

- **錯誤**：在資料庫 '{database}' 中，資料表 '{table}' 中資料行 '{}' 的預設值在來源和目標伺服器上不一樣。 來源上為 '{value on source}'，而目標上為 '{value on target}'。

  **限制**：當來源與目標資料庫之間的資料行架構上的預設值不同時，就會發生此錯誤。
  因應**措施：確定**目標上的架構符合來源上的架構。 如需有關遷移架構的詳細資訊，請參閱[Azure 于 postgresql 線上遷移檔](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)。

- **錯誤**：目標資料庫 '{database}' 有 '{number of tables}' 個資料表，而來源資料庫 '{database}' 有 '{number of tables}' 個資料表。 來源和目標資料庫上的資料表數目應相符。

  **限制**：當來源與目標資料庫之間的資料表數目不同時，就會發生此錯誤。

  因應**措施：確定**目標上的架構符合來源上的架構。 如需有關遷移架構的詳細資訊，請參閱[Azure 于 postgresql 線上遷移檔](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)。

- **錯誤：** 源資料庫 {database} 是空的。

  **限制**：當源資料庫是空的時，就會發生此錯誤。 這很可能是因為您選取了錯誤的資料庫做為來源。

  因應**措施：再次**檢查您選取要遷移的源資料庫，然後再試一次。

- **錯誤：** 目標資料庫 {database} 是空的。 請遷移結構描述。

  **限制**：當目標資料庫上沒有架構時，就會發生此錯誤。 請確定目標上的架構符合來源上的架構。
  因應**措施：確定**目標上的架構符合來源上的架構。 如需有關遷移架構的詳細資訊，請參閱[Azure 于 postgresql 線上遷移檔](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)。

## <a name="other-limitations"></a>其他限制

- 資料庫名稱不能包含分號 (;)。
- 擷取資料表必須有主索引鍵。 如果資料表沒有主索引鍵，則 DELETE 和 UPDATE 記錄作業將會產生無法預期的結果。
- 系統會忽略主索引鍵片段的更新。 在這類情況下，套用這類更新會使目標將更新識別為沒有更新任何資料列，並導致系統將記錄寫入至例外狀況資料表。
- 移轉多個具相同名稱但大小寫不同的資料表 (如 table1、TABLE1 和 Table1) 可能會造成無法預期的行為，因此沒有受到支援。
- 變更 [建立 |] 的處理ALTER |DROP |截斷] 不支援資料表 Ddl。
- 在 Azure 資料庫移轉服務中，單一遷移活動最多隻能容納四個資料庫。
