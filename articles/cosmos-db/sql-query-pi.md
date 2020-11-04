---
title: Azure Cosmos DB 查詢語言中的 PI
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數 PI。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0ebe94d8b06c342fc61fae4255fa354150974c4f
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341700"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
