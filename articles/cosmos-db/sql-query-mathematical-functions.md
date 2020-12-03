---
title: Azure Cosmos DB 查詢語言的數學函數
description: 瞭解 Azure Cosmos DB 中的數學函式，以根據提供作為引數的輸入值來執行計算，並傳回數值。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 63d349c8cfff52932d51ce7143aba33521c43890
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96549179"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>數學函式 (Azure Cosmos DB)   
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

每個數學函式都會執行計算，通常以提供來作為引數的輸入值為基礎，並傳回數值。

您可以執行如下列範例所示的查詢：

```sql
    SELECT VALUE ABS(-4)
```

結果如下：

```json
    [4]
```

## <a name="functions"></a>函式

下列支援的內建數學函數會執行計算（通常是根據輸入引數），並傳回數值運算式：
 
* [ABS](sql-query-abs.md)
* [ACOS](sql-query-acos.md)
* [ASIN](sql-query-asin.md)
* [ATAN](sql-query-atan.md)
* [ATN2](sql-query-atn2.md)
* [CEILING](sql-query-ceiling.md)
* [COS](sql-query-cos.md)
* [COT](sql-query-cot.md)
* [DEGREES](sql-query-degrees.md)
* [EXP](sql-query-exp.md)
* [FLOOR](sql-query-floor.md)
* [LOG](sql-query-log.md)
* [LOG10](sql-query-log10.md)
* [PI](sql-query-pi.md)
* [POWER](sql-query-power.md)
* [RADIANS](sql-query-radians.md)
* [RAND](sql-query-rand.md)
* [ROUND](sql-query-round.md)
* [SIGN](sql-query-sign.md)
* [SIN](sql-query-sin.md)
* [SQRT](sql-query-sqrt.md)
* [SQUARE](sql-query-square.md)
* [TAN](sql-query-tan.md)
* [TRUNC](sql-query-trunc.md)

  
除了 RAND，所有數學函數都是具決定性的函數。 這表示每次利用一組特定輸入值來呼叫它們時，都會傳回相同的結果。

## <a name="next-steps"></a>後續步驟

- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
- [使用者定義的函式](sql-query-udfs.md)
- [彙總](sql-query-aggregate-functions.md)
