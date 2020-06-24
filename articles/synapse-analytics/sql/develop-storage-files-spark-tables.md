---
title: 在 SQL 隨選 (預覽) 中同步處理 Apache Spark for Azure Synapse 外部資料表定義
description: 概述如何使用 SQL 隨選 (預覽) 查詢 Spark 資料表
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: e36f98a20d1fc6392aef1aebf5fc86e18085cc10
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85204893"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-sql-on-demand-preview"></a>在 SQL 隨選 (預覽) 中同步處理 Apache Spark for Azure Synapse 外部資料表定義

SQL 隨選 (預覽) 可以自動同步處理來自 Apache Spark for Azure Synapse 集區的中繼資料。 系統會針對 Spark 集區 (預覽) 中的每個資料庫建立 SQL 隨選資料庫。 

針對以 Parquet 為基礎且位於 Azure 儲存體中的每個 Spark 外部資料表，系統會在 SQL 隨選資料庫中建立一個外部資料表。 因此，您可以在關閉 Spark 集區後，繼續從 SQL 隨選查詢 Spark 外部資料表。

在 Spark 中分割資料表時，儲存體中的檔案會依資料夾組織。 SQL 隨選會使用分割區中繼資料，而且只會以相關的資料夾和檔案作為查詢目標。

系統已針對 Azure Synapse 工作區中佈建的每個 Spark 集區自動設定中繼資料同步。 您可以立即開始查詢 Spark 外部資料表。

位於 Azure 儲存體中的每個 Spark Parquet 外部資料表都會以 dbo 結構描述中對應至 SQL 隨選資料庫的外部資料表來表示。 

針對 Spark 外部資料表查詢，請執行以外部 [spark_table] 為目標的查詢。 在執行下列範例之前，請確定您可以正確地[存取檔案所在的儲存體帳戶](develop-storage-files-storage-access-control.md)。

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
