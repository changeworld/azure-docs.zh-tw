---
title: 設計 ELT，而不是 ETL
description: 針對 Azure Synapse 分析內的 SQL 分析執行彈性的資料載入策略
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 02/19/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: bd356fba557d61f083e811c8763b4e7cf9805fbb
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199932"
---
# <a name="data-loading-strategies-for-data-warehousing"></a>資料倉儲的資料載入策略

傳統 SMP 資料倉儲會使用「解壓縮」、「轉換」和「載入」（ETL）處理常式來載入資料。 Azure Synapse 分析中的 SQL 集區具有大量平行處理（MPP）架構，會利用計算和儲存體資源的擴充性和彈性。 運用擷取、載入及轉換 (ELT) 程序可以利用 MPP，而且不需要載入之前轉換資料的資源。 雖然 SQL 集區支援許多載入方法，包括受歡迎的 SQL Server 選項，例如 BCP 和 SQL BulkCopy API，但載入資料的最快速且可調整的方式是透過 PolyBase 外部資料表和[COPY 語句](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)（預覽）。 使用 PolyBase 和 COPY 語句，您可以透過 T-sql 語言存取儲存在 Azure Blob 儲存體或 Azure Data Lake 存放區中的外部資料。 若要在載入時擁有最大的彈性，建議使用 COPY 語句。

> [!NOTE]  
> COPY 陳述式目前處於公開預覽階段。 若要提供意見反應，請將電子郵件傳送至下列通訊群組清單： sqldwcopypreview@service.microsoft.com。


> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]


## <a name="what-is-elt"></a>什麼是 ELT?

「解壓縮」、「載入」和「轉換」（ELT）是一種程式，可讓資料從來源系統中解壓縮、載入資料倉儲，然後進行轉換。 

執行 ELT 的基本步驟如下：

1. 將來源資料擷取至文字檔。
2. 讓資料登陸到 Azure Blob 儲存體或 Azure Data Lake Store。
3. 準備要載入的資料。
4. 使用 PolyBase 或 COPY 命令，將資料載入臨時表中。 
5. 轉換資料。
6. 將資料插入生產資料表。


如需 PolyBase 載入教學課程，請參閱[使用 polybase 從 Azure blob 儲存體載入資料](load-data-from-azure-blob-storage-using-polybase.md)。

如需詳細資訊，請參閱[載入模式部落格](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)。 


## <a name="1-extract-the-source-data-into-text-files"></a>1. 將來源資料解壓縮至文字檔

從來源系統取得資料視儲存位置而定。  目標是將資料移至 PolyBase 和複製支援的分隔文字或 CSV 檔案。 

### <a name="polybase-and-copy-external-file-formats"></a>PolyBase 和複製外部檔案格式

使用 PolyBase 和 COPY 語句，您可以從 UTF-8 和 UTF-16 編碼的分隔文字或 CSV 檔案載入資料。 除了分隔的文字或 CSV 檔案之外，它還會從 Hadoop 檔案格式（例如 ORC 和 Parquet）載入。 PolyBase 和 COPY 語句也可以從 Gzip 和 Snappy 壓縮檔案載入資料。 不支援擴充 ASCII、固定寬度格式和嵌套格式，例如 WinZip 或 XML。 如果您要從 SQL Server 匯出，可以使用[bcp 命令列工具](/sql/tools/bcp-utility?view=azure-sqldw-latest)，將資料匯出成分隔的文字檔。 

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. 將資料放入 Azure Blob 儲存體或 Azure Data Lake 存放區

若要將資料放在 Azure 儲存體中，您可以將它移至[Azure Blob 儲存體](../storage/blobs/storage-blobs-introduction.md)或[Azure Data Lake 存放區 Gen2](../data-lake-store/data-lake-store-overview.md)。 在任一位置中，資料應該會儲存到文字檔。 PolyBase 和 COPY 語句可以從任一位置載入。

您可以用來將資料移至 Azure 儲存體的工具和服務：

- [Azure ExpressRoute](../expressroute/expressroute-introduction.md) 服務會增強網路輸送量、效能及可預測性。 ExpressRoute 是一項服務，它會透過專用私人連線將您的資料路由傳送至 Azure。 ExpressRoute 連線不會透過公用網際網路路由傳送資料。 相較於透過公用網際網路的一般連線，這個連線提供更為可靠、速度更快、延遲更低且安全性更高的網際網路連線。
- [AZCopy 公用程式](../storage/common/storage-moving-data.md)透過公用網際網路將資料移至 Azure 儲存體。 如果您的資料大小小於 10 TB，就適用這個選項。 若要使用 AZCopy 定期執行載入，請測試網路速度以查看是否可以接受。 
- [Azure Data Factory (ADF)](../data-factory/introduction.md) 具有閘道，您可以在本機伺服器上安裝。 然後您可以建立管線，將資料從本機伺服器移至 Azure 儲存體。 若要使用 Data Factory 搭配 SQL 分析，請參閱[載入 Sql 分析的資料](/azure/data-factory/load-azure-sql-data-warehouse)。


## <a name="3-prepare-the-data-for-loading"></a>3. 準備要載入的資料

您可能需要先準備並清除儲存體帳戶中的資料，然後再載入。 資料準備可以在您的資料是在來源中、當您將資料匯出到文字檔時，或是在資料在 Azure 儲存體之後執行。  盡可能儘早在程序中使用資料最簡單。  

### <a name="define-external-tables"></a>定義外部資料表

