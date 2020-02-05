---
title: 整合 Azure Data Lake Storage 與 Azure 服務 |Microsoft Docs
description: 瞭解哪些 Azure 服務與 Azure Data Lake Storage Gen2 整合。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: normesta
ms.openlocfilehash: 7f43f69ebdac05b8f99739ea6b51453671b9a70a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024546"
---
# <a name="integrate-azure-data-lake-storage-with-azure-services"></a>整合 Azure Data Lake Storage 與 Azure 服務

您可以使用 Azure 服務來內嵌資料、執行分析，以及建立視覺標記法。 本文提供支援的 Azure 服務清單，並提供您文章的連結，協助您將這些服務與 Azure Data Lake Storage Gen2 搭配使用。

## <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>支援 Azure Data Lake Storage Gen2 的 Azure 服務

下表列出支援 Azure Data Lake Storage Gen2 的 Azure 服務。

| Azure 服務 |  相關文章 |
|---------------|-------------------|
|Azure Data Factory | [使用 Azure Data Factory 將資料載入 Azure Data Lake Storage Gen2 中](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks | [搭配 Azure Databricks 使用](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [快速入門：使用 Azure Databricks 分析 Azure Data Lake Storage Gen2 中的資料](data-lake-storage-quickstart-create-databricks-account.md) <br>[教學課程：使用 Azure Databricks 解壓縮、轉換和載入資料](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[教學課程：使用 Spark 以 Azure Databricks 存取 Data Lake Storage Gen2 資料](data-lake-storage-use-databricks-spark.md) |
|Azure 事件中樞 capture| [透過 Azure Blob 儲存體或 Azure Data Lake Storage 中的 Azure 事件中樞來捕捉事件](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Logic Apps | [總覽-Azure Logic Apps 是什麼？](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|[存取 Azure 儲存體服務中的資料](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Azure 認知搜尋 | [Azure Data Lake Storage Gen2 檔編制索引和搜尋（預覽）](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure 串流分析| [快速入門：使用 Azure 入口網站建立串流分析作業](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Azure Data Lake Gen2 的輸出](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2) |
|Data Box|  [使用 Azure 資料箱將內部部署 HDFS 存放區中的資料移轉至 Azure 儲存體](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight | [搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[搭配 Data Lake Storage Gen2 使用 HDFS CLI](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[教學課程：在 Azure HDInsight 上使用 Apache Hive 來解壓縮、轉換和載入資料](data-lake-storage-tutorial-extract-transform-load-hive.md) |
|IoT Hub | [使用 IoT 中樞訊息路由將裝置到雲端訊息傳送至不同的端點](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|  [使用 Power BI 分析 Data Lake Storage Gen2 中的資料](https://docs.microsoft.com/power-query/connectors/datalakestorage) |
|SQL 資料倉儲 | [搭配 Azure SQL 資料倉儲使用](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS) | [Azure 儲存體連線管理員](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|

## <a name="next-steps"></a>後續步驟

- 深入瞭解您可以使用哪些工具來處理 data lake 中的資料。 [如需 big Data 需求，請參閱使用 Azure Data Lake Storage Gen2](data-lake-storage-data-scenarios.md)。

- 深入瞭解 Data Lake Storage Gen2，以及如何開始使用。 請參閱[Azure Data Lake Storage Gen2 簡介](data-lake-storage-introduction.md)
