---
title: Azure Cosmos DB 查詢語言中的 ARRAY_SLICE
description: 瞭解 Azure Cosmos DB 中的陣列配量 SQL 系統函數如何傳回陣列運算式的一部分
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a98cb17d22f41776ff788d12ced6aa988ad0b10e
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303319"
---
# <a name="array_slice-azure-cosmos-db"></a>ARRAY_SLICE （Azure Cosmos DB）
 傳回陣列運算式的一部分。
  
## <a name="syntax"></a>語法
  
```sql
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
## <a name="arguments"></a>引數
  
*arr_expr*  
   這是任何陣列運算式。  
  
*num_expr*  
   以零為起始的數值索引，可用來開始陣列。 負數值可能會用來指定相對於陣列最後一個元素的起始索引，例如 -1 會參考陣列中的最後一個元素。  

*num_expr*選擇性的數值運算式，可設定產生之陣列中的元素數目上限。    

## <a name="return-types"></a>傳回類型
  
  傳回陣列運算式。  
  
## <a name="examples"></a>範例
  
  下列範例顯示如何使用 `ARRAY_SLICE`取得陣列的不同配量。  
  
```sql
SELECT
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 以下為結果集。  
  
```json
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  

## <a name="remarks"></a>備註

這個系統函數不會使用索引。

## <a name="next-steps"></a>後續步驟

- [陣列函數 Azure Cosmos DB](sql-query-array-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
