---
title: 已知問題：線上遷移至適用於 MySQL 的 Azure 資料庫
titleSuffix: Azure Database Migration Service
description: 瞭解使用 Azure 資料庫移轉服務時，線上遷移至適用於 MySQL 的 Azure 資料庫的已知問題和遷移限制。
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
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 3b9a94f7f9f64426374a5ea349b3653d837fc1ac
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494442"
---
# <a name="online-migration-issues--limitations-to-azure-db-for-mysql-with-azure-database-migration-service"></a>使用 Azure 資料庫移轉服務，對適用于 MySQL 的 Azure DB 進行線上遷移問題 & 限制

從 MySQL 線上移轉到適用於 MySQL 的 Azure 資料庫相關聯已知問題和限制，如下所述。

## <a name="online-migration-configuration"></a>線上移轉組態


- 來源 MySQL 伺服器版本必須是 5.6.35、5.7.18 或更新版本
- 適用於 MySQL 的 Azure 資料庫支援：
  - MySQL 社群版
  - InnoDB 引擎
- 相同版本移轉。 不支援將 MySQL 5.6 移轉到適用於 MySQL 5.7 的 Azure 資料庫。 不支援從 MySQL 8.0 進行遷移。
- 在 my.ini (Windows) 或 my.cnf (Unix) 中啟用二進位記錄
  - 將 Server_id 設為大於或等於 1 的任何數字，例如，Server_id=1 (僅適用於 MySQL 5.6)
  - 設定 log-bin = \<path> (僅適用於 MySQL 5.6)
  - 設定 binlog_format = row
  - Expire_logs_days = 5 (建議 - 僅適用於 MySQL 5.6)
- 使用者必須擁有 ReplicationAdmin 角色。
- 針對來源 MySQL 資料庫所定義的定序，與在目標適用於 MySQL 的 Azure 資料庫所定義的定序相同。
- 來源 MySQL 資料庫與適用於 MySQL 的 Azure 資料庫中目標資料庫，兩者之間的結構描述必須相符。
- 適用於 MySQL 的 Azure 資料庫中結構描述不能具有外部索引鍵。 請使用以下查詢來卸除外部索引鍵：
    ```
    SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT 
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
          KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
          AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ['schema_name']) Queries
      GROUP BY SchemaName;
    ```

    在查詢結果中執行卸除外部索引鍵 (這是第二個資料行)。
- 適用於 MySQL 的 Azure 資料庫中結構描述不能具有任何觸發程序。 若要在目標資料庫中卸除觸發程序：
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>資料類型限制

- **限制**：如果在來源 MySQL 資料庫中有 JSON 資料類型，持續同步期間移轉會失敗。

    **因應措施**：在來源 MySQL 資料庫中將 JSON 資料類型修改為中長文字或長文字。

- **限制**：如果資料表中沒有主索引鍵，持續同步會失敗。

    **因應措施**：暫時設定資料表的主索引鍵讓移轉繼續。 您可以在資料移轉完成之後，移除主索引鍵。

## <a name="lob-limitations"></a>LOB 限制

大型物件 (LOB) 資料行是可能會在大小上增長的資料行。 針對 MySQL、Medium text、Longtext、Blob、Mediumblob、Longblob 等，則是 LOB 的一些資料類型。

- **限制**：如果 LOB 資料類型作為主索引鍵，移轉將會失敗。

    **因應措施**：使用不是 LOB 的其他資料類型或資料行，取代主索引鍵。

- **限制**：如果大型物件 (LOB) 資料行的長度大於「限制 LOB 大小」參數 (不應大於 64 KB) ，則可能會在目標上截斷資料。 您可以使用以下查詢，檢查 LOB 資料行的長度：
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    因應**措施：如果**您的 LOB 物件大於 64 KB，請使用「允許無限制的 lob 大小」參數。 請注意，使用「允許無限制的 LOB 大小」參數的遷移將會比使用「限制 LOB 大小」參數進行遷移慢。

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>從 AWS RDS MySQL 線上遷移時的限制

