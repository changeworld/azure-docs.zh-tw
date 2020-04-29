---
title: 使用 Synapse SQL 指派變數
description: 在本文中，您會找到使用 Synapse SQL 指派 T-sql 變數的秘訣。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: b2a596b71ee7e5f58e01d5bc10b330f6f54a69d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81428663"
---
# <a name="assigning-variables-with-synapse-sql"></a>使用 Synapse SQL 指派變數

在本文中，您會找到使用 Synapse SQL 指派 T-sql 變數的秘訣。

## <a name="setting-variables-with-declare"></a>使用 DECLARE 設定宣告

Synapse SQL 中的`DECLARE`變數是使用語句或`SET`語句來設定。 使用 DECLARE 初始化變數是在 Synapse SQL 中設定變數值的其中一個最具彈性的方式。

```sql
DECLARE @v  int = 0
;
```

您也可以使用 DECLARE，一次設定一個以上的變數。 您無法使用 [選取] 或 [更新] 來執行下列動作：

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

您無法在相同的 DECLARE 語句中初始化和使用變數。 為了說明，不允許使用下列範例， *@p1*因為已在相同的 DECLARE 語句中初始化和使用。 下列範例會顯示錯誤。

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

您不能使用 UPDATE 進行變數指派。

## <a name="next-steps"></a>後續步驟

如需更多開發秘訣，請參閱[SYNAPSE SQL 開發簡介](develop-overview.md)一文。