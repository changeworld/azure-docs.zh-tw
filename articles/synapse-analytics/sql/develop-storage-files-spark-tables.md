---
title: 使用 SQL 隨選查詢 Spark 資料表 (預覽)
description: 概述如何使用 SQL 隨選 (預覽) 查詢 Spark 資料表
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 41e31a322a3d771557474fdf5c318960822bcfe1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420072"
---
# <a name="query-spark-tables-with-azure-synapse-analytics-using-sql-on-demand-preview"></a>透過 Azure Synapse Analytics 使用 SQL 隨選 (預覽) 查詢 Spark 資料表

SQL 隨選 (預覽) 可自動同步 Synapse 工作區 (預覽) 中 Spark 集區的中繼資料。 系統會針對 Spark 集區 (預覽) 中的每個資料庫建立 SQL 隨選資料庫。 系統會針對每個 Parquet 或 CSV 形式的 Spark 資料表，在 SQL 隨選資料庫中建立外部資料表。 因此，您可以在關閉 Spark 集區後，繼續從 SQL 隨選查詢 Spark 資料表。

在 Spark 中分割資料表時，儲存體中的檔案會依資料夾組織。 SQL 隨選會使用分割區中繼資料，而且只會以相關的資料夾和檔案作為查詢目標。

系統已針對 Azure Synapse 工作區中佈建的每個 Spark 集區自動設定中繼資料同步。 您可以立即開始查詢 Spark 資料表。

每個 Spark 資料表都會以對應至 SQL 隨選資料庫的外部資料表 (dbo 結構描述) 來表示。 針對 Spark 資料表查詢，請執行以外部 [spark_table] 為目標的查詢。 在執行下列範例之前，請確定您可以正確地[存取檔案所在的儲存體帳戶](develop-storage-files-storage-access-control.md)。

```sql
SELECT * FROM [db].dbo.[spark_table]
```

## <a name="spark-data-types-to-sql-data-types-mapping"></a>Spark 資料類型與 SQL 資料類型的對應

| Spark 資料類型 | SQL 資料類型               |
| --------------- | --------------------------- |
| ByteType        | SMALLINT                    |
| ShortType       | SMALLINT                    |
| IntegerType     | int                         |
| LongType        | BIGINT                      |
| FloatType       | real                        |
| DoubleType      | FLOAT                       |
| DecimalType     | decimal                     |
| TimestampType   | datetime2                   |
| DateType        | date                        |
| StringType      | varchar(max)*               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| ArrayType       | varchar(max)* (into JSON)** |
| MapType         | varchar(max)* (into JSON)** |
| StructType      | varchar(max)* (into JSON)** |

\* 使用的定序為 Latin1_General_100_BIN2_UTF8。

** ArrayType、MapType 和 StructType 會以 JSON 表示。



## <a name="next-steps"></a>後續步驟

若要深入了解儲存體的存取控制，請前往[儲存體存取控制](develop-storage-files-storage-access-control.md)一文。
