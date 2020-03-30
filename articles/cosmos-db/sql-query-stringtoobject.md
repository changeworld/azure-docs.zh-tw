---
title: Azure 宇宙 DB 查詢語言中的 StringToObject
description: 瞭解 Azure 宇宙 DB 中的 SQL 系統函數 StringtoObject。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c3e61d1efe20910d84ef4ff583d74982b3ea9f3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296376"
---
# <a name="stringtoobject-azure-cosmos-db"></a>字串物件（Azure 宇宙資料庫）
 返回轉換為物件的運算式。 如果無法轉換運算式，則返回未定義。  
  
## <a name="syntax"></a>語法
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   是要作為 JSON 物件運算式解析的字串運算式。 請注意，嵌套字串值必須用雙引號寫入才能有效。 有關 JSON 格式的詳細資訊，請參閱[json.org](https://json.org/)  
  
## <a name="return-types"></a>傳回類型
  
  返回物件運算式或未定義。  
  
## <a name="examples"></a>範例
  
  下面的示例顯示了不同類型`StringToObject`之間的表現。 
  
 以下是具有有效輸入的示例。

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

 以下是輸入不正確示例。
即使它們在查詢中有效，它們也不會解析為有效物件。 物件字串中的字串\\必須轉義\\"\""："str""""""""""""""""""""""str"，\\\\或者周圍的引號必須為單個"\"a"："str"*。

圍繞屬性名稱的單個引號無效。JSON。

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

以下為結果集。

```json
[{}]
```  

沒有周圍引號的屬性名稱無效。

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

以下為結果集。

```json
[{}]
``` 

以下是輸入不正確示例。

 傳遞的運算式將作為 JSON 物件解析;因此，將解析為 JSON 物件。這些輸入不計算以鍵入物件，因此返回未定義。

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

此系統功能不會利用索引。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure 宇宙 DB](sql-query-string-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
