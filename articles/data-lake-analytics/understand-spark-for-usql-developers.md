---
title: 瞭解 Azure Data Lake Analytics U-SQL 開發人員的 Apache Spark。
description: 本文將說明 Apache Spark 的概念，以協助您進行與 SQL 開發人員之間的差異。
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: a66a82a6d2a5bb1f534ed211091793b2ab4d2a28
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221089"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>了解適用於 U-SQL 開發人員的 Apache Spark

Microsoft 支援多項分析服務，例如 [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) 和 [Azure HDInsight](../hdinsight/hdinsight-overview.md) ，以及 Azure Data Lake Analytics。 我們聽取開發人員的意見，讓他們能夠在建立分析管線時，對開放原始碼解決方案有很清楚的喜好設定。 為了協助 SQL 開發人員瞭解 Apache Spark，以及您如何將您的 U SQL 腳本轉換成 Apache Spark，我們已建立了本指南。

它包含數個您可以採取的步驟，以及數個替代方案。

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>將 SQL-DMO 轉換成 Apache Spark 的步驟

1. 轉換您的作業協調流程管線。

   如果您使用 [Azure Data Factory](../data-factory/introduction.md) 來協調您的 Azure Data Lake Analytics 腳本，您必須加以調整以協調新的 Spark 程式。
2. 瞭解如何在 SQL 和 Spark 管理資料之間的差異

   如果您想要將資料從 [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) 移至 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)，您必須複製檔案資料和目錄維護的資料。 請注意，Azure Data Lake Analytics 只支援 Azure Data Lake Storage Gen1。 請參閱 [瞭解 Spark 資料格式](understand-spark-data-formats.md)
3. 將您的 U-SQL 腳本轉換為 Spark

   轉換您的 U SQL 腳本之前，您必須選擇分析服務。 可用的一些計算服務如下：
      - [Azure Data Factory 資料流程](../data-factory/concepts-data-flow-overview.md) 對應資料流程是以視覺方式設計的資料轉換，可讓資料工程師開發圖形化資料轉換邏輯，而不需要撰寫程式碼。 雖然不適合用來執行複雜的使用者程式碼，但它們可以輕鬆地代表傳統的類似 SQL 的資料流程轉換
      - [Azure HDInsight Hive](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md) HDInsight 上的 Apache Hive 適合用來解壓縮、轉換和載入 (ETL) 作業。 這表示您將會轉譯您的 U SQL 腳本以 Apache Hive。
      - Apache Spark 引擎（例如 [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) 或 [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) ）表示您會將您的 U SQL 腳本轉譯為 Spark。 如需詳細資訊，請參閱 [瞭解 Spark 資料格式](understand-spark-data-formats.md)

> [!CAUTION]
> [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks)和[Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md)都是叢集服務，而非無伺服器作業（例如 Azure Data Lake Analytics）。 您必須考慮如何布建叢集，以取得適當的成本/效能比例，以及如何管理其存留期，以將成本降至最低。 這些服務與以 .NET 撰寫的使用者程式碼具有不同的效能特性，因此您必須以支援的語言撰寫包裝函式或重寫程式碼。 如需詳細資訊，請參閱 [瞭解 Spark 資料格式](understand-spark-data-formats.md)、 [瞭解適用于 SQL 開發人員的 Apache Spark 程式碼概念](understand-spark-code-concepts.md)、 [適用于 Apache Spark 的 .net](https://dotnet.microsoft.com/apps/data/spark)

## <a name="next-steps"></a>後續步驟

- [瞭解適用于 U SQL 開發人員的 Spark 資料格式](understand-spark-data-formats.md)
- [瞭解適用于 U-SQL 開發人員的 Spark 程式碼概念](understand-spark-code-concepts.md)
- [將您的巨量資料分析解決方案從 Azure Data Lake Storage Gen1 升級為 Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [適用於 Apache Spark 的 .NET](/dotnet/spark/what-is-apache-spark-dotnet)
- [使用 Azure Data Factory 中的 Hadoop Hive 活動轉換資料](../data-factory/transform-data-using-hadoop-hive.md)
- [使用 Azure Data Factory 中的 Spark 活動轉換資料](../data-factory/transform-data-using-spark.md)
- [什麼是 Azure HDInsight 中的 Apache Spark](../hdinsight/spark/apache-spark-overview.md)