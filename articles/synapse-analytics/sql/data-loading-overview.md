---
title: 而不是 ETL,為突觸 SQL 池設計 ELT |微軟文件
description: 設計用於載入資料或 SQL 池的提取、載入和轉換 (ELT) 過程,而不是 ETL。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 596f4bcf2e3f829430fdc90eb1806a44a84b2bc5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429586"
---
# <a name="designing-a-polybase-data-loading-strategy-for-azure-synapse-sql-pool"></a>為 Azure 突觸 SQL 池設計 PolyBase 資料載入策略

傳統 SMP 資料倉儲會使用擷取、轉換和載入 (ETL) 程序來載入資料。 Azure SQL 池是一種大規模並行處理 (MPP) 體系結構,它利用了計算和存儲資源的可伸縮性和靈活性。 運用擷取、載入及轉換 (ELT) 程序可以利用 MPP，而且不需要載入之前轉換資料的資源。

雖然 SQL 池支援許多載入方法,包括非多邊形選項(如 BCP 和 SQL BulkCopy API),但載入日期最快、最可擴展的方式是透過 PolyBase。  PolyBase 是一種技術，能夠透過 T-SQL 語言存取在 Azure Blob 儲存體或 Azure Data Lake Store 中儲存的外部資料。

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="what-is-elt"></a>什麼是 ELT?

擷取、載入及轉換 (ELT) 是從來源系統擷取資料、載入至資料倉儲再進行轉換的程序。

實現 SQL 池的 PolyBase ELT 的基本步驟包括:

1. 將來源資料擷取至文字檔。
2. 讓資料登陸到 Azure Blob 儲存體或 Azure Data Lake Store。
3. 準備要載入的資料。
4. 使用 PolyBase 將數據載入到 SQL 池暫存表中。
5. 轉換資料。
6. 將資料插入生產資料表。

