---
title: 使用使用者定義的架構
description: 使用 T-sql 使用者定義架構在 Synapse SQL 集區中開發解決方案的秘訣。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: fc5e035215e7cabd02861c6ee2498cadd1ef0534
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85213358"
---
# <a name="user-defined-schemas-in-synapse-sql-pool"></a>Synapse SQL 集區中的使用者定義架構
本文著重于提供使用 T-sql 使用者定義架構在 Synapse SQL 集區中開發解決方案的數個秘訣。

## <a name="schemas-for-application-boundaries"></a>應用程式界限的結構描述

傳統資料倉儲通常使用不同的資料庫，根據工作負載、網域或安全性來建立應用程式界限。 

例如，傳統的 SQL Server 資料倉儲可能包含臨時資料庫、資料倉儲資料庫和某些資料超市資料庫。 在此拓撲中，每個資料庫在架構中會以工作負載和安全性界限的形式運作。

相反地，SQL 集區會在一個資料庫內執行整個資料倉儲工作負載。 不允許跨資料庫聯結。 SQL 集區預期倉儲所使用的所有資料表都會儲存在一個資料庫中。

> [!NOTE]
> SQL 集區不支援任何種類的跨資料庫查詢。 因此，需要修改運用此模式的資料倉儲實作。
> 
> 

## <a name="recommendations"></a>建議
以下是使用使用者定義的架構來合併工作負載、安全性、網域和功能界限的建議：

- 使用一個 SQL 集區資料庫來執行整個資料倉儲工作負載。
- 將您現有的資料倉儲環境合併，以使用一個 SQL 集區資料庫。
- 運用 **使用者定義的結構描述** 來提供先前使用資料庫實作的界限。

如果之前未使用過使用者定義的架構，您就會有一個全新的平板。 使用舊的資料庫名稱做為 SQL 集區資料庫中的使用者定義架構的基礎。

如果已經使用架構，則您有幾個選項：

- 移除舊版架構名稱，然後重新開始。
- 將舊版架構名稱預先擱置為數據表名稱，以保留舊版架構名稱。
- 在額外結構描述中的資料表上實作檢視來重建舊的結構描述結構，以保留舊版結構描述名稱。

> [!NOTE]
> 在第一次檢查時，選項 3 似乎像是最吸引人的選項。 不過，魔鬼就在細節裡。 Views 在 SQL 集區中是唯讀的。 必須對基底資料表執行任何的資料表或資料修改。 選項 3 還在您的系統中引進了一個檢視層。 如果您已在架構中使用檢視，您可以再仔細思考一下這個選項。
> 
> 

### <a name="examples"></a>範例：
根據資料庫名稱來執行使用者定義的架構：

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

將舊版架構名稱預先暫止至資料表名稱，以保留舊版架構名稱。 使用工作負載界限的架構：

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

使用 views 保留舊版架構名稱：

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> 結構描述策略如有任何變更，則需要檢閱資料庫的資訊安全模型。 在許多情況下，您可能可以在架構層級指派許可權，以簡化安全性模型。 如果需要更細微的許可權，您可以使用資料庫角色。
> 
> 

## <a name="next-steps"></a>後續步驟
如需更多開發秘訣，請參閱[開發概觀](sql-data-warehouse-overview-develop.md)。

