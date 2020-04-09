---
title: 支援 Azure 資料儲存第 2 代的 Azure 服務 |微軟文件
description: 瞭解哪些 Azure 服務與 Azure 資料儲存第 2 代整合
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: edfcb1d0bfc87c84620b13ed26ec681fef32f1e3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878318"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>支援 Azure 資料儲存第 2 代的 Azure 服務

可以使用 Azure 服務來引入數據、執行分析並創建視覺化表示。 本文提供了受支援的 Azure 服務的清單,公開其支援等級,並為您提供指向有助於將這些服務與 Azure 資料湖存儲 Gen2 一起使用的文章的連結。

## <a name="supported-azure-services"></a>支援的 Azure 服務

此表列出了可用於 Azure 資料湖儲存 Gen2 的 Azure 服務。 隨著支援的繼續擴展,這些表中顯示的項將隨時間而變化。

> [!NOTE]
> 支援級別僅指數據湖存儲第 2 代如何支援服務。

|Azure 服務 |支援層級 |相關文章 |
|---------------|-------------------|---|
|Azure Data Factory|正式推出|[使用 Azure Data Factory 將資料載入 Azure Data Lake Storage Gen2 中](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|正式推出|[搭配使用 Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [快速入門:使用 Azure 資料塊分析 Azure 資料湖儲存第 2 代中的資料](data-lake-storage-quickstart-create-databricks-account.md) <br>[教學:使用 Azure 資料塊擷取、轉換和載入資料](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[教學:使用 Spark 使用 Azure 資料塊存取資料儲存第 2 代資料](data-lake-storage-use-databricks-spark.md)|
|Azure 事件中樞|正式推出|[透過 Azure 事件中樞在 Azure Blob 儲存體或 Azure Data Lake Storage 中擷取事件](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Event Grid|正式推出|[教學:實現資料湖擷取模式以更新資料區塊增量表](data-lake-storage-events.md)|
|Azure Logic Apps|正式推出|[概觀 - 什麼是 Azure Logic Apps？](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|正式推出|[存取 Azure 儲存服務中的資料](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Azure 串流分析|正式推出|[快速入門：使用 Azure 入口網站建立串流分析作業](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [進入 Azure 資料湖 第 2 代](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|資料箱|正式推出|[使用 Azure 資料框將資料從本地 HDFS 儲存到 Azure 儲存](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |正式推出|[搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[使用 HDFS CLI 搭配 Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[教學:在 Azure HDInsight 使用 Apache Hive 擷取、轉換和載入資料](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|IoT 中樞 |正式推出|[使用 IoT 中心訊息路由將裝置到雲端的消息傳送到不同的終結點](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|正式推出|[使用 Power BI 分析資料儲存第 2 代中的資料](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|SQL 資料倉儲|正式推出|[搭配使用 Azure SQL 資料倉儲](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS)|正式推出|[Azure 儲存體連線管理員](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Azue 認知搜尋|預覽|[索引與搜尋 Azure 資料儲存第 2 代文件(預覽版)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure 資料總管|預覽|[使用 Azure 資料資源管理員在 Azure 資料湖中查詢資料](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)|
|Azure 內容傳遞網路|尚不支援|[索引與搜尋 Azure 資料儲存第 2 代文件(預覽版)](https://docs.microsoft.com/azure/cdn/cdn-overview)|


## <a name="see-also"></a>另請參閱

- [Azure Data Lake Storage Gen2 的已知問題](data-lake-storage-known-issues.md)
- [Azure 資料湖儲存第 2 代中提供的 Blob 儲存功能](data-lake-storage-supported-blob-storage-features.md)
- [支援 Azure 資料儲存第 2 代的開源平臺](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage 上的多重通訊協定存取](data-lake-storage-multi-protocol-access.md)