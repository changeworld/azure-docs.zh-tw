---
title: Azure 宇宙 DB SQL 查詢中的 Scalar 運算式
description: 瞭解 Azure Cosmos DB 的標量運算式 SQL 語法。 本文還介紹了如何使用運算子將標量運算式合併到複雜運算式中。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: f8c98915ad3b682af00492acc7bc51672ec874a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870729"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Azure 宇宙 DB SQL 查詢中的 Scalar 運算式

[SELECT 子句](sql-query-select.md)支援標量運算式。 純量運算式結合了符號及運算子，可以加以評估以取得單一值。 標量運算式的示例包括：常量、屬性引用、陣列元素引用、別名引用或函式呼叫。 可以使用運算子將 Scalar 運算式組合到複雜的運算式中。

## <a name="syntax"></a>語法
  
```sql  
<scalar_expression> ::=  
       <constant>
     | input_alias
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>
     | <create_array_expression>  
     | (<scalar_expression>)
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```

## <a name="arguments"></a>引數
  
- `<constant>`  
  
   代表常數值。 請參閱＜[常數](sql-query-constants.md)＞一節以取得詳細資料。  
  
- `input_alias`  
  
   代表在 `FROM` 子句中導入，且由 `input_alias` 定義的值。  
  此值不保證為**未定義** – 輸入中的**未定義值**會略過。  
  
- `<scalar_expression>.property_name`  
  
   代表物件屬性值。 如果屬性不存在或屬性在值（該值不是物件）上引用，則運算式將計算為**未定義的**值。  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   表示具有陣列索引`property_name``array_index`的名稱或陣列元素的屬性的值。 若屬性不存在或屬性/陣列索引已在非物件/陣列的值中參考，則運算式會評估為未定義的值。  
  
- `unary_operator <scalar_expression>`  
  
   代表已套用至單一值的運算子。 如需詳細資料，請參閱[運算子](sql-query-operators.md)一節。  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   代表已套用至兩個值的運算子。 如需詳細資料，請參閱[運算子](sql-query-operators.md)一節。  
  
- `<scalar_function_expression>`  
  
   代表由函式呼叫結果定義的值。  
  
- `udf_scalar_function`  
  
   使用者定義純量值函式的名稱。  
  
- `builtin_scalar_function`  
  
   內建純量值函式的名稱。  
  
- `<create_object_expression>`  
  
   代表搭配指定屬性及其值所建立之新物件取得的值。  
  
- `<create_array_expression>`  
  
   代表搭配指定值作為元素所建立之新物件取得的值  
  
- `parameter_name`  
  
   代表指定參數名字的值。 參數名稱必須帶有單一 \@ 作為第一個字元。  
  
## <a name="remarks"></a>備註
  
  調用內置或使用者定義的標量函數時，必須定義所有參數。 若有任何未定義的引數，則不會呼叫函式，同時會產生未定義的結果。  
  
  建立物件時，會略過任何指派未定義值的屬性，且不會納入已建立的物件中。  
  
  建立陣列時，會略過任何指派**未定義**值的元素值，且不會納入已建立的物件中。 這會讓下一個定義的元素以相同方式取代其位置，而建立的陣列將不會具備已略過的索引。  

## <a name="examples"></a>範例

```sql
    SELECT ((2 + 11 % 7)-2)/3
```

結果如下：

```json
    [{
      "$1": 1.33333
    }]
```

在以下查詢中，標量運算式的結果為布林：

```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

結果如下：

```json
    [
      {
        "AreFromSameCityState": false
      },
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="next-steps"></a>後續步驟

- [Azure 宇宙 DB 簡介](introduction.md)
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [子查詢](sql-query-subquery.md)