如果您使用 PolyBase，您必須先在資料倉儲中定義外部資料表，然後再載入。 COPY 語句不需要外部資料表。 PolyBase 使用外部資料表以定義及存取 Azure 儲存體中的資料。 外部資料表類似於資料表檢視。 外部資料表包含資料表結構描述，並指向儲存在資料倉儲外部的資料。 

定義外部資料表牽涉到指定資料來源、文字檔格式和資料表定義。 您將需要的 t-sql 語法主題如下：
- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest)

載入 Parquet 時，SQL 資料類型對應為：

| **Parquet 資料類型** | **SQL 資料類型** |
| :-------------------: | :---------------: |
|        tinyint        |      tinyint      |
|       smallint        |     smallint      |
|          int          |        int        |
|        BIGINT         |      BIGINT       |
|        boolean        |        位元        |
|        double         |       float       |
|         float         |       real        |
|        double         |       money       |
|        double         |    SMALLMONEY     |
|        string         |       NCHAR       |
|        string         |     nvarchar      |
|        string         |       char        |
|        string         |      varchar      |
|        二進位         |      二進位       |
|        二進位         |     varbinary     |
|       timestamp       |       date        |
|       timestamp       |   smalldatetime   |
|       timestamp       |     datetime2     |
|       timestamp       |     datetime      |
|       timestamp       |       time        |
|         date          |       date        |
|        decimal        |      decimal      |

如需建立外部物件的範例，請參閱載入教學課程中的[建立外部資料表](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data)步驟。

### <a name="format-text-files"></a>格式化文字檔

如果您使用 PolyBase，則定義的外部物件需要將文字檔的資料列與外部資料表和檔案格式定義對齊。 文字檔之每個資料列中的資料必須對齊資料表定義。
若要格式化文字檔：

- 如果您的資料是來自非關聯式來源，您必須將它轉換成資料列和資料行。 無論資料是來自關聯式或非關聯式來源，資料都必須轉換以對齊您打算將資料載入其中之資料表的資料行定義。 
- 將文字檔中的資料格式化，以與目的地資料表中的資料行和資料類型對齊。 如果外部文字檔與資料倉儲資料表的的資料類型之間沒有對齊，會導致在載入期間資料列遭到拒絕。
- 使用結束字元分隔文字檔中的欄位。  請務必使用在來源資料中找不到的字元或字元序列。 搭配 [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql) 使用您指定的結束字元。


## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. 使用 PolyBase 或 COPY 語句載入資料

這是將資料載入暫存資料表的最佳做法。 暫存資料表可讓您處理錯誤，而不會干擾生產資料表。 臨時表也讓您有機會在將資料插入生產資料表之前，先使用 SQL 集區 MPP 進行資料轉換。 載入具有複製的臨時表時，必須預先建立資料表。

### <a name="options-for-loading-with-polybase-and-copy-statement"></a>使用 PolyBase 和 COPY 語句載入的選項

若要使用 PolyBase 載入資料，您可以使用任何一種載入選項：

- [PolyBase 與 T-SQL](load-data-from-azure-blob-storage-using-polybase.md) 非常適合於當您的資料是在 Azure Blob 儲存體或 Azure Data Lake Store 中的時候。 它給予您對於載入程序最多的控制權，但是也需要您定義外部資料物件。 其他方法會在您將來源資料表對應至目的地資料表時，在幕後定義這些物件。  若要協調 T-SQL 載入，您可以使用 Azure Data Factory、SSIS 或 Azure 函式。 
- [PolyBase 與 SSIS](/sql/integration-services/load-data-to-sql-data-warehouse) 非常適合於當您的來源資料是在 SQL Server 中的時候，無論是 SQL Server 內部部署或是在雲端。 SSIS 會定義來源至目的地資料表對應，也會協調載入。 如果您已經有 SSIS 套件，您可以將套件修改為搭配新的資料倉儲目的地。 
- [具有 Azure Data Factory （ADF）的 PolyBase 和 COPY 語句](sql-data-warehouse-load-with-data-factory.md)是另一個協調流程工具。  它會定義管線並排程作業。 
- [具有 Azure Databricks 的 PolyBase](../azure-databricks/databricks-extract-load-sql-data-warehouse.md)會將資料表的資料傳輸至 Databricks 資料框架，並（或）使用 polybase 將資料從 Databricks 資料框架寫入資料表。

### <a name="other-loading-options"></a>其他載入選項

除了 PolyBase 和 COPY 語句以外，您還可以使用[bcp](/sql/tools/bcp-utility?view=azure-sqldw-latest)或[SQLBulkCopy API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx)。 bcp 會直接載入資料庫，而不需要透過 Azure Blob 儲存體，而且僅適用于小型負載。 請注意，這些選項的負載效能會比 PolyBase 和 COPY 語句慢。 


## <a name="5-transform-the-data"></a>5. 轉換資料

當資料在暫存表格時，執行您的工作負載需要的轉換。 然後將資料移至生產資料表中。


## <a name="6-insert-the-data-into-production-tables"></a>6. 將資料插入生產資料表

插入 .。。SELECT 語句會將資料從臨時表移至永久資料表。 

當您設計 ETL 程序時，嘗試在小型測試範例上執行程序。 嘗試從資料表將 1000 個資料列擷取至檔案，將它移至 Azure，然後嘗試將它載入暫存表格。 


## <a name="partner-loading-solutions"></a>合作夥伴載入解決方案

我們有許多合作夥伴皆提供載入解決方案。 若要深入了解，請參閱我們的[解決方案合作夥伴](sql-data-warehouse-partner-business-intelligence.md)清單。 


## <a name="next-steps"></a>後續步驟

如需載入指引，請參閱[載入資料的指引](guidance-for-loading-data.md)。
