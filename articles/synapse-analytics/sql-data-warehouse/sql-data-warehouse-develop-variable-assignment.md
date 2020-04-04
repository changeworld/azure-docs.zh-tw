---
title: 配置變數
description: 在本文中,您將找到在 SQL 池中分配 T-SQL 變數的基本提示。
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
ms.openlocfilehash: 2dcf706ea59657abc2718a69e59191604dc2849d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633413"
---
# <a name="assign-variables-in-synapse-sql-pool"></a>在 Synapse SQL 池中配置變數

在本文中,您將找到在 SQL 池中分配 T-SQL 變數的基本提示。

## <a name="set-variables-with-declare"></a>使用 DECLARE 設定變數

SQL 池中的變數使用`DECLARE`語句`SET`或 語句進行設置。 使用 DECLARE 初始化變數是在 SQL 池中設置變數值的最靈活方法之一。

```sql
DECLARE @v  int = 0
;
```

您也可以使用 DECLARE，一次設定一個以上的變數。 無法使用 SELECT 或更新執行以下操作:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

不能在同一 DECLARE 語句中初始化和使用變數。 為了說明這點，**不**允許下列範例，因為 @p1 已在相同的 DECLARE 陳述式中初始化和使用。 因此,以下範例給出錯誤:

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

不能將 UPDATE 用於變數分配。

## <a name="next-steps"></a>後續步驟

如需更多開發秘訣，請參閱[開發概觀](sql-data-warehouse-overview-develop.md)。
