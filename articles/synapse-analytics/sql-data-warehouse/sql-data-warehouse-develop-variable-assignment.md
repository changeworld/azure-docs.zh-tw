---
title: 指派變數
description: 在本文中，您將找到在 Azure Synapse Analytics 中為專用的 SQL 集區指派 T-sql 變數的重要秘訣。
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 87448ea737c11af13a52632e5bf4f67dc54d9ae3
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459234"
---
# <a name="assign-variables-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中為專用的 SQL 集區指派變數

在本文中，您將找到在專用 SQL 集區中指派 T-sql 變數的重要秘訣。

## <a name="set-variables-with-declare"></a>使用 DECLARE 設定變數

專用 SQL 集區中的變數是使用 `DECLARE` 語句或語句來設定 `SET` 。 使用 DECLARE 初始化變數是在 SQL 集區中設定變數值最具彈性的方式之一。

```sql
DECLARE @v  int = 0
;
```

您也可以使用 DECLARE，一次設定一個以上的變數。 您無法使用 [選取或更新] 執行下列動作：

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

您無法在相同的 DECLARE 語句中初始化和使用變數。 為了說明這點，**不** 允許下列範例，因為 @p1 已在相同的 DECLARE 陳述式中初始化和使用。 因此，下列範例會提供錯誤：

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>使用 SET 設定值

SET 是設定單一變數時常見的方法。

下列陳述式是使用 SET 設定變數所有的有效方法：

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

您一次只能使用 SET 設定一個變數。 不過，允許複合運算子。

## <a name="limitations"></a>限制

您無法使用 UPDATE 進行變數指派。

## <a name="next-steps"></a>後續步驟

如需更多開發秘訣，請參閱[開發概觀](sql-data-warehouse-overview-develop.md)。