如需載入教學課程，請參閱[使用 PolyBase 將資料從 Azure Blob 儲存體載入 SQL 資料倉儲中](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

如需詳細資訊，請參閱[載入模式部落格](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)。

## <a name="1-extract-the-source-data-into-text-files"></a>1. 將源資料到文字檔案中

從來源系統取得資料視儲存位置而定。  目標是將資料移至 PolyBase 支援的分隔符號文字檔。

### <a name="polybase-external-file-formats"></a>PolyBase 外部檔案格式

PolyBase 會從 UTF-8 和 UTF-16 編碼分隔符號文字檔載入資料。 除了分隔符號文字檔，它會從 Hadoop 檔案格式 RC 檔案、ORC 和 Parquet 載入。 PolyBase 也可以從 Gzip 和 Snappy 壓縮檔案載入資料。 PolyBase 目前不支援延伸的 ASCII、固定寬度格式和巢狀格式，例如 WinZip、JSON 和 XML。

如果您從 SQL Server 匯出，您可以使用 [bcp 命令列工具](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)將資料匯出到標示分隔符號的文字檔。 Parquet 到 SQL DW 資料類型映射如下:

| **鑲木地板資料類型** |                      **SQL 資料類型**                       |
| :-------------------: | :----------------------------------------------------------: |
|        TINYINT        |                           TINYINT                            |
|       SMALLINT        |                           SMALLINT                           |
|          int          |                             int                              |
|        BIGINT         |                            BIGINT                            |
|        boolean        |                             bit                              |
|        double         |                            FLOAT                             |
|         FLOAT         |                             real                             |
|        double         |                            money                             |
|        double         |                          SMALLMONEY                          |
|        字串         |                            NCHAR                             |
|        字串         |                           NVARCHAR                           |
|        字串         |                             char                             |
|        字串         |                           varchar                            |
|        BINARY         |                            BINARY                            |
|        BINARY         |                          varbinary                           |
|       timestamp       |                             date                             |
|       timestamp       |                        smalldatetime                         |
|       timestamp       |                          datetime2                           |
|       timestamp       |                           Datetime                           |
|       timestamp       |                             time                             |
|       date            |                             date                             |
|        decimal        |                            decimal                           |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. 將資料放入 Azure Blob 儲存或 Azure 資料湖儲存

若要讓資料登陸到 Azure 儲存體，您可以將它移至 [Azure Blob 儲存體](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)或 [Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。 在任一位置中，資料應該會儲存到文字檔。 PolyBase 可以從任一位置載入。

您可以用來將資料移至 Azure 儲存體的工具和服務：

- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) 服務會增強網路輸送量、效能及可預測性。 ExpressRoute 是一項服務，它會透過專用私人連線將您的資料路由傳送至 Azure。 ExpressRoute 連線不會透過公用網際網路路由傳送資料。 相較於透過公用網際網路的一般連線，這個連線提供更為可靠、速度更快、延遲更低且安全性更高的網際網路連線。
- [AZCopy 公用程式](../../storage/common/storage-use-azcopy-v10.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)透過公用網際網路將資料移至 Azure 儲存體。 如果您的資料大小小於 10 TB，就適用這個選項。 若要使用 AZCopy 定期執行載入，請測試網路速度以查看是否可以接受。
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 具有閘道，您可以在本機伺服器上安裝。 然後您可以建立管線，將資料從本機伺服器移至 Azure 儲存體。 要將資料工廠與 SQL 池一起使用,請參閱[將數據載入到 SQL 池](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)中。

## <a name="3-prepare-the-data-for-loading"></a>3. 準備載入資料

在將資料載入 SQL 池之前,您可能需要準備和清理儲存帳戶中的資料。 資料準備可以在您的資料是在來源中、當您將資料匯出到文字檔時，或是在資料在 Azure 儲存體之後執行。  盡可能儘早在程序中使用資料最簡單。  

### <a name="define-external-tables"></a>定義外部資料表

在您可以載入資料之前，您必須在您的資料倉儲中定義外部資料表。 PolyBase 使用外部資料表以定義及存取 Azure 儲存體中的資料。 外部資料表類似於資料表檢視。 外部資料表包含資料表結構描述，並指向儲存在資料倉儲外部的資料。

定義外部資料表牽涉到指定資料來源、文字檔格式和資料表定義。 以下是您需要的 T-SQL 語法主題：

- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [建立外部表](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

如需建立外部物件的範例，請參閱載入教學課程中的[建立外部資料表](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-external-tables-for-the-sample-data)步驟。

### <a name="format-text-files"></a>格式化文字檔

一旦定義外部物件，您必須對齊文字檔的資料列與外部資料表和檔案格式定義。 文字檔之每個資料列中的資料必須對齊資料表定義。
若要格式化文字檔：

- 如果您的資料是來自非關聯式來源，您必須將它轉換成資料列和資料行。 無論資料是來自關聯式或非關聯式來源，資料都必須轉換以對齊您打算將資料載入其中之資料表的資料行定義。
- 設定文字檔案中的數據的格式,以便與 SQL 池目標表中的欄和數據類型對齊。 如果外部文字檔與資料倉儲資料表的的資料類型之間沒有對齊，會導致在載入期間資料列遭到拒絕。
- 使用結束字元分隔文字檔中的欄位。  請務必使用在來源資料中找不到的字元或字元序列。 搭配 [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 使用您指定的結束字元。

## <a name="4-load-the-data-into-sql-pool-staging-tables-using-polybase"></a>4. 使用 PolyBase 將資料載入 SQL 池暫存表中

這是將資料載入暫存資料表的最佳做法。 暫存資料表可讓您處理錯誤，而不會干擾生產資料表。 暫存表還使您能夠在將資料插入生產表中之前使用 SQL 池 MPP 進行數據轉換。

### <a name="options-for-loading-with-polybase"></a>以 PolyBase 載入的選項

若要使用 PolyBase 載入資料，您可以使用任何一種載入選項：

- [PolyBase 與 T-SQL](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 非常適合於當您的資料是在 Azure Blob 儲存體或 Azure Data Lake Store 中的時候。 它給予您對於載入程序最多的控制權，但是也需要您定義外部資料物件。 其他方法會在您將來源資料表對應至目的地資料表時，在幕後定義這些物件。  若要協調 T-SQL 載入，您可以使用 Azure Data Factory、SSIS 或 Azure 函式。
- [PolyBase 與 SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 非常適合於當您的來源資料是在 SQL Server 中的時候，無論是 SQL Server 內部部署或是在雲端。 SSIS 會定義來源至目的地資料表對應，也會協調載入。 如果您已經有 SSIS 套件，您可以將套件修改為搭配新的資料倉儲目的地。
- [PolyBase 與 Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 是另一個協調工具。  它會定義管線並排程作業。
- [具有 Azure 資料磚塊的 PolyBase](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)將數據從 SQL 資料倉儲表傳輸到資料磚塊數據框和/或使用 PolyBase 將數據從資料塊資料幀寫入 SQL 資料倉儲表。

### <a name="non-polybase-loading-options"></a>非 PolyBase 載入選項

如果您的資料與 PolyBase 不相容，您可以使用 [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 或 [SQLBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。 bcp 直接載入 SQL 池而不經過 Azure Blob 儲存,並且僅用於小負載。 請注意，這些選項的載入效能會顯著低於 PolyBase。

## <a name="5-transform-the-data"></a>5. 轉換資料

當資料在暫存表格時，執行您的工作負載需要的轉換。 然後將資料移至生產資料表中。

## <a name="6-insert-the-data-into-production-tables"></a>6. 將資料插入生產表中

插入到 ...SELECT 語句將數據從暫存表移動到永久表。

當您設計 ETL 程序時，嘗試在小型測試範例上執行程序。 嘗試從資料表將 1000 個資料列擷取至檔案，將它移至 Azure，然後嘗試將它載入暫存表格。

## <a name="partner-loading-solutions"></a>合作夥伴載入解決方案

我們有許多合作夥伴皆提供載入解決方案。 若要深入了解，請參閱我們的[解決方案合作夥伴](../sql-data-warehouse/sql-data-warehouse-partner-business-intelligence.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)清單。

## <a name="next-steps"></a>後續步驟

如需載入指引，請參閱[載入資料的指引](data-loading-best-practices.md)。
