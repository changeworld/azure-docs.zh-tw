---
title: Azure Cosmos DB 查詢語言中的 StringToNumber
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數 StringToNumber。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5ca8d0c4a6d244823dda6f0f79a3cf5c743a12a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "78296417"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber (Azure Cosmos DB) 
 傳回轉譯成數位的運算式。 如果無法轉譯運算式，則會傳回 undefined。  
  
## <a name="syntax"></a>語法
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   這是要剖析為 JSON 數位運算式的字串運算式。 JSON 中的數位必須是整數或浮點數。 如需 JSON 格式的詳細資訊，請參閱 [json.org](https://json.org/)  
  
## <a name="return-types"></a>傳回類型
  
  傳回數位運算式或未定義。  
  
## <a name="examples"></a>範例
  
  下列範例顯示 `StringToNumber` 不同類型的行為。 

只有在數位之前或之後，才允許使用空白。

```sql
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 以下為結果集。  
  
```json
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

在 JSON 中，有效的數位必須是整數或浮點數。

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 以下為結果集。  
  
```json
{{}}
```  

傳遞的運算式將會剖析為數字運算式;這些輸入不會評估為類型編號，因此會傳回 undefined。 

```sql
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 以下為結果集。  
  
```json
{{}}
```  

## <a name="remarks"></a>備註

這個系統函數將不會使用索引。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
