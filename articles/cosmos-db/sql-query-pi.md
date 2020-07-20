---
title: Azure Cosmos DB 查詢語言中的 PI
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數 PI。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27832008e8922e339a648985192a58b111555bc9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "71349662"
---
# <a name="pi-azure-cosmos-db"></a>PI （Azure Cosmos DB）
 傳回 PI 的常數值。  
  
## <a name="syntax"></a>Syntax
  
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
