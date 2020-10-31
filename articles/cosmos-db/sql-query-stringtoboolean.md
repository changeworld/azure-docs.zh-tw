---
title: Azure Cosmos DB 查詢語言中的 StringToBoolean
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數 StringToBoolean。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: bb31aa3ba90c54873586cfba35e668cd31e32572
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099251"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 傳回轉譯為布林值的運算式。 如果無法轉譯運算式，則會傳回 undefined。  
  
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
  
  下列範例顯示 `StringToBoolean` 不同類型的行為。 
 
 以下是具有有效輸入的範例。

只允許在 "true"/"false" 之前或之後使用空白字元。

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

以下是無效輸入的範例。

 布林值會區分大小寫，而且必須以所有小寫字元（亦即 "true" 和 "false"）撰寫。

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

以下為結果集。  
  
```json
[{}]
``` 

傳遞的運算式將會剖析為布林運算式;這些輸入不會評估為類型布林值，因此會傳回 undefined。

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

這個系統函數將不會使用索引。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
