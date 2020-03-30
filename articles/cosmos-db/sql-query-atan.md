---
title: Azure 宇宙 DB 查詢語言中的 ATAN
description: 瞭解 Azure Cosmos DB 中的 Arctangent （ATAN ） SQL 系統函數如何以弧度返回角度，其切線是指定的數位運算式
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 899c94a939be7825dca82522eab235bde9252896
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302673"
---
# <a name="atan-azure-cosmos-db"></a>ATAN（Azure 宇宙資料庫）
 傳回角度，以弧度為單位，其正切函數是指定的數值運算式。 這也稱為反正切函數 (Arctangent)。  
  
## <a name="syntax"></a>語法
  
```sql
ATAN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expr*  
   為數值運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="examples"></a>範例
  
  下面的示例返回`ATAN`指定值的 。  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 以下為結果集。  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  
## <a name="remarks"></a>備註

此系統功能不會利用索引。

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure 宇宙 DB](sql-query-mathematical-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
