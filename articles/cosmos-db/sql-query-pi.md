---
title: Azure Cosmos DB 查詢語言中的 PI
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數 PI。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27832008e8922e339a648985192a58b111555bc9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "71349662"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Cosmos DB) 
 傳回 PI 的常數值。  
  
## <a name="syntax"></a>語法
  
```sql
PI ()  
```  
   
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會傳回 `PI` 的值。  
  
```sql
SELECT PI() AS pi 
```  
  
 以下為結果集。  
  
```json
[{"pi": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
