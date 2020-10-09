---
title: Azure Cosmos DB 查詢語言中的 StringToNull
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數 StringToNull。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7e17547f88465103b61eabec04978ea806ffa2c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "78296434"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull (Azure Cosmos DB) 
 傳回轉換為 null 的運算式。 如果無法轉譯運算式，則會傳回 undefined。  
  
## <a name="syntax"></a>語法
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   這是要剖析為 null 運算式的字串運算式。
  
## <a name="return-types"></a>傳回類型
  
  傳回 null 運算式或未定義。  
  
## <a name="examples"></a>範例
  
  下列範例顯示 `StringToNull` 不同類型的行為。 

以下是具有有效輸入的範例。

 空白字元只允許在 "null" 之前或之後。

```sql
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 以下為結果集。  
  
```json
[{"n1": null, "n2": null, "n3": true}]
```  

以下是無效輸入的範例。

Null 會區分大小寫，且必須以所有小寫字元（亦即 "null"）撰寫。

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 以下為結果集。  
  
```json
[{}]
```  

傳遞的運算式將會剖析為 null 運算式;這些輸入不會評估為類型 null，因此會傳回 undefined。

```sql
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
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
