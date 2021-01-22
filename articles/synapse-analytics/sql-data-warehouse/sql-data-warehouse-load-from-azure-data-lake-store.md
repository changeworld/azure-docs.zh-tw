---
title: 教學課程從 Azure Data Lake Storage 載入資料
description: 使用 COPY 語句，從適用于專用 SQL 集區的 Azure Data Lake Storage 載入資料。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 0974e880b75cce69f2b5ac82e3c4b39de53e03ce
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98677099"
---
# <a name="load-data-from-azure-data-lake-storage-into-dedicated-sql-pools-in-azure-synapse-analytics"></a>將資料從 Azure Data Lake Storage 載入至 Azure Synapse Analytics 中的專用 SQL 集區

本指南概述如何使用 [COPY 語句](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) ，從 Azure Data Lake Storage 載入資料。 如需有關在所有驗證方法上使用 COPY 語句的快速範例，請造訪下列檔： [使用專用的 SQL 集區安全地載入資料](./quickstart-bulk-load-copy-tsql-examples.md)。

> [!NOTE]  
> 若要在 COPY 語句上提供意見反應或回報問題，請傳送電子郵件至下列通訊群組清單： sqldwcopypreview@service.microsoft.com 。
>
> [!div class="checklist"]
>
> * 建立要從 Azure Data Lake Storage 載入資料的目標資料表。
> * 建立 COPY 語句，以將資料載入資料倉儲。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="before-you-begin"></a>開始之前

開始本教學課程之前，請下載並安裝最新版的 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS)。

若要執行此教學課程，您需要：

* 專用的 SQL 集區。 請參閱 [建立專用的 SQL 集區和查詢資料](create-data-warehouse-portal.md)。
* Data Lake Storage 帳戶。 請參閱[開始使用 Azure Data Lake Storage](../../data-lake-store/data-lake-store-get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。 針對此儲存體帳戶，您必須設定或指定下列其中一項要載入的認證：儲存體帳戶金鑰、共用存取簽章 (SAS) 金鑰、Azure Directory 應用程式使用者，或具有儲存體帳戶適當 Azure 角色的 AAD 使用者。

## <a name="create-the-target-table"></a>建立目標資料表

連接到您專用的 SQL 集區，並建立您將載入的目標資料表。 在此範例中，我們要建立產品維度資料表。

```sql
-- A: Create the target table
-- DimProduct
CREATE TABLE [dbo].[DimProduct]
(
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    DISTRIBUTION = HASH([ProductKey]),
    CLUSTERED COLUMNSTORE INDEX
    --HEAP
);
```


## <a name="create-the-copy-statement"></a>建立 COPY 語句

連接到您的 SQL 專用集區，並執行 COPY 語句。 如需範例的完整清單，請參閱下列檔： [使用專用的 SQL 集區安全地載入資料](./quickstart-bulk-load-copy-tsql-examples.md)。

```sql
-- B: Create and execute the COPY statement

COPY INTO [dbo].[DimProduct] 
--The column list allows you map, omit, or reorder input file columns to target table columns. 
--You can also specify the default value when there is a NULL value in the file.
--When the column list is not specified, columns will be mapped based on source and target ordinality
(
    ProductKey default -1 1,
    ProductLabel default 'myStringDefaultWhenNull' 2,
    ProductName default 'myStringDefaultWhenNull' 3
)
--The storage account location where you data is staged
FROM 'https://storageaccount.blob.core.windows.net/container/directory/'
WITH 
(
   --CREDENTIAL: Specifies the authentication method and credential access your storage account
   CREDENTIAL = (IDENTITY = '', SECRET = ''),
   --FILE_TYPE: Specifies the file type in your storage account location
   FILE_TYPE = 'CSV',
   --FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text (CSV) file
   FIELDTERMINATOR = '|',
   --ROWTERMINATOR: Marks the end of a record in the file
   ROWTERMINATOR = '0x0A',
   --FIELDQUOTE: Specifies the delimiter for data of type string in a delimited text (CSV) file
   FIELDQUOTE = '',
   ENCODING = 'UTF8',
   DATEFORMAT = 'ymd',
   --MAXERRORS: Maximum number of reject rows allowed in the load before the COPY operation is canceled
   MAXERRORS = 10,
   --ERRORFILE: Specifies the directory where the rejected rows and the corresponding error reason should be written
   ERRORFILE = '/errorsfolder',
) OPTION (LABEL = 'COPY: ADLS tutorial');
```

## <a name="optimize-columnstore-compression"></a>最佳化資料行存放區壓縮

根據預設，資料表會定義為叢集資料行存放區索引。 載入完成後，某些資料列可能不會被壓縮為資料行存放區。  有許多原因會導致發生此情況。 若要深入了解，請參閱[管理資料行存放區索引](sql-data-warehouse-tables-index.md)。

若要最佳化載入後的查詢效能和資料行存放區壓縮，請重建資料表以強制資料行存放區索引對所有資料列進行壓縮。

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>最佳化統計資料

您最好在載入後立刻建立單一資料行統計資料。 針對統計資料，您將會有一些選項。 例如，如果您在每個資料行上建立單一資料行統計資料，可能會需要很長的時間才能重建所有統計資料。 如果您知道某些資料行不會被包含在查詢述詞中，您可以略過為那些資料行建立統計資料。

如果您決定要在每個資料表的每個資料行上建立單一資料行統計資料，便可以使用[統計資料](sql-data-warehouse-tables-statistics.md)一文中的預存程序程式碼範例 `prc_sqldw_create_stats`。

下列範例為建立統計資料的好起點。 它會在維度資料表中的每個資料行上，以及在事實資料表中的每個聯結資料行上建立單一資料行統計資料。 您之後隨時可以將單一或多個資料行統計資料新增到其他事實資料表資料行上。

## <a name="achievement-unlocked"></a>成就解鎖！

您已成功將資料載入到您的資料倉儲。 太棒了！

## <a name="next-steps"></a>後續步驟
載入資料是開發使用 Azure Synapse Analytics 的資料倉儲解決方案第一步。 請參閱我們的開發資源。

> [!div class="nextstepaction"]
> [了解如何開發適用於資料倉儲的資料表](sql-data-warehouse-tables-overview.md)

如需更多載入範例和參考，請參閱下列檔：
- [COPY 語句參考檔](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax)
- [複製每個驗證方法的範例](./quickstart-bulk-load-copy-tsql-examples.md)
- [複製單一資料表的快速入門](./quickstart-bulk-load-copy-tsql.md)