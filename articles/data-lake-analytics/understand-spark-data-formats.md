---
title: 瞭解 Azure Data Lake Analytics U-SQL 開發人員的 Apache Spark 資料格式。
description: 本文說明 Apache Spark 的概念，以協助 U_SQL 的開發人員瞭解 U-SQL 與 Spark 資料格式之間的差異。
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 36f39503ca32f1ee4b422ae7b1cf9abf48716f07
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73648437"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>瞭解 U-SQL 與 Spark 資料格式之間的差異

如果您想要使用[Azure Databricks](../azure-databricks/what-is-azure-databricks.md)或[Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md)，建議您將資料從[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)遷移到[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)。

除了移動檔案之外，您也會想要將資料儲存在可供 Spark 存取的 U-SQL 資料表中。

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>移動儲存在 Azure Data Lake Storage Gen1 檔案中的資料

儲存在檔案中的資料可透過各種方式移動：

- 撰寫[Azure Data Factory](../data-factory/introduction.md)管線，將資料從[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)帳戶複製到[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)帳戶。
- 撰寫 Spark 作業，以讀取來自[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)帳戶的資料，並將它寫入[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)帳戶。 根據您的使用案例，如果您不需要保留源檔案格式，您可能會想要以不同的格式（例如 Parquet）來撰寫它。

建議您參閱將[您的海量資料分析解決方案從 Azure Data Lake Storage Gen1 升級至 Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)一文

## <a name="move-data-stored-in-u-sql-tables"></a>移動儲存在 U-SQL 資料表中的資料

Spark 不了解 U-SQL 資料表。 如果您將資料儲存在 U-SQL 資料表中，您將會執行一個會解壓縮資料表資料的 U-SQL 作業，並以 Spark 瞭解的格式儲存。 最適當的格式是在 Hive 中繼存放區的資料夾版面配置之後，建立一組 Parquet 檔案。

您可以使用內建的 Parquet 輸出器，並搭配使用動態輸出分割與檔案集來建立磁碟分割資料夾，以在 U-SQL 中達成輸出。 [處理比以往更多的檔案，並使用 Parquet](https://blogs.msdn.microsoft.com/azuredatalake/2018/06/11/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics)提供如何建立這類 Spark 可耗用資料的範例。

在此轉換之後，您可以複製資料，如[移動儲存在 Azure Data Lake Storage Gen1 檔案中的資料](#move-data-stored-in-azure-data-lake-storage-gen1-files)一章所述。

## <a name="caveats"></a>警示

- 資料語義複製檔案時，複製將會發生在位元組層級。 因此，相同的資料應該會出現在[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)帳戶中。 不過，請注意，Spark 可能會以不同的方式來解讀某些字元。 例如，它可能會針對 CSV 檔案中的資料列分隔符號使用不同的預設值。
    此外，如果您要複製具類型的資料（從資料表），則 Parquet 和 Spark 對於某些類型的值（例如 float）可能會有不同的有效位數和小數位數，而且可能會以不同的方式來處理 null 值。 例如，U-SQL 具有 null 值的 c # 語義，而 Spark 有三個值的邏輯用於 null 值。

- 資料組織（分割）： U-SQL 資料表提供兩個層級的分割。 外部層級（`PARTITIONED BY`）是藉由值和對應，大部分都是使用資料夾階層的 Hive/Spark 資料分割配置。 您必須確定 null 值會對應到正確的資料夾。 U-SQL 中`DISTRIBUTED BY`的內部層級（）提供4個散發配置：迴圈配置資源、範圍、雜湊和直接雜湊。
    Hive/Spark 資料表只支援使用與 U-SQL 不同的雜湊函數來進行值分割或雜湊資料分割。 當您輸出您的 U-SQL 資料表資料時，您可能只能夠對應到 Spark 的值分割，而且可能需要根據您的最終 Spark 查詢進一步調整資料配置。

## <a name="next-steps"></a>後續步驟

- [瞭解適用于 U-SQL 開發人員的 Spark 程式碼概念](understand-spark-code-concepts.md)
- [將您的巨量資料分析解決方案從 Azure Data Lake Storage Gen1 升級為 Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [適用於 Apache Spark 的 .NET](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [使用 Azure Data Factory 中的 Spark 活動轉換資料](../data-factory/transform-data-using-spark.md)
- [使用 Azure Data Factory 中的 Hadoop Hive 活動轉換資料](../data-factory/transform-data-using-hadoop-hive.md)
- [什麼是 Azure HDInsight 中的 Apache Spark](../hdinsight/spark/apache-spark-overview.md)
