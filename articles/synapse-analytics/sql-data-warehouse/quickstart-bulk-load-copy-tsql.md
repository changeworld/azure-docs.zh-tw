---
title: 快速入門：使用單一 T-SQL 陳述式大量載入資料
description: 使用 COPY 陳述式大量載入資料
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/08/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: d39b3085a802ca0ff745ab1f63f4a8fba966ea48
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115010"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>快速入門：使用 COPY 陳述式大量載入資料

在本快速入門中，您會使用簡單且彈性的 [COPY 陳述式](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)，將資料大量載入至 SQL 集區，以進行高輸送量的資料內嵌。 COPY 陳述式是建議的載入公用程式，因為其可讓您透過提供下列功能，順暢且靈活地載入資料：

- 允許較低權限的使用者載入，無需對資料倉儲設定嚴格的控制權限
- 只利用單一 T-SQL 陳述式，無需建立任何額外的資料庫物件
- 在不使用共用存取簽章 (SAS) 公開儲存體帳戶金鑰的情況下，利用更精細的權限模型
- 為 ERRORFILE 位置 (REJECTED_ROW_LOCATION) 指定不同的儲存體帳戶
- 自訂每個目標資料行的預設值，並指定要載入特定目標資料行的來源資料欄位
- 指定 CSV 檔案的自訂資料列結束字元
- 為 CSV 檔案逸出字串、欄位和資料列分隔符號
- 利用 CSV 檔案的 SQL Server 日期格式
- 在儲存體位置路徑中指定萬用字元和多個檔案

## <a name="prerequisites"></a>Prerequisites

本快速入門假設您已有 SQL 集區。 如果尚未建立 SQL 集區，請使用[建立和連線 - 入口網站](create-data-warehouse-portal.md)快速入門。

## <a name="create-the-target-table"></a>建立目標資料表

在此範例中，我們將從紐約計程車資料集載入資料。 我們會載入稱為「行程」的資料表，代表計程車在一年內所進行的行程。 執行下列項目以建立資料表：

```sql
CREATE TABLE [dbo].[Trip]
(
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
);
```

## <a name="run-the-copy-statement"></a>執行 COPY 陳述式

執行下列 COPY 陳述式，將資料從 Azure Blob 儲存體帳戶載入至「行程」資料表。

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>監視載入

定期執行下列查詢，以檢查您的載入是否正在進行中：

```sql
SELECT  r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command
,       sum(bytes_processed) AS bytes_processed
,       sum(rows_processed) AS rows_processed
FROM    sys.dm_pdw_exec_requests r
              JOIN sys.dm_pdw_dms_workers w
                     ON r.[request_id] = w.request_id
WHERE [label] = 'COPY: dbo.trip' and session_id <> session_id() and type = 'WRITER'
GROUP BY r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command;

```

## <a name="next-steps"></a>後續步驟

- 如需載入資料的最佳做法，請參閱[載入資料的最佳做法](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/guidance-for-loading-data)。
- 如需如何管理資料載入資源的相關資訊，請參閱[工作負載隔離](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-configure-workload-isolation-tsql)。 
