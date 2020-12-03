---
title: Azure Cosmos DB 查詢語言中的類型檢查函式
description: 瞭解如何在 Azure Cosmos DB 中檢查 SQL 系統函數的類型。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2becc9216d847dfe26d8fd3a433993112fff7980
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546342"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>類型檢查函式 (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

類型檢查函數可讓您檢查 SQL 查詢中的運算式類型。 您可以使用類型檢查函式，在專案為變數或未知時，即時判斷專案中的屬性類型。 

## <a name="functions"></a>函式

以下是支援的內建類型檢查功能資料表：

下列函式支援針對輸入值檢查類型，並且會傳回布林值。  

* [IS_ARRAY](sql-query-is-array.md)
* [IS_BOOL](sql-query-is-bool.md)
* [IS_DEFINED](sql-query-is-defined.md)
* [IS_NULL](sql-query-is-null.md)
* [IS_NUMBER](sql-query-is-number.md)
* [IS_OBJECT](sql-query-is-object.md)
* [IS_PRIMITIVE](sql-query-is-primitive.md)
* [IS_STRING](sql-query-is-string.md)

## <a name="next-steps"></a>後續步驟

- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
- [使用者定義的函式](sql-query-udfs.md)
- [彙總](sql-query-aggregate-functions.md)
