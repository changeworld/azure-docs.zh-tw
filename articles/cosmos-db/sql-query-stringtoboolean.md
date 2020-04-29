---
title: Azure Cosmos DB 查詢語言中的 StringToBoolean
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數 StringToBoolean。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ce11db91eff51e669f0917fbf34b1d560d0e9f07
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78296536"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean （Azure Cosmos DB）
 傳回轉譯成布林值的運算式。 如果無法轉譯運算式，則會傳回 undefined。  
  
## <a name="syntax"></a>語法
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   這是要剖析為布林運算式的字串運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回布林運算式或未定義。  
  
## <a name="examples"></a>範例
  
  下列範例顯示不同類型`StringToBoolean`的行為。 
 
 以下是具有有效輸入的範例。

只有在 "true"/"false" 之前或之後才允許空白字元。

```sql
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 以下為結果集。  
  
```json
[{"b1": true, "b2": false, "b3": false}]
```  

以下是具有無效輸入的範例。

 布林值區分大小寫，而且必須寫成所有小寫字元，亦即 "true" 和 "false"。

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

以下為結果集。  
  
```json
[{}]
``` 

傳遞的運算式會剖析為布林運算式;這些輸入不會評估為布林類型，因此會傳回 undefined。

```sql
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

以下為結果集。  
  
```json
[{}]
```  

## <a name="remarks"></a>備註

這個系統函數不會使用索引。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
