---
title: 設計 ELT，而不是 ETL
description: 針對 Azure Synapse Analytics 中的 Synapse SQL 集區實作彈性的資料載入策略
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/13/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 1b73b82b4367d50cc5fbe9881a67e0afa041db86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85201153"
---
# <a name="data-loading-strategies-for-synapse-sql-pool"></a>Synapse SQL 集區的資料載入策略

傳統 SMP SQL 集區會使用擷取、轉換和載入 (ETL) 程序來載入資料。 Azure Synapse Analytics 中的 Synapse SQL 集區具有大量平行處理 (MPP) 架構，可利用計算和儲存體資源的延展性和彈性。

使用擷取、載入及轉換 (ELT) 程序可以利用 MPP，而且不需要載入之前轉換資料的資源。

雖然 SQL 集區支援許多載入方法，包括熱門的 SQL Server 選項 (例如 [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 和 [SqlBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json))，但是載入資料最快速且最能調整的方式是透過 PolyBase 外部資料表和 [COPY 陳述式](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (預覽)。

使用 PolyBase 和 COPY 陳述式，您可以透過 T-SQL 語言存取在 Azure Blob 儲存體或 Azure Data Lake Store 中儲存的外部資料。 若要在載入時擁有最大的彈性，建議使用 COPY 陳述式。

> [!NOTE]  
> COPY 陳述式目前處於公開預覽階段。 若要提供意見反應，請將電子郵件傳送至下列通訊群組清單：sqldwcopypreview@service.microsoft.com。

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="what-is-elt"></a>什麼是 ELT?

擷取、載入及轉換 (ELT) 是從來源系統擷取資料、載入至 SQL 集區再進行轉換的程序。

實作 ELT 的基本步驟如下：

1. 將來源資料擷取至文字檔。
2. 讓資料登陸到 Azure Blob 儲存體或 Azure Data Lake Store。
3. 準備要載入的資料。
4. 使用 PolyBase 或 COPY 命令，將資料載入暫存表格中。
5. 轉換資料。
6. 將資料插入生產資料表。

如需載入教學課程，請參閱 [從 Azure blob 儲存體載入資料](load-data-from-azure-blob-storage-using-polybase.md)。

## <a name="1-extract-the-source-data-into-text-files"></a>1.將來源資料擷取至文字檔

從來源系統取得資料視儲存位置而定。 目標是將資料移至支援的分隔文字或 CSV 檔案。

### <a name="supported-file-formats"></a>支援的檔案格式

使用 PolyBase 和 COPY 陳述式，您可以從 UTF-8 和 UTF-16 編碼的分隔符號文字檔或 CSV 檔案載入資料。 除了分隔符號文字檔或 CSV 檔案，會從 Hadoop 檔案格式 (例如 ORC 和 Parquet) 載入。 PolyBase 和 COPY 陳述式也可以從 Gzip 和 Snappy 壓縮檔案載入資料。

不支援延伸的 ASCII、固定寬度格式和巢狀格式，例如 WinZip 或 XML。 如果您從 SQL Server 匯出，您可以使用 [bcp 命令列工具](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)將資料匯出到分隔符號文字檔。

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2.讓資料登陸到 Azure Blob 儲存體或 Azure Data Lake Store

若要讓資料登陸到 Azure 儲存體，您可以將其移至 [Azure Blob 儲存體](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)或 [Azure Data Lake Store Gen2](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。 在任一位置中，資料應該會儲存到文字檔。 PolyBase 和 COPY 陳述式可以從任一位置載入。

您可以用來將資料移至 Azure 儲存體的工具和服務：

- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 服務會增強網路輸送量、效能及可預測性。 ExpressRoute 是一項服務，它會透過專用私人連線將您的資料路由傳送至 Azure。 ExpressRoute 連線不會透過公用網際網路路由傳送資料。 相較於透過公用網際網路的一般連線，這個連線提供更為可靠、速度更快、延遲更低且安全性更高的網際網路連線。
- [AZCopy 公用程式](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)透過公用網際網路將資料移至 Azure 儲存體。 如果您的資料大小小於 10 TB，就適用這個選項。 若要使用 AZCopy 定期執行載入，請測試網路速度以查看是否可以接受。
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 具有閘道，您可以在本機伺服器上安裝。 然後您可以建立管線，將資料從本機伺服器移至 Azure 儲存體。 若要搭配 SQL 集區使用 Data Factory，請參閱[針對 SQL 集區載入資料](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。

## <a name="3-prepare-the-data-for-loading"></a>3.準備要載入的資料

在載入之前，您可能需要在儲存體帳戶中準備及清除資料。 資料準備可以在您的資料是在來源中、當您將資料匯出到文字檔時，或是在資料在 Azure 儲存體之後執行。  盡可能儘早在程序中使用資料最簡單。  

### <a name="define-the-tables"></a>定義資料表

當您使用 COPY 語句時，必須先定義您要在 SQL 集區中載入的資料表 (s) 。

如果您使用 PolyBase，您必須先在 SQL 集區中定義外部資料表，然後再載入。 PolyBase 使用外部資料表以定義及存取 Azure 儲存體中的資料。 外部資料表類似於資料表檢視。 外部資料表包含資料表結構描述，並指向儲存在 SQL 集區外部的資料。

定義外部資料表牽涉到指定資料來源、文字檔格式和資料表定義。 您需要的 T-SQL 語法參考文章如下：

- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

載入 Parquet 檔案時，請使用下列 SQL 資料類型對應：

|                         Parquet 類型                         |   Parquet 邏輯類型 (註釋)   |  SQL 資料類型   |
| :----------------------------------------------------------: | :-----------------------------------: | :--------------: |
|                           BOOLEAN                            |                                       |       bit        |
|                     BINARY/BYTE_ARRAY                      |                                       |    varbinary     |
|                            DOUBLE                            |                                       |      FLOAT       |
|                            FLOAT                             |                                       |       real       |
|                            INT32                             |                                       |       int        |
|                            INT64                             |                                       |      BIGINT      |
|                            INT96                             |                                       |    datetime2     |
|                     FIXED_LEN_BYTE_ARRAY                     |                                       |      BINARY      |
|                            BINARY                            |                 UTF8                  |     NVARCHAR     |
|                            BINARY                            |                STRING                 |     NVARCHAR     |
|                            BINARY                            |                 ENUM                  |     NVARCHAR     |
|                            BINARY                            |                 UUID                  | UNIQUEIDENTIFIER |
|                            BINARY                            |                DECIMAL                |     decimal      |
|                            BINARY                            |                 JSON                  |  nvarchar(MAX)   |
|                            BINARY                            |                 BSON                  |  varbinary(max)  |
|                     FIXED_LEN_BYTE_ARRAY                     |                DECIMAL                |     decimal      |
|                          BYTE_ARRAY                          |               INTERVAL                |  varchar(max),   |
|                            INT32                             |             INT(8, true)              |     SMALLINT     |
|                            INT32                             |            INT(16, true)            |     SMALLINT     |
|                            INT32                             |             INT(32, true)             |       int        |
|                            INT32                             |            INT(8, false)            |     TINYINT      |
|                            INT32                             |            INT(16, false)             |       int        |
|                            INT32                             |           INT(32, false)            |      BIGINT      |
|                            INT32                             |                 日期                  |       date       |
|                            INT32                             |                DECIMAL                |     decimal      |
|                            INT32                             |            TIME (MILLIS)             |       time       |
|                            INT64                             |            INT(64, true)            |      BIGINT      |
|                            INT64                             |           INT(64, false)            |  decimal(20,0)   |
|                            INT64                             |                DECIMAL                |     decimal      |
|                            INT64                             |         TIME (MICROS / NANOS)         |       time       |
|                            INT64                             | TIMESTAMP (MILLIS / MICROS / NANOS) |    datetime2     |
| [複雜類型](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23lists&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=6Luk047sK26ijTzfvKMYc%2FNu%2Fz0AlLCX8lKKTI%2F8B5o%3D&reserved=0) |                 清單                  |   varchar(max)   |
| [複雜類型](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23maps&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=FiThqXxjgmZBVRyigHzfh5V7Z%2BPZHjud2IkUUM43I7o%3D&reserved=0) |                  MAP                  |   varchar(max)   |



如需建立外部物件的範例，請參閱 [建立外部資料表](https://docs.microsoft.com/azure/synapse-analytics/sql/develop-tables-external-tables?tabs=sql-pool)。

### <a name="format-text-files"></a>格式化文字檔

如果您使用 PolyBase，定義的外部物件必須對齊文字檔的資料列與外部資料表和檔案格式定義。 文字檔之每個資料列中的資料必須對齊資料表定義。
若要格式化文字檔：

- 如果您的資料是來自非關聯式來源，您必須將它轉換成資料列和資料行。 無論資料是來自關聯式或非關聯式來源，資料都必須轉換以對齊您打算將資料載入其中之資料表的資料行定義。
- 格式化文字檔中的資料，以對齊目的地資料表中的資料行和資料類型。 如果外部文字檔與 SQL 集區資料表的資料類型之間沒有對齊，會導致在載入期間資料列遭到拒絕。
- 使用結束字元分隔文字檔中的欄位。  請務必使用在來源資料中找不到的字元或字元序列。 搭配 [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 使用您指定的結束字元。

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4.使用 PolyBase 或 COPY 陳述式載入資料

這是將資料載入暫存資料表的最佳做法。 暫存資料表可讓您處理錯誤，而不會干擾生產資料表。 臨時表也可讓您在將資料插入生產資料表之前，先使用 SQL 集區平行處理架構進行資料轉換。

### <a name="options-for-loading"></a>載入的選項

若要載入資料，您可以使用下列任何一種載入選項：

- [COPY 語句](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)是建議的載入公用程式，因為它可讓您順暢且靈活地載入資料。 此語句有許多 PolyBase 未提供的額外載入功能。 
- [PolyBase 與 t-sql](load-data-from-azure-blob-storage-using-polybase.md) 需要您定義外部資料物件。
- [搭配使用 PolyBase 和 COPY 陳述式與 Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 是另一個協調工具。  它會定義管線並排程作業。
- 當您的來源資料位於 SQL Server 時[，具有 SSIS 的 PolyBase](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)會正常運作。 SSIS 會定義來源至目的地資料表對應，也會協調載入。 如果您已經有 SSIS 套件，您可以將套件修改為搭配新的資料倉儲目的地。
- [搭配使用 PolyBase 與 Azure DataBricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)，可以使用 PolyBase 將資料從資料表移轉到 Databricks 資料框架和/或將資料從 Databricks 資料框架寫入至資料表。

### <a name="other-loading-options"></a>其他載入選項

除了 PolyBase 和 COPY 陳述式以外，您還可以使用 [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 或 [SqlBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。 bcp 會直接載入至資料庫而不需要透過 Azure Blob 儲存體，僅適用於小型載入。

> [!NOTE]
> 這些選項的載入效能會顯著低於 PolyBase 和 COPY 陳述式。

## <a name="5-transform-the-data"></a>5.轉換資料

當資料在暫存表格時，執行您的工作負載需要的轉換。 然後將資料移至生產資料表中。

## <a name="6-insert-the-data-into-production-tables"></a>6.將資料插入生產資料表

INSERT INTO ...SELECT 陳述式會從暫存表格將資料移至永久資料表。

當您設計 ETL 程序時，嘗試在小型測試範例上執行程序。 嘗試從資料表將 1000 個資料列擷取至檔案，將它移至 Azure，然後嘗試將它載入暫存表格。

## <a name="partner-loading-solutions"></a>合作夥伴載入解決方案

我們有許多合作夥伴皆提供載入解決方案。 若要深入了解，請參閱我們的[解決方案合作夥伴](sql-data-warehouse-partner-business-intelligence.md)清單。

## <a name="next-steps"></a>後續步驟

如需載入指引，請參閱[載入資料的指引](guidance-for-loading-data.md)。
