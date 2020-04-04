---
title: 使用使用者定義的架構
description: 使用 T-SQL 使用者定義的架構在 Synapse SQL 池中開發解決方案的提示。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7144fa75d156ca7aed9d8215592f89c167cfb221
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633460"
---
# <a name="user-defined-schemas-in-synapse-sql-pool"></a>Synapse SQL 池中使用者定義的架構
本文重點介紹使用 T-SQL 使用者定義的架構在 Synapse SQL 池中開發解決方案的一些提示。

## <a name="schemas-for-application-boundaries"></a>應用程式界限的結構描述

傳統資料倉儲通常使用不同的資料庫，根據工作負載、網域或安全性來建立應用程式界限。 

例如,傳統的 SQL Server 數據倉庫可能包括暫存資料庫、數據倉庫資料庫和某些數據集市資料庫。 在此拓撲中,每個資料庫在體系結構中作為工作負載和安全邊界運行。

相比之下,SQL 池在一個資料庫中運行整個數據倉庫工作負載。 不允許跨資料庫聯接。 SQL 池希望倉庫使用的所有表都存儲在一個資料庫中。

> [!NOTE]
> SQL 池不支援任何類型的跨資料庫查詢。 因此，需要修改運用此模式的資料倉儲實作。
> 
> 

## <a name="recommendations"></a>建議
以下是使用使用者定義的架構整合工作負載、安全性、域和功能邊界的建議:

- 使用一個 SQL 池資料庫運行整個數據倉庫工作負荷。
- 整合現有數據倉庫環境以使用一個 SQL 池資料庫。
- 運用 **使用者定義的結構描述** 來提供先前使用資料庫實作的界限。

如果以前未使用使用者定義的架構,則您有一個乾淨的石板。 使用舊資料庫名稱作為 SQL 池資料庫中使用者定義的架構的基礎。

如果架構已被使用,則有幾個選項:

- 刪除舊架構名稱並重新開始。
- 通過將舊架構名稱預掛起到表名稱來保留舊架構名稱。
- 在額外結構描述中的資料表上實作檢視來重建舊的結構描述結構，以保留舊版結構描述名稱。

> [!NOTE]
> 在第一次檢查時，選項 3 似乎像是最吸引人的選項。 不過，魔鬼就在細節裡。 視圖僅在 SQL 池中讀取。 必須對基底資料表執行任何的資料表或資料修改。 選項 3 還在您的系統中引進了一個檢視層。 如果您已在架構中使用檢視，您可以再仔細思考一下這個選項。
> 
> 

### <a name="examples"></a>範例：
基於資料庫名稱實現使用者定義的架構:

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

通過預先掛起到表名稱來保留舊架構名稱。 對工作負載邊界使用架構:

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

使用檢視保留舊架構名稱:

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
> 結構描述策略如有任何變更，則需要檢閱資料庫的資訊安全模型。 在許多情況下,您可以通過在架構級別分配許可權來簡化安全模型。 如果需要更精細的許可權,則可以使用資料庫角色。
> 
> 

## <a name="next-steps"></a>後續步驟
如需更多開發秘訣，請參閱[開發概觀](sql-data-warehouse-overview-develop.md)。

