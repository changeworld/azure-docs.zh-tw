---
title: 瞭解 Azure Data Lake Analytics 的 U SQL 開發人員 Apache Spark 資料格式。
description: 本文描述 Apache Spark 的概念，以協助 U_SQL 開發人員瞭解 SQL-DMO 和 Spark 資料格式之間的差異。
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: bff8c89dcdcbb7c319e04e5e7518985badf5a5ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132308"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>瞭解 U-SQL 與 Spark 資料格式之間的差異

如果您想要使用 [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) 或 [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md)，建議您將資料從 [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) 遷移至 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)。

除了移動您的檔案之外，您也會想要讓 Spark 可以存取儲存在 U SQL 資料表中的資料。

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>移動 Azure Data Lake Storage Gen1 檔案中儲存的資料

檔案中儲存的資料可以各種不同的方式移動：

- 撰寫 [Azure Data Factory](../data-factory/introduction.md) 管線，以將資料從 [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) 帳戶複製到 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 帳戶。
- 撰寫 Spark 作業，從 [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) 帳戶讀取資料，並將它寫入 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 帳戶。 根據您的使用案例，您可能會想要以不同的格式（例如 Parquet）來撰寫，如果您不需要保留源檔案格式。

建議您參閱將[您的大型資料分析解決方案從 Azure Data Lake Storage Gen1 升級到 Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)的文章。

## <a name="move-data-stored-in-u-sql-tables"></a>移動儲存在 U SQL 資料表中的資料

Spark 無法理解 U SQL 資料表。 如果您將資料儲存在 U SQL 資料表中，您將會執行可解壓縮資料表資料的 U SQL 作業，並將其儲存為 Spark 可理解的格式。 最適當的格式是在 Hive 中繼存放區的資料夾配置之後，建立一組 Parquet 檔案。

您可以使用內建的 Parquet 輸出器，並搭配使用動態輸出資料分割和檔案集來建立磁碟分割資料夾，以在 U-SQL 中達成輸出。 [處理比以往更多的檔案，並使用 Parquet](https://blogs.msdn.microsoft.com/azuredatalake/2018/06/11/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics) 提供如何建立這類 Spark 可耗用資料的範例。

在這項轉換之後，您會複製資料，如下列章節所述： [移動儲存在 Azure Data Lake Storage Gen1 檔案中的資料](#move-data-stored-in-azure-data-lake-storage-gen1-files)。

## <a name="caveats"></a>警示

- 複製檔案時的資料語法，會在位元組層級進行複製。 因此，相同的資料應該會出現在 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 帳戶中。 不過請注意，Spark 可能會以不同的方式解讀某些字元。 例如，它可能會針對 CSV 檔案中的資料列分隔符號使用不同的預設值。
    此外，如果您從資料表) 複製具類型的資料 (，則 Parquet 和 Spark 可能會對某些具類型的值有不同的精確度和小數位數 (例如，浮點數) ，而且可能會以不同的方式處理 null 值。 例如，當 Spark 有 null 值的三值邏輯時，就會有雙 SQL 的 c # 語義。

- 資料組織 (資料分割) U SQL 資料表提供兩種層級的資料分割。 外部層級 (`PARTITIONED BY`) 是依價值，而對應大多是使用資料夾階層的 Hive/Spark 資料分割配置。 您必須確保 null 值會對應到正確的資料夾。 `DISTRIBUTED BY`在 U-SQL 中) 的內部層級 (提供4種散發配置：迴圈配置資源、範圍、雜湊和直接雜湊。
    Hive/Spark 資料表只支援值分割或雜湊資料分割，但使用的雜湊函數與 SQL-DMO 不同。 當您輸出您的 U SQL 資料表資料時，您可能只能夠對應至 Spark 的值資料分割，而且可能需要根據您的最終 Spark 查詢進一步調整資料版面配置。

## <a name="next-steps"></a>接下來的步驟

- [瞭解適用于 U-SQL 開發人員的 Spark 程式碼概念](understand-spark-code-concepts.md)
- [將您的巨量資料分析解決方案從 Azure Data Lake Storage Gen1 升級為 Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [適用於 Apache Spark 的 .NET](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [使用 Azure Data Factory 中的 Spark 活動轉換資料](../data-factory/transform-data-using-spark.md)
- [使用 Azure Data Factory 中的 Hadoop Hive 活動轉換資料](../data-factory/transform-data-using-hadoop-hive.md)
- [什麼是 Azure HDInsight 中的 Apache Spark](../hdinsight/spark/apache-spark-overview.md)
