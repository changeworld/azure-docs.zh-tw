---
title: Azure Cosmos DB 查詢語言中的 IS_DEFINED
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數 IS_DEFINED。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a5fcaf52d8e9e6b942a95f0b0c43f3f654c5d5d2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78303846"
---
# <a name="is_defined-azure-cosmos-db"></a>IS_DEFINED （Azure Cosmos DB）
 傳回布林值，表示屬性是否已經指派值。  
  
## <a name="syntax"></a>語法
  
```sql
IS_DEFINED(<expr>)  
```  
  
## <a name="arguments"></a>引數
  
*expr*  
   這是任何運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回布林運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會檢查指定的 JSON 文件內的屬性是否存在。 第一次會傳回 True，因為出現了 "a"，但第二次就會傳回 False 因為 "b" 不存在。  
  
```sql
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 以下為結果集。  
  
```json
[{"isDefined1":true,"isDefined2":false}]  
```  

## <a name="remarks"></a>備註

此系統函數將受益於[範圍索引](index-policy.md#includeexclude-strategy)。

## <a name="next-steps"></a>後續步驟

- [型別檢查函數 Azure Cosmos DB](sql-query-type-checking-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
