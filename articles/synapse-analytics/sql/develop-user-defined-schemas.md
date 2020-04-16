---
title: Synapse SQL 中使用者定義的架構
description: 在以下各節中,您將找到使用 T-SQL 使用者定義的架構使用 Azure 突觸分析的突觸 SQL 功能開發解決方案的各種提示。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: ac4753da1405fe6b8cd209bb4899192e9f317aa1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428702"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Synapse SQL 中使用者定義的架構

在以下各節中,您將找到使用 T-SQL 使用者定義的架構在 Synapse SQL 中開發解決方案的各種提示。

## <a name="schemas-for-application-boundaries"></a>應用程式界限的結構描述

傳統的分析體系結構通常使用單獨的資料庫來根據工作負載、域或安全性創建應用程序邊界。 例如,傳統的 SQL Server 分析基礎結構可能包括暫存資料庫、分析資料庫和數據集市資料庫。 在此拓撲中,每個資料庫在體系結構中作為工作負載和安全邊界運行。

相反,Synapse SQL 在一個資料庫中運行整個分析工作負荷。 不允許跨資料庫聯接。 Synapse SQL 期望倉庫使用的所有表都存儲在一個資料庫中。

> [!NOTE]
> SQL 池不支援任何類型的跨資料庫查詢。 因此,需要修訂利用此模式的分析實現。 SQL 按需(預覽)支援跨資料庫查詢。

## <a name="user-defined-schema-recommendations"></a>使用者定義的架構建議

其中包括使用使用者定義的架構整合工作負載、安全性、域和功能邊界的建議:

- 使用一個資料庫運行整個分析工作負荷。
- 整合現有分析環境以使用一個資料庫。
- 運用 **使用者定義的結構描述** 來提供先前使用資料庫實作的界限。

如果以前未使用使用者定義的架構,則您擁有乾淨的石板。 使用舊資料庫名稱作為 Synapse SQL 資料庫中使用者定義的架構的基礎。

如果架構已被使用,則有幾個選項:

- 移除舊版結構描述名稱並重新開始
- 以將舊架構名稱預先掛到表格名稱來保留舊架構名稱
- 通過在額外的架構中實現對表的視圖來保留舊架構名稱,從而重新創建舊的架構結構。

> [!NOTE]
> 在第一次檢查時,選項 3 可能看起來是最具吸引力的選擇。 視圖僅在 Synapse SQL 中讀取。 必須對基底資料表執行任何的資料表或資料修改。 選項 3 還在您的系統中引進了一個檢視層。 如果您已經在體系結構中使用視圖,則可能需要對此進行額外的考慮。
> 
> 

### <a name="examples"></a>範例

基於資料庫名稱實現使用者定義的架構。

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the analytics
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create analytics tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

通過將舊架構名稱預先掛起到表名稱來保留它們。 使用工作負載界限的結構描述。

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the analytics boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the analytics boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

使用檢視保留舊架構名稱。

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the analytics boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base analytics tables in the analytics boundary
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
> 架構策略的任何更改都需要檢查資料庫的安全模型。 在許多情況下,您可以通過在架構級別分配許可權來簡化安全模型。

如果需要更精細的許可權,則可以使用資料庫角色。 有關資料庫角色的詳細資訊,請參閱[管理資料庫角色和使用者](../../analysis-services/analysis-services-database-users.md)一文。

## <a name="next-steps"></a>後續步驟

有關更多開發提示,請參閱[Synapse SQL 開發概述](develop-overview.md)。
