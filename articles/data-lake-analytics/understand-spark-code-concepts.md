---
title: 瞭解 Azure 資料湖分析 U-SQL 開發人員的 Apache Spark 代碼概念。
description: 本文介紹了 Apache Spark 概念，以説明 U-SQL 開發人員瞭解 Spark 代碼概念。
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: Understand-apache-spark-code-concepts
ms.date: 10/15/2019
ms.openlocfilehash: bdb38e36a9f1344a3adde15d349a2ec176c0fe95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74424014"
---
# <a name="understand-apache-spark-code-for-u-sql-developers"></a>瞭解 U-SQL 開發人員的 Apache Spark 代碼

本節提供有關將 U-SQL 腳本轉換為 Apache Spark 的高級指導。

- 它從[比較兩種語言的處理范式](#understand-the-u-sql-and-spark-language-and-processing-paradigms)開始
- 提供有關如何操作的提示：
   - [轉換腳本](#transform-u-sql-scripts)，包括 U-SQL 的[行集運算式](#transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions)
   - [.NET 程式碼](#transform-net-code)
   - [資料類型](#transform-typed-values)
   - [目錄物件](#transform-u-sql-catalog-objects)。

## <a name="understand-the-u-sql-and-spark-language-and-processing-paradigms"></a>瞭解 U-SQL 和 Spark 語言和處理範例

在開始將 Azure 資料湖分析的 U-SQL 腳本遷移到 Spark 之前，瞭解兩個系統的一般語言和處理理念非常有用。

U-SQL 是一種類似于 SQL 的聲明性查詢語言，它使用資料流程範例，並允許您輕鬆嵌入和擴展以 .NET（例如 C#）、Python 和 R 編寫的使用者代碼。使用者擴展可以實現簡單的運算式或使用者定義的函數，但還可以為使用者提供實現所謂的使用者定義的運算子的能力，這些運算子實現自訂運算子以執行行集級別轉換、提取和寫入輸出。

Spark 是一個橫向擴展框架，在 Scala、JAVA、Python、.NET 等中提供多種語言綁定，在這些綁定中，您主要用這些語言之一編寫代碼，創建稱為彈性分散式資料集 （RDD）、資料幀和資料集的資料抽象以及然後使用類似 LINQ 的域特定語言 （DSL） 來轉換它們。 它還提供 SparkSQL 作為資料幀和資料集抽象的聲明性子語言。 DSL 提供兩類操作，即轉換和操作。 將轉換應用於資料抽象不會執行轉換，而是構建將提交用於操作進行評估的執行計畫（例如，將結果寫入臨時表或檔，或列印結果）。

因此，將 U-SQL 腳本轉換為 Spark 程式時，您必須決定要使用哪種語言至少生成資料幀抽象（這是當前最常用的資料抽象），以及是否要編寫聲明性使用 DSL 或 SparkSQL 的資料流程轉換。 在某些更複雜的情況下，您可能需要將 U-SQL 腳本拆分為 Spark 序列以及使用 Azure 批次處理或 Azure 函數實現的其他步驟。

此外，Azure 資料湖分析在無伺服器作業服務環境中提供 U-SQL，而 Azure 資料塊和 Azure HDInsight 都以叢集服務的形式提供 Spark。 在轉換應用程式時，必須考慮現在創建、調整大小、縮放和停用群集的影響。

## <a name="transform-u-sql-scripts"></a>轉換 U-SQL 腳本

U-SQL 腳本遵循以下處理模式：

1. 資料從非結構化檔、使用`EXTRACT`語句、位置或檔集規範、內置或使用者定義的提取器和所需架構或 U-SQL 表（託管表或外部表）讀取。 它表示為行集。
2. 行集在多個 U-SQL 語句中轉換，這些語句將 U-SQL 運算式應用於行集並生成新的行集。
3. 最後，生成的行集使用指定位置和內置輸出器或`OUTPUT`使用者定義的輸出器的語句輸出到任一檔中，或者輸出到 U-SQL 表中。

腳本被懶惰地計算，這意味著每個提取和轉換步驟都組成一個運算式樹並全域計算（資料流程）。

Spark 程式類似，因為您將使用 Spark 連接器讀取資料並創建資料幀，然後使用 LINQ 樣的 DSL 或 SparkSQL 在資料幀上應用轉換，然後將結果寫入檔、臨時 Spark 表，某些程式設計語言類型，或主控台。

## <a name="transform-net-code"></a>轉換 .NET 代碼

U-SQL 的運算式語言是 C#，它提供了多種方法來擴展自訂 .NET 代碼。

由於 Spark 目前不支援本機執行 .NET 代碼，因此您必須將運算式重寫為等效的 Spark、Scala、JAVA 或 Python 運算式，或者找到調用 .NET 代碼的方法。 如果腳本使用 .NET 庫，則具有以下選項：

- 將 .NET 代碼轉換為 Scala 或 Python。
- 將 U-SQL 腳本拆分為幾個步驟，其中使用 Azure 批次處理過程應用 .NET 轉換（如果可以獲得可接受的比例）
- 使用開放源碼中提供的名為 Moebius 的 .NET 語言綁定。 此專案未處於受支援狀態。

在任何情況下，如果您的 U-SQL 腳本中具有大量的 .NET 邏輯，請通過 Microsoft 帳戶代表與我們聯繫，以進行進一步的指導。

以下詳細資訊適用于 U-SQL 腳本中 .NET 和 C# 用法的不同情況。

### <a name="transform-scalar-inline-u-sql-c-expressions"></a>轉換標量內聯 U-SQL C++ 運算式

U-SQL 的運算式語言是 C#。 許多標量內聯 U-SQL 運算式是本機實現的，以提高性能，而更複雜的運算式可以通過調用 .NET 框架來執行。

Spark 有自己的標量運算式語言（作為 DSL 的一部分或 SparkSQL 中的一部分），並允許調用以託管語言編寫的使用者定義的函數。

如果在 U-SQL 中具有標量運算式，則應首先找到最適當的本機理解的 Spark 標量運算式以獲得最佳性能，然後將其他運算式映射到您選擇的 Spark 託管語言的使用者定義的函數中。

請注意，.NET 和 C# 的類型語義與 Spark 託管語言和 Spark 的 DSL 不同。 有關類型系統差異的更多詳細資訊，請參閱[下文](#transform-typed-values)。

### <a name="transform-user-defined-scalar-net-functions-and-user-defined-aggregators"></a>轉換使用者定義的標量 .NET 函數和使用者定義的聚合器

U-SQL 提供了調用任意標量 .NET 函數和調用以 .NET 編寫的使用者定義的聚合器的方法。

Spark 還支援使用者定義的函數和使用者定義的聚合器，這些聚合器使用大多數託管語言編寫，這些語言可以從 Spark 的 DSL 和 SparkSQL 調用。

### <a name="transform-user-defined-operators-udos"></a>轉換使用者定義的運算子 （UDO）

U-SQL 提供了幾類使用者定義的運算子 （UDO），如提取器、輸出器、縮減器、處理器、應用程式器和組合器，這些運算子可以在 .NET（在某種程度上- 在 Python 和 R 中）編寫。

Spark 不為操作員提供相同的擴展模型，但對某些運算子具有等效功能。

與提取器和輸出器等效的火花是火花連接器。 對於許多 U-SQL 提取器，您可能會在 Spark 社區中找到等效連接器。 對於其他人，您必須編寫自訂連接器。 如果 U-SQL 提取器很複雜，並且使用多個 .NET 庫，則最好在 Scala 中構建一個連接器，該連接器使用 Interop 調用執行資料實際處理的 .NET 庫。 在這種情況下，您必須將 .NET Core 運行時部署到 Spark 群集，並確保引用的 .NET 庫符合 .NET 標準 2.0 標準。

其他類型的 U-SQL UDO 需要使用使用者定義的函數和聚合器以及語義上適當的 Spark DLS 或 SparkSQL 運算式重寫。 例如，處理器可以映射到各種 UDF 調用的 SELECT，打包為一個函數，該函數將資料框作為參數並返回資料框。

### <a name="transform-u-sqls-optional-libraries"></a>轉換 U-SQL 的可選庫

U-SQL 提供了一組可選和演示庫，提供[Python](data-lake-analytics-u-sql-python-extensions.md)Python、R、JSON、XML、AVRO[支援](https://github.com/Azure/usql/tree/master/Examples/DataFormats)和一些[認知服務功能](data-lake-analytics-u-sql-cognitive.md)。 [R](data-lake-analytics-u-sql-r-extensions.md)

Spark 分別提供自己的 Python 和 R 集成、pySpark 和 SparkR，並提供用於讀取和寫入 JSON、XML 和 AVRO 的連接器。

如果您需要轉換引用認知服務庫的腳本，我們建議您通過 Microsoft 帳戶代表與我們聯繫。

## <a name="transform-typed-values"></a>變換類型化值

由於 U-SQL 的類型系統基於 .NET 類型系統，Spark 有自己的類型系統，受主機語言綁定的影響，因此您必須確保操作的類型接近某些類型，因此類型範圍、精度和/或比例可能略有不同。 此外，U-SQL 和`null`Spark 對待值的方式也不同。

### <a name="data-types"></a>資料類型

下表為給定的 USQL 類型提供了 Spark、Scala 和 PySpark 中的等效類型。

| U-SQL | Spark |  Scala | PySpark |
| ------ | ------ | ------ | ------ |
|`byte`       ||||
|`sbyte`      |`ByteType` |`Byte` | `ByteType`|
|`int`        |`IntegerType` |`Int` | `IntegerType`|
|`uint`       ||||
|`long`       |`LongType` |`Long` | `LongType`|
|`ulong`      ||||
|`float`      |`FloatType` |`Float` | `FloatType`|
|`double`     |`DoubleType` |`Double` | `DoubleType`|
|`decimal`    |`DecimalType` |`java.math.BigDecimal` | `DecimalType`|
|`short`      |`ShortType` |`Short` | `ShortType`|
|`ushort`     ||||
|`char`   | |`Char`||
|`string` |`StringType` |`String` |`StringType` |
|`DateTime`   |`DateType`, `TimestampType` |`java.sql.Date`, `java.sql.Timestamp` | `DateType`, `TimestampType`|
|`bool`   |`BooleanType` |`Boolean` | `BooleanType`|
|`Guid`   ||||
|`byte[]` |`BinaryType` |`Array[Byte]` | `BinaryType`|
|`SQL.MAP<K,V>`   |`MapType(keyType, valueType, valueContainsNull)` |`scala.collection.Map` | `MapType(keyType, valueType, valueContainsNull=True)`|
|`SQL.ARRAY<T>`   |`ArrayType(elementType, containsNull)` |`scala.collection.Seq` | `ArrayType(elementType, containsNull=True)`|

如需詳細資訊，請參閱

- [org.apache.spark.sql.type](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.types.package)
- [火花 SQL 和資料框架類型](https://spark.apache.org/docs/latest/sql-reference.html#data-types)
- [Scala 數值型別](https://www.scala-lang.org/api/current/scala/AnyVal.html)
- [pyspark.sql.type](https://spark.apache.org/docs/latest/api/python/pyspark.sql.html#module-pyspark.sql.types)

### <a name="treatment-of-null"></a>Null 的處理

在 Spark 中，每個預設值的類型允許 Null 值，而在 U-SQL 中，顯式將標量、非物件標記為空。 雖然 Spark 允許您將列定義為不可取消，但它不會強制執行約束，[並可能導致錯誤的結果](https://medium.com/@weshoffman/apache-spark-parquet-and-troublesome-nulls-28712b06f836)。

在 Spark 中，Null 表示該值未知。 Spark Null 值不同于任何值，包括其自身。 兩個 Spark Null 值之間的比較，或 Null 值和任何其他值之間的比較，返回未知，因為每個 Null 的值未知。  

此行為不同于 U-SQL，U-SQL 遵循 C#`null`語義，其中與任何值不同，但等於自身。  

因此，使用的`SELECT``WHERE column_name = NULL`SparkSQL 語句返回零行，即使 中`column_name`存在 Null 值也是如此，而在 U-SQL`column_name`中，它將`null`返回設置為 的行。 同樣，使用`SELECT``WHERE column_name != NULL`Spark 語句返回零行，即使 中`column_name`存在非空值也是如此，而在 U-SQL 中，它將返回具有非空的行。 因此，如果想要 U-SQL 空檢查語義，則應分別使用[isnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnull)和[isnotnull（](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnotnull)或其 DSL 等效項）。

## <a name="transform-u-sql-catalog-objects"></a>轉換 U-SQL 目錄物件

一個主要區別是 U-SQL 腳本可以使用其目錄物件，其中許多物件沒有直接的 Spark 等效項。

Spark 確實支援 Hive Meta 存儲概念（主要是資料庫和表），因此您可以將 U-SQL 資料庫和架構映射到 Hive 資料庫，將 U-SQL 表映射到 Spark 表（請參閱[移動存儲在 U-SQL 表中的資料](understand-spark-data-formats.md#move-data-stored-in-u-sql-tables)），但它不支援視圖、表值函數 （TVF）、預存程序、U-SQL 程式集、外部資料源等。

U-SQL 代碼物件（如視圖、TVF、預存程序和程式集）可以通過 Spark 中的代碼函數和庫進行建模，並使用主機語言的功能和程式抽象機器制（例如，通過導入）進行引用Python 模組或引用 Scala 函數）。

如果 U-SQL 目錄已用於跨專案和團隊共用資料和代碼物件，則必須使用等效的共用機制（例如，用於共用代碼物件的 Maven）。

## <a name="transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions"></a>轉換 U-SQL 行集運算式和基於 SQL 的標量運算式

U-SQL 的核心語言正在轉換行集，並且基於 SQL。 以下是 U-SQL 中提供的最常見行集運算式的非詳盡清單：

- `SELECT`/`FROM`/`WHERE`/`GROUP BY`[聚合]`HAVING`/`ORDER BY`+`FETCH`
- `INNER`/`OUTER`/`CROSS`/`SEMI``JOIN`運算式
- `CROSS`/`OUTER``APPLY`運算式
- `PIVOT`/`UNPIVOT`表達 式
- `VALUES`排集建構函式

- 設置運算式`UNION`/`OUTER UNION`/`INTERSECT`/`EXCEPT`

此外，U-SQL 提供了各種基於 SQL 的標量運算式，例如

- `OVER`視窗運算式
- 各種內置聚合器和次序函數（`SUM`等） `FIRST`
- 一些最熟悉的 SQL 標量運算式： `CASE`、 `LIKE`、`NOT` `IN`（ `AND` `OR` ） 等。

Spark 以 DSL 和 SparkSQL 形式為大多數這些運算式提供了等效運算式。 Spark 中本機不支援的某些運算式必須使用本機 Spark 運算式和語義等效模式的組合進行重寫。 例如，`OUTER UNION`必須轉化為預測和聯合的等效組合。

由於對 Null 值的處理不同，如果比較的兩列都包含 Null 值，則 U-SQL 聯接始終匹配一行，而 Spark 中的聯接將不匹配此類列，除非添加顯式空檢查。

## <a name="transform-other-u-sql-concepts"></a>轉換其他 U-SQL 概念

U-SQL 還提供各種其他功能和概念，例如針對 SQL Server 資料庫、參數、標量和 lambda 運算式變數、系統變數和`OPTION`提示的聯集查詢。

### <a name="federated-queries-against-sql-server-databasesexternal-tables"></a>針對 SQL Server 資料庫/外部表的聯集查詢

U-SQL 提供資料來源和外部表，以及針對 Azure SQL 資料庫的直接查詢。 雖然 Spark 不提供相同的物件抽象，但它為[Azure SQL 資料庫提供了可用於](../sql-database/sql-database-spark-connector.md)查詢 SQL 資料庫的 Spark 連接器。

### <a name="u-sql-parameters-and-variables"></a>U-SQL 參數和變數

參數和使用者變數在 Spark 及其託管語言中具有等效的概念。

例如，在 Scala 中，可以使用`var`關鍵字定義變數：

```
var x = 2 * 3;
println(x)
```

U-SQL 的系統變數（以`@@`開頭的變數）可以分為兩類：

- 可設置為特定值以影響腳本行為的可設置系統變數
- 查詢系統和作業級別資訊的資訊系統變數

大多數可設置系統變數在 Spark 中沒有直接等效項。 某些資訊系統變數可以通過在作業執行期間將資訊作為參數傳遞來建模，而其他變數在 Spark 的託管語言中可能具有等效函數。

### <a name="u-sql-hints"></a>U-SQL 提示

U-SQL 提供了幾種語法方法，可向查詢最佳化工具和執行引擎提供提示：  

- 設置 U-SQL 系統變數
- 與`OPTION`行集運算式關聯的子句，用於提供資料或計畫提示
- 聯接運算式語法中的聯接提示（例如， `BROADCASTLEFT`

Spark 基於成本的查詢最佳化工具具有提供提示和調整查詢性能的功能。 請參閱相應的文檔。

## <a name="next-steps"></a>後續步驟

- [瞭解 U-SQL 開發人員的 Spark 資料格式](understand-spark-data-formats.md)
- [.NET for Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [將您的巨量資料分析解決方案從 Azure Data Lake Storage Gen1 升級為 Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [使用 Azure Data Factory 中的 Spark 活動轉換資料](../data-factory/transform-data-using-spark.md)
- [使用 Azure Data Factory 中的 Hadoop Hive 活動轉換資料](../data-factory/transform-data-using-hadoop-hive.md)
- [什麼是 Azure HDInsight 中的 Apache Spark](../hdinsight/spark/apache-spark-overview.md)
