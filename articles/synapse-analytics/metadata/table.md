---
title: Azure Synapse Analytics 的共用中繼資料資料表
description: Azure Synapse Analytics 會提供共用中繼資料模型，您在 Apache Spark 中建立的資料表，將能夠透過其 SQL 隨選 (預覽) 和 SQL 集區引擎存取，而不需要複製資料。
services: sql-data-warehouse
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: 3e28a76a559603755d3d72e8d5e27cde72aa9533
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701072"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Azure Synapse Analytics 共用中繼資料資料表

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Azure Synapse Analytics 可讓不同的工作區計算引擎在其 Apache Spark 集區 (預覽) 和 SQL 隨選 (預覽) 引擎之間共用資料庫和 Parquet 支援的資料表。

Spark 作業建立資料庫之後，您就可以透過 Spark，在其中建立使用 Parquet 儲存格式的資料表。 這些資料表隨即可供任何 Azure Synapse 工作區 Spark 集區查詢。 若具有適當權限，您也可以從任何 Spark 作業中使用這些資料表。

Spark 所建立和管理的資料表以及外部資料表也會在 SQL 隨選的對應同步資料庫中，以同名的外部資料表形式來提供使用。 [在 SQL 中公開 Spark 資料表](#exposing-a-spark-table-in-sql)會提供資料表同步的更多詳細資料。

由於資料表不會即時同步至 SQL，因此資料表會延遲顯示。

## <a name="manage-a-spark-created-table"></a>管理 Spark 建立的資料表

使用 Spark 來管理 Spark 建立的資料庫。 例如，透過 Spark 集區作業來刪除資料庫，以及從 Spark 建立其中的資料表。

如果您透過 SQL 隨選在這類資料庫中建立物件，或嘗試卸載資料庫，雖然此作業將會成功，但不會變更原始的 Spark 資料庫。

## <a name="exposing-a-spark-table-in-sql"></a>在 SQL 中公開 Spark 資料表

### <a name="which-spark-tables-are-shared"></a>共用的 Spark 資料表

Spark 提供兩種可讓 Azure Synapse 自動在 SQL 中公開的資料類型：

- 受控資料表

  Spark 提供許多將資料儲存在受控資料表中的選項，例如 TEXT、CSV、JSON、JDBC、PARQUET、ORC、HIVE、DELTA 和 LIBSVM。 這些檔案通常會儲存在 `warehouse` 目錄，該目錄也是儲存受控資料表資料的地方。

- 外部資料表

  Spark 也可讓您藉由提供 `LOCATION` 選項或使用 Hive 格式，以現有資料建立外部資料表。 這類外部資料表可以使用各種不同的資料格式，包括 Parquet。

Azure Synapse 目前只會共用受控及外部 Spark 資料表，這些資料表會使用 SQL 引擎將其資料儲存為 Parquet 格式。 以其他格式支援的資料表不會自動進行同步。 如果 SQL 引擎支援資料表的基礎格式，您可以自行將這類資料表明確地同步為您自己 SQL 資料庫中的外部資料表。

### <a name="how-are-spark-tables-shared"></a>如何共用 Spark 資料表

在 SQL 引擎中以外部資料表形式公開的可共用受控資料表和外部 Spark 資料表，具有下列屬性：

- SQL 外部資料表的資料來源是代表 Spark 資料表所在資料夾的資料來源。
- SQL 外部資料表的檔案格式為 Parquet。
- SQL 外部資料表的存取認證是傳遞式認證。

由於所有 Spark 資料表名稱都是有效的 SQL 資料表名稱，而且所有 Spark 資料行名稱都是有效的 SQL 資料行名稱，因此 SQL 外部資料表會使用 Spark 資料表和資料行的名稱。

Spark 資料表提供的資料類型與 Synapse SQL 引擎不同。 下表是 Spark 資料表的資料類型與 SQL 類型的對應：

| Spark 資料類型 | SQL 資料類型 | 註解 |
|---|---|---|
| `byte`      | `smallint`       ||
| `short`     | `smallint`       ||
| `integer`   |    `int`            ||
| `long`      |    `bigint`         ||
| `float`     | `real`           |<!-- need precision and scale-->|
| `double`    | `float`          |<!-- need precision and scale-->|
| `decimal`      | `decimal`        |<!-- need precision and scale-->|
| `timestamp` |    `datetime2`      |<!-- need precision and scale-->|
| `date`      | `date`           ||
| `string`    |    `varchar(max)`   | 使用 `Latin1_General_CP1_CI_AS_UTF8` 定序 |
| `binary`    |    `varbinary(max)` ||
| `boolean`   |    `bit`            ||
| `array`     |    `varchar(max)`   | 使用 `Latin1_General_CP1_CI_AS_UTF8` 定序序列化為 JSON |
| `map`       |    `varchar(max)`   | 使用 `Latin1_General_CP1_CI_AS_UTF8` 定序序列化為 JSON |
| `struct`    |    `varchar(max)`   | 使用 `Latin1_General_CP1_CI_AS_UTF8` 定序序列化為 JSON |

<!-- TODO: Add precision and scale to the types mentioned above -->

## <a name="security-model"></a>安全性模型

Spark 資料庫和資料表，以及其在 SQL 引擎中的同步代表項目，將會在基礎儲存層級受到保護。 因為其本身目前沒有物件的權限，所以物件會在物件總管中顯示。

建立受控資料表的安全性主體會被視為該資料表的擁有者，並具有該資料表的所有權限，包括基礎資料夾和檔案。 此外，資料庫的擁有者會自動成為資料表的共同擁有者。

如果您使用驗證傳遞來建立 Spark 或 SQL 外部資料表，資料只會在資料夾和檔案層級上受到保護。 如果有人查詢此類型的外部資料表，查詢提交者的安全性識別會向下傳遞至檔案系統，以檢查存取權限。

如需有關如何設定資料夾和檔案權限的詳細資訊，請參閱 [共用資料庫](database.md)。

## <a name="examples"></a>範例

### <a name="create-a-managed-table-backed-by-parquet-in-spark-and-query-from-sql-on-demand"></a>在 Spark 中建立由 Parquet 支援的受控資料表，並從 SQL 隨選查詢

在此案例中，您有一個名為 `mytestdb` 的 Spark 資料庫。 請參閱[建立及連線到 Spark 資料庫 - SQL 隨選](database.md#create--connect-to-spark-database---sql-on-demand)。

藉由執行下列命令，您可以使用 SparkSQL 建立受控 Spark 資料表：

```sql
    CREATE TABLE mytestdb.myParquetTable(id int, name string, birthdate date) USING Parquet
```

這會在 `mytestdb` 資料庫中建立 `myParquetTable` 資料表。 短暫延遲之後，您就可以在 SQL 隨選中看到資料表。 例如，從 SQL 隨選執行下列陳述式。

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

確認結果中包含 `myParquetTable`。

>[!NOTE]
>未使用 Parquet 作為其儲存格式的資料表將不會進行同步。

接下來，將一些值從 Spark 插入資料表中，例如，在 C# 筆記本中使用下列 C# Spark 陳述式：

```csharp
using Microsoft.Spark.Sql.Types;

var data = new List<GenericRow>();

data.Add(new GenericRow(new object[] { 1, "Alice", new Date(2010, 1, 1)}));
data.Add(new GenericRow(new object[] { 2, "Bob", new Date(1990, 1, 1)}));

var schema = new StructType
    (new List<StructField>()
        {
            new StructField("id", new IntegerType()),
            new StructField("name", new StringType()),
            new StructField("birthdate", new DateType())
        }
    );

var df = spark.CreateDataFrame(data, schema);
df.Write().Mode(SaveMode.Append).InsertInto("mytestdb.myParquetTable");
```

現在您可以從 SQL 隨選中讀取資料，如下所示：

```sql
SELECT * FROM mytestdb.dbo.myParquetTable WHERE name = 'Alice';
```

您應該會取得具有下列資料列的結果：

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="creating-an-external-table-backed-by-parquet-in-spark-and-querying-it-from-sql-on-demand"></a>在 Spark 中建立由 Parquet 支援的外部資料表，並從 SQL 隨選進行查詢

在此範例中，請以先前受控資料表範例中建立的 Parquet 資料檔案建立外部 Spark 資料表。

例如，使用 SparkSQL 執行：

```sql
CREATE TABLE mytestdb.myExternalParquetTable
    USING Parquet
    LOCATION "abfss://<fs>@arcadialake.dfs.core.windows.net/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

將 `<fs>` 預留位置取代為工作區預設檔案系統的檔案系統名稱，並以您用來執行此範例的 synapse 工作區名稱取代 `<synapse_ws>` 預留位置。

前一個範例會在 `mytestdb` 資料庫中建立 `myExtneralParquetTable` 資料表。 短暫延遲之後，您就可以在 SQL 隨選中看到資料表。 例如，從 SQL 隨選執行下列陳述式。

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

確認結果中包含 `myExternalParquetTable`。

現在您可以從 SQL 隨選中讀取資料，如下所示：

```sql
SELECT * FROM mytestdb.dbo.myExternalParquetTable WHERE name = 'Alice';
```

您應該會取得具有下列資料列的結果：

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

## <a name="next-steps"></a>後續步驟

- [深入了解 Azure Synapse Analytics 的共用中繼資料](overview.md)
- [深入了解 Azure Synapse Analytics 的共用中繼資料資料表](table.md)


