---
title: Azure Cosmos DB 查詢語言中的 StringToObject
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數 StringToObject。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c3e61d1efe20910d84ef4ff583d74982b3ea9f3d
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78296376"
---
# <a name="stringtoobject-azure-cosmos-db"></a>StringToObject （Azure Cosmos DB）
 傳回轉譯成物件的運算式。 如果無法轉譯運算式，則會傳回 undefined。  
  
## <a name="syntax"></a>語法
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   這是要剖析為 JSON 物件運算式的字串運算式。 請注意，嵌套字串值必須以雙引號寫成有效的。 如需 JSON 格式的詳細資訊，請參閱[json.org](https://json.org/)  
  
## <a name="return-types"></a>傳回類型
  
  傳回物件運算式或未定義。  
  
## <a name="examples"></a>範例
  
  下列範例顯示 `StringToObject` 在不同類型之間的行為方式。 
  
 以下是具有有效輸入的範例。

```sql
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

以下為結果集。

```json
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 以下是具有無效輸入的範例。
雖然它們在查詢中是有效的，但它們不會剖析成有效的物件。 物件字串內的字串必須被轉義 "{\\" a\\"：\\" str\\"}"，或周圍的引號必須是單一 ' {"a"： "str"} '。

括住屬性名稱的單引號不是有效的 JSON。

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

以下為結果集。

```json
[{}]
```  

沒有周圍引號的屬性名稱不是有效的 JSON。

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

以下為結果集。

```json
[{}]
``` 

以下是具有無效輸入的範例。

 傳遞的運算式會剖析為 JSON 物件;這些輸入不會評估為 object 類型，因此會傳回 undefined。

```sql
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
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
