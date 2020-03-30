---
title: 已知問題：連線遷移到 MySQL 的 Azure 資料庫
titleSuffix: Azure Database Migration Service
description: 使用 Azure 資料庫移轉服務時，瞭解連線遷移到 MySQL Azure 資料庫的已知問題和遷移限制。
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
ms.openlocfilehash: 8c3de28ea934302086a5b14e61482e6a4ab9a7ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235284"
---
# <a name="online-migration-issues--limitations-to-azure-db-for-mysql-with-azure-database-migration-service"></a>使用 Azure 資料庫移轉服務，& Azure DB 的 Azure DB 限制的線上遷移問題

從 MySQL 線上移轉到適用於 MySQL 的 Azure 資料庫相關聯已知問題和限制，如下所述。

## <a name="online-migration-configuration"></a>線上移轉組態


- 來源 MySQL 伺服器版本必須是 5.6.35、5.7.18 或更新版本
- 適用於 MySQL 的 Azure 資料庫支援：
  - MySQL 社群版
  - InnoDB 引擎
- 相同版本移轉。 不支援將 MySQL 5.6 移轉到適用於 MySQL 5.7 的 Azure 資料庫。
- 在 my.ini (Windows) 或 my.cnf (Unix) 中啟用二進位記錄
  - 將 Server_id 設為大於或等於 1 的任何數字，例如，Server_id=1 (僅適用於 MySQL 5.6)
  - 設置日誌箱 =\<路徑>（僅適用于 MySQL 5.6）
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

大型物件 (LOB) 資料行是可能會在大小上增長的資料行。 對於 MySQL、中文本、長文本、Blob、中Blob、Longblob 等，是 LOB 的一些資料類型。

- **限制**：如果 LOB 資料類型作為主索引鍵，移轉將會失敗。

    **因應措施**：使用不是 LOB 的其他資料類型或資料行，取代主索引鍵。

- **限制**：如果大型物件 (LOB) 資料行的長度大於 32 KB，目標的資料可能會遭到截斷。 您可以使用以下查詢，檢查 LOB 資料行的長度：
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **解決方法**：如果 LOB 物件大於 32 KB，請與["詢問 Azure 資料庫移轉"](mailto:AskAzureDatabaseMigrations@service.microsoft.com)中的工程團隊聯繫。

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>從 AWS RDS MySQL 線上遷移時的限制

當您嘗試執行從 AWS RDS MySQL 到 MySQL Azure 資料庫的線上遷移時，可能會遇到以下錯誤。

- **錯誤：** 資料庫{0}' 具有目標上的外鍵。 修正目標，然後啟動新的資料移轉活動。 在目標上執行以下腳本以列出外鍵

  **限制**：如果您的架構中具有外鍵，遷移的初始載入和連續同步將失敗。
  **解決方法**：在 MySQL 工作臺中執行以下腳本以提取刪除外鍵腳本並添加外鍵腳本：

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **錯誤：** 伺服器上不存在{0}資料庫 ' ' 所提供的 MySQL 來源伺服器會區分大小寫。 請檢查資料庫名稱。

  **限制**：使用命令列介面 （CLI） 將 MySQL 資料庫遷移到 Azure 時，使用者可能會遭遇此錯誤。 服務無法在源伺服器上找到資料庫，這可能是因為您可能提供了不正確的資料庫名稱，或者列出的伺服器上不存在資料庫。 請注意，資料庫名稱區分大小寫。

  **解決方法**：提供確切的資料庫名稱，然後重試。

- **錯誤：** 資料庫中有同名的表"[資料庫]"。 適用於 MySQL 的 Azure 資料庫不支援區分大小寫的資料表。

  **限制**：當源資料庫中有兩個同名表時，將發生此錯誤。 MySQL 的 Azure 資料庫不支援區分大小寫的表。

  **解決方法**：將表名稱更新為唯一名稱，然後重試。

- **錯誤：** 目標資料庫 [資料庫] 為空。 請遷移結構描述。

  **限制**：當 MySQL 資料庫的目標 Azure 資料庫沒有所需的架構時，將發生此錯誤。 架構遷移是啟用將資料移轉到目標所必需的。

  **解決方法**：[將架構](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#migrate-the-sample-schema)從源資料庫移轉到目標資料庫。

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

- 在 Azure 資料庫移轉服務中，在單個遷移活動中遷移的資料庫限制為 4。

- **錯誤：** 行大小過大（> 8126）。 將某些列更改為 TEXT 或 BLOB 可能會有所説明。 在當前行格式中，BLOB 首碼為 0 位元組是內聯存儲的。

  **限制**：當您使用 InnoDB 儲存引擎遷移到 MySQL 的 Azure 資料庫時，會發生此錯誤，並且任何表行大小都太大（>8126 位元組）。

  **解決方法**：更新行大小大於 8126 位元組的表的架構。 我們不建議更改嚴格模式，因為資料將被截斷。 不支援更改page_size。
