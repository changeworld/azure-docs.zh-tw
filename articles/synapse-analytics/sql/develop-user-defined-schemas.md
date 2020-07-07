---
title: Synapse SQL 中的使用者定義架構
description: 在下列各節中，您將找到各種使用 T-sql 使用者定義架構來開發解決方案的秘訣，其中包含 Azure Synapse Analytics 的 Synapse SQL 功能。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: ac4753da1405fe6b8cd209bb4899192e9f317aa1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81428702"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Synapse SQL 中的使用者定義架構

在下列各節中，您會發現在 Synapse SQL 中使用 T-sql 使用者定義架構來開發解決方案的各種秘訣。

## <a name="schemas-for-application-boundaries"></a>應用程式界限的結構描述

傳統的分析架構通常會使用不同的資料庫來建立以工作負載、網域或安全性為基礎的應用程式界限。 例如，傳統 SQL Server 分析基礎結構可能包含臨時資料庫、分析資料庫和資料超市資料庫。 在此拓撲中，每個資料庫都是以工作負載和架構中的安全性界限來運作。

相反地，Synapse SQL 會在一個資料庫中執行整個分析工作負載。 不允許跨資料庫聯結。 Synapse SQL 需要倉儲所使用的所有資料表都儲存在一個資料庫中。

> [!NOTE]
> SQL 集區不支援任何種類的跨資料庫查詢。 因此，使用此模式的分析實現將需要修訂。 SQL 隨選（預覽）支援跨資料庫查詢。

## <a name="user-defined-schema-recommendations"></a>使用者定義的架構建議

其中包含使用使用者定義的架構來合併工作負載、安全性、網域和功能界限的建議：

- 使用一個資料庫來執行整個分析工作負載。
- 整合現有的分析環境，以使用一個資料庫。
- 運用 **使用者定義的結構描述** 來提供先前使用資料庫實作的界限。

如果先前未曾用過使用者定義的架構，您就會有乾淨的平板電腦。 使用舊的資料庫名稱做為 Synapse SQL 資料庫中使用者定義架構的基礎。

如果已使用架構，則您有幾個選項：

- 移除舊版結構描述名稱並重新開始
- 將舊版架構名稱預先擱置至資料表名稱，以保留舊版架構名稱
- 藉由在額外的架構中執行資料表的視圖，以重新建立舊的架構結構，以保留舊版架構名稱。

> [!NOTE]
> 第一次檢查時，選項3看起來會像是最吸引人的選擇。 Views 在 Synapse SQL 中是唯讀的。 必須對基底資料表執行任何的資料表或資料修改。 選項 3 還在您的系統中引進了一個檢視層。 如果您已經在架構中使用 views，您可能會想要另外考慮一下。
> 
> 

### <a name="examples"></a>範例

根據資料庫名稱來執行使用者定義的架構。

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

保留舊版架構名稱，方法是將它們預先暫止于資料表名稱。 使用工作負載界限的結構描述。

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

使用 views 保留舊版架構名稱。

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
> 架構策略中的任何變更都需要審查資料庫的安全性模型。 在許多情況下，您可能可以在架構層級指派許可權，以簡化安全性模型。

如果需要更細微的許可權，您可以使用資料庫角色。 如需資料庫角色的詳細資訊，請參閱[管理資料庫角色和使用者](../../analysis-services/analysis-services-database-users.md)一文。

## <a name="next-steps"></a>後續步驟

如需更多開發秘訣，請參閱 [Synapse SQL 開發概觀](develop-overview.md)。