當您嘗試執行從 AWS RDS MySQL 至適用於 MySQL 的 Azure 資料庫的線上遷移時，可能會遇到下列錯誤。

- **錯誤：** 資料庫 ' {0} ' 在目標上有外鍵 (s) 。 修正目標，然後啟動新的資料移轉活動。 在目標上執行下列腳本，以列出外鍵 (s) 

  **限制**：如果您的架構中有外鍵，則遷移的初始載入和持續同步將會失敗。
  因應**措施：在**MySQL 工作臺中執行下列腳本，以將 drop foreign key 腳本和 add 外鍵腳本解壓縮：

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **錯誤：** 資料庫 ' {0} ' 不存在於伺服器上。 所提供的 MySQL 來源伺服器會區分大小寫。 請檢查資料庫名稱。

  **限制**：使用命令列介面 (CLI) 將 MySQL 資料庫遷移至 Azure 時，使用者可能會遇到此錯誤。 服務在來源伺服器上找不到資料庫，這可能是因為您可能提供不正確的資料庫名稱，或資料庫不存在於列出的伺服器上。 請注意，資料庫名稱會區分大小寫。

  因應**措施：請**提供正確的資料庫名稱，然後再試一次。

- **錯誤：** 資料庫 ' {database} ' 中有相同名稱的資料表。 適用於 MySQL 的 Azure 資料庫不支援區分大小寫的資料表。

  **限制**：當您在源資料庫中有兩個具有相同名稱的資料表時，就會發生此錯誤。 適用於 MySQL 的 Azure 資料庫不支援區分大小寫的資料表。

  因應**措施：請**將資料表名稱更新為唯一的，然後再試一次。

- **錯誤：** 目標資料庫 {database} 是空的。 請遷移結構描述。

  **限制**：如果目標適用於 MySQL 的 Azure 資料庫資料庫沒有必要的架構，就會發生此錯誤。 需要進行架構遷移，才能將資料移轉到您的目標。

  因應**措施：將**架構從源資料庫[遷移](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#migrate-the-sample-schema)至目標資料庫。

## <a name="other-limitations"></a>其他限制

- 不支援在開頭或結尾具有左右大括弧 {  } 的密碼字串。 限制同時適用於連線到來源 MySQL，以及與目標適用於 MySQL 的 Azure 資料庫。
- 不支援下列 DDL：
  - 所有資料分割 DDL
  - 卸除資料表
  - 重新命名資料表
- 不支援使用 alter table <table_name> add column <column_name>** 陳述式，將資料行新增至資料表開頭或中間。 alter table <table_name> add column <column_name>** 會在資料表結尾處新增資料行。
- 不支援只在資料行資料的一部分建立索引。 下列陳述式是僅使用資料行資料的一部分，建立索引的範例：

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- 在 Azure 資料庫移轉服務中，單一遷移活動中要遷移的資料庫限制為四個。

- Azure DMS 不支援 CASCADE 參考動作，這有助於在父資料表中刪除或更新資料列時，自動刪除或更新子資料表中相符的資料列。 如需詳細資訊，請參閱 MySQL 文件中[外部索引鍵條件約束](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html)一文的「參考動作」一節。 Azure DMS 會要求您在初始資料載入期間，將外部索引鍵條件約束放在目標資料庫伺服器中，而且您不能使用參考動作。 如果您的工作負載相依於透過此參考動作來更新相關的子資料表，我們建議您改為執行[傾印並還原](https://docs.microsoft.com/azure/mysql/concepts-migrate-dump-restore)。 

- **錯誤：** 資料列大小太大 ( # A0 8126) 。 將某些資料行變更為文字或 BLOB 可能會有所説明。 在目前的資料列格式中，BLOB 前置詞0位元組是以內嵌方式儲存。

  **限制**：當您使用 InnoDB 儲存引擎遷移至適用於 MySQL 的 Azure 資料庫，且任何資料表資料列大小太大 ( # B0 8126 位元組) 時，就會發生此錯誤。

  因應**措施：更新**資料列大小超過8126位元組之資料表的架構。 我們不建議您變更 strict 模式，因為資料將會被截斷。 不支援變更 page_size。
