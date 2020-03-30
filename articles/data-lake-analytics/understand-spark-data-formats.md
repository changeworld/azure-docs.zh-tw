---
title: 瞭解 Azure 資料湖分析 U-SQL 開發人員的 Apache Spark 資料格式。
description: 本文介紹了 Apache Spark 概念，以説明U_SQL開發人員瞭解 U-SQL 和 Spark 資料格式之間的差異。
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 36f39503ca32f1ee4b422ae7b1cf9abf48716f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648437"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>瞭解 U-SQL 和 Spark 資料格式之間的差異

如果要使用[Azure 資料塊](../azure-databricks/what-is-azure-databricks.md)或[Azure HDInsight Spark，](../hdinsight/spark/apache-spark-overview.md)我們建議您將資料從[Azure 資料存儲第 1 代](../data-lake-store/data-lake-store-overview.md)遷移到[Azure 資料湖存儲 Gen2](../storage/blobs/data-lake-storage-introduction.md)。

除了移動檔之外，您還需要使存儲在 U-SQL 表中的資料可供 Spark 訪問。

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>移動存儲在 Azure 資料存儲第 1 代檔中的資料

存儲在檔中的資料可以通過多種方式移動：

- 編寫[Azure 資料工廠](../data-factory/introduction.md)管道將資料從[Azure 資料存儲庫存儲 Gen1](../data-lake-store/data-lake-store-overview.md)帳戶複製到[Azure 資料湖存儲 Gen2](../storage/blobs/data-lake-storage-introduction.md)帳戶。
- 編寫 Spark 作業，從 Azure[資料存儲庫存儲 Gen1](../data-lake-store/data-lake-store-overview.md)帳戶中讀取資料並將其寫入[Azure 資料湖存儲 Gen2](../storage/blobs/data-lake-storage-introduction.md)帳戶。 根據用例，如果您不需要保留原始檔案格式，則可能需要以其他格式（如 Parquet）編寫它。

我們建議您查看文章[，將大資料分析解決方案從 Azure 資料存儲第 1 代升級到 Azure 資料湖存儲 Gen2](../storage/blobs/data-lake-storage-upgrade.md)

## <a name="move-data-stored-in-u-sql-tables"></a>移動存儲在 U-SQL 表中的資料

Spark 無法理解 U-SQL 表。 如果資料存儲在 U-SQL 表中，則運行 U-SQL 作業，該作業提取表資料並將其保存為 Spark 理解的格式。 最合適的格式是在 Hive 元存儲的資料夾佈局之後創建一組 Parquet 檔。

輸出可以通過內置 Parquet 輸出器在 U-SQL 中實現，並使用帶檔集的動態輸出分區來創建分區資料夾。 [處理的檔比以往任何時候都多，使用 Parquet](https://blogs.msdn.microsoft.com/azuredatalake/2018/06/11/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics)提供了如何創建此類 Spark 易耗資料的示例。

在此轉換後，您可以複製存儲在[Azure 資料湖存儲 Gen1 檔中](#move-data-stored-in-azure-data-lake-storage-gen1-files)的章節中的所述資料。

## <a name="caveats"></a>警示

- 資料語義 複製檔時，複製將在位元組級別發生。 因此[，Azure 資料湖存儲 Gen2](../storage/blobs/data-lake-storage-introduction.md)帳戶中應顯示相同的資料。 但請注意，Spark 可能會以不同的方式解釋某些字元。 例如，它可能對 CSV 檔中的行分隔符號使用不同的預設值。
    此外，如果要複製類型化資料（來自表），則 Parquet 和 Spark 對某些類型化值（例如 float）的精度和比例可能不同，並且可能會以不同的方式對待空值。 例如，U-SQL 具有 null 值的 C# 語義，而 Spark 具有 null 值的三值邏輯。

- 資料組織（分區）U-SQL 表提供兩個級別分區。 外部級別 （`PARTITIONED BY`） 按值和映射主要到使用資料夾層次結構的 Hive/Spark 分區方案。 您需要確保 null 值對應到正確的資料夾。 U-SQL`DISTRIBUTED BY`中的內部級別 （ ） 提供 4 種分佈方案：迴圈、範圍、雜湊和直接雜湊。
    Hive/Spark 表僅支援使用與 U-SQL 不同的雜湊函數進行值分區或雜湊分割。 輸出 U-SQL 表資料時，您可能只能映射到 Spark 的值分區，並且可能需要根據最終的 Spark 查詢進行進一步調整資料佈局。

## <a name="next-steps"></a>後續步驟

- [瞭解 U-SQL 開發人員的 Spark 代碼概念](understand-spark-code-concepts.md)
- [將您的巨量資料分析解決方案從 Azure Data Lake Storage Gen1 升級為 Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [.NET for Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [使用 Azure Data Factory 中的 Spark 活動轉換資料](../data-factory/transform-data-using-spark.md)
- [使用 Azure Data Factory 中的 Hadoop Hive 活動轉換資料](../data-factory/transform-data-using-hadoop-hive.md)
- [什麼是 Azure HDInsight 中的 Apache Spark](../hdinsight/spark/apache-spark-overview.md)
