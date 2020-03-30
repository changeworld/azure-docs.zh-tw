---
title: Azure Cosmos DB 查詢語言中的類型檢查函數
description: 瞭解 Azure Cosmos DB 中的類型檢查 SQL 系統函數。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cac8be4c39737ca209653532d00375c8c9bee48e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349073"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>類型檢查功能（Azure 宇宙資料庫）

類型檢查功能允許您檢查 SQL 查詢中的運算式類型。 您可以使用類型檢查函數來確定專案內的屬性類型動態，當它們是可變的或未知的。 

## <a name="functions"></a>函式

下面是支援內置類型檢查功能的表：

下列函式支援針對輸入值檢查類型，並且會傳回布林值。  
  
||||  
|-|-|-|  
|[IS_ARRAY](sql-query-is-array.md)|[IS_BOOL](sql-query-is-bool.md)|[IS_DEFINED](sql-query-is-defined.md)|  
|[IS_Null](sql-query-is-null.md)|[IS_NUMBER](sql-query-is-number.md)|[IS_OBJECT](sql-query-is-object.md)|  
|[IS_PRIMITIVE](sql-query-is-primitive.md)|[IS_STRING](sql-query-is-string.md)||  
  

## <a name="next-steps"></a>後續步驟

- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
- [使用者定義的函式](sql-query-udfs.md)
- [彙總](sql-query-aggregates.md)
