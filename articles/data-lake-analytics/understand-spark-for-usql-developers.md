---
title: 瞭解 Azure 資料湖分析 U-SQL 開發人員的 Apache Spark。
description: 本文介紹了 Apache Spark 概念，以説明您在 U-SQL 開發人員之間存在差異。
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: 594e1055c4c063e4e151fefa3e183e6e799c90b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648424"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>了解適用於 U-SQL 開發人員的 Apache Spark

Microsoft 支援多個分析服務，如[Azure 資料塊](../azure-databricks/what-is-azure-databricks.md)和[Azure HDInsight](../hdinsight/hdinsight-overview.md)以及 Azure 資料湖分析。 我們從開發人員那裡得知，他們在構建分析管道時明顯傾向于開源解決方案。 為了説明 U-SQL 開發人員瞭解 Apache Spark 以及如何將 U-SQL 腳本轉換為 Apache Spark，我們創建了本指南。

它包括您可以採取的一些步驟，以及幾個備選方案。

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>將 U-SQL 轉換為 Apache Spark 的步驟

1. 轉換作業業務流程管道。

   如果使用[Azure 資料工廠](../data-factory/introduction.md)來協調 Azure 資料湖分析腳本，則必須調整它們以協調新的 Spark 程式。
2. 瞭解 U-SQL 和 Spark 管理資料之間的區別

   如果要將資料從[Azure 資料存儲第 1 代](../data-lake-store/data-lake-store-overview.md)移動到 Azure[資料湖存儲 Gen2，](../storage/blobs/data-lake-storage-introduction.md)必須同時複製檔資料和目錄維護的資料。 請注意，Azure 資料湖分析僅支援 Azure 資料湖存儲第 1 代。 請參閱[瞭解 Spark 資料格式](understand-spark-data-formats.md)
3. 將 U-SQL 腳本轉換為 Spark

   在轉換 U-SQL 腳本之前，您必須選擇分析服務。 一些可用的計算服務包括：
      - [Azure 資料工廠資料流程](../data-factory/concepts-data-flow-overview.md)映射資料流程是視覺化設計的資料轉換，允許資料工程師無需編寫代碼即可開發圖形資料轉換邏輯。 雖然它們不適合執行複雜的使用者代碼，但它們可以很容易地表示傳統的類似 SQL 的資料流程轉換
      - [Azure HDInsight 蜂巢](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md)HDInsight 上的 Apache 蜂巢適用于擷取、轉換和下載 （ETL） 操作。 這意味著您將 U-SQL 腳本轉換為 Apache Hive。
      - Apache Spark 引擎（如[Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md)或[Azure 資料塊](../azure-databricks/what-is-azure-databricks.md)）這意味著您將將 U-SQL 腳本轉換為 Spark。 有關詳細資訊，請參閱瞭解[Spark 資料格式](understand-spark-data-formats.md)

> [!CAUTION]
> [Azure 資料塊](../azure-databricks/what-is-azure-databricks.md)和[Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md)都是叢集服務，而不是無伺服器作業，如 Azure 資料湖分析。 您必須考慮如何預配群集以獲得適當的成本/性能比率，以及如何管理其生命週期以最小化成本。 這些服務具有不同的性能特徵，使用者代碼以 .NET 編寫，因此您必須編寫包裝器或用受支援的語言重寫代碼。 有關詳細資訊，請參閱瞭解[Spark 資料格式](understand-spark-data-formats.md)、[瞭解 U-SQL 開發人員的 Apache Spark 代碼概念](understand-spark-code-concepts.md) [.net 表示阿帕奇 Spark](https://dotnet.microsoft.com/apps/data/spark)

## <a name="next-steps"></a>後續步驟

- [瞭解 U-SQL 開發人員的 Spark 資料格式](understand-spark-data-formats.md)
- [瞭解 U-SQL 開發人員的 Spark 代碼概念](understand-spark-code-concepts.md)
- [將您的巨量資料分析解決方案從 Azure Data Lake Storage Gen1 升級為 Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [.NET for Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [使用 Azure Data Factory 中的 Hadoop Hive 活動轉換資料](../data-factory/transform-data-using-hadoop-hive.md)
- [使用 Azure Data Factory 中的 Spark 活動轉換資料](../data-factory/transform-data-using-spark.md)
- [什麼是 Azure HDInsight 中的 Apache Spark](../hdinsight/spark/apache-spark-overview.md)
