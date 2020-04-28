---
title: Azure Cosmos DB 查詢語言的類型檢查函數
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數類型檢查。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cac8be4c39737ca209653532d00375c8c9bee48e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "71349073"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>類型檢查函式（Azure Cosmos DB）

類型檢查函數可讓您檢查 SQL 查詢中的運算式類型。 當專案為變數或未知時，您可以使用類型檢查函式來即時判斷其中的屬性類型。 

## <a name="functions"></a>函式

以下是支援的內建類型檢查函數表：

下列函式支援針對輸入值檢查類型，並且會傳回布林值。  
  
||||  
|-|-|-|  
|[IS_ARRAY](sql-query-is-array.md)|[IS_BOOL](sql-query-is-bool.md)|[IS_DEFINED](sql-query-is-defined.md)|  
|[IS_NULL](sql-query-is-null.md)|[IS_NUMBER](sql-query-is-number.md)|[IS_OBJECT](sql-query-is-object.md)|  
|[IS_PRIMITIVE](sql-query-is-primitive.md)|[IS_STRING](sql-query-is-string.md)||  
  

## <a name="next-steps"></a>後續步驟

- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
- [使用者定義的函式](sql-query-udfs.md)
- [彙總](sql-query-aggregates.md)
