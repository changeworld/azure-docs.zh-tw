---
title: 使用 Synapse SQL 分配變數
description: 在本文中,您將找到使用 Synapse SQL 分配 T-SQL 變數的提示。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: b2a596b71ee7e5f58e01d5bc10b330f6f54a69d2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428663"
---
# <a name="assigning-variables-with-synapse-sql"></a>使用 Synapse SQL 分配變數

在本文中,您將找到使用 Synapse SQL 分配 T-SQL 變數的提示。

## <a name="setting-variables-with-declare"></a>使用 DECLARE 設定宣告

Synapse SQL 中的變數`DECLARE`使用 語`SET`句或 語句進行設置。 使用 DECLARE 初始化變數是在 Synapse SQL 中設置變數值的最靈活方法之一。

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

不能在同一 DECLARE 語句中初始化和使用變數。 為了說明這一點,不允許使用以下範例,*@p1*因為在同一 DECLARE 語句中同時初始化和使用。 下列範例會顯示錯誤。

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>使用 SET 設定值

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

有關更多開發提示,請參閱[Synapse SQL 開發概述](develop-overview.md)一文。