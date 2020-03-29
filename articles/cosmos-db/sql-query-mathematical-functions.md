---
title: Azure 宇宙 DB 查詢語言中的數學函數
description: 瞭解 Azure Cosmos DB 中的數學函數，以便根據作為參數提供的輸入值執行計算，並返回數值。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a04867d356aaf2f55dbe900d2e35b42f74206851
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873262"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>數學函數（Azure 宇宙 DB）  

每個數學函式都會執行計算，通常以提供來作為引數的輸入值為基礎，並傳回數值。

您可以執行查詢，如以下示例：

```sql
    SELECT VALUE ABS(-4)
```

結果如下：

```json
    [4]
```

## <a name="functions"></a>函式

以下支援的內置數學函數執行計算（通常基於輸入參數）並返回數值運算式。
  
||||  
|-|-|-|  
|[Abs](sql-query-abs.md)|[ACOS](sql-query-acos.md)|[阿辛](sql-query-asin.md)|  
|[阿坦](sql-query-atan.md)|[ATN2](sql-query-atn2.md)|[CEILING](sql-query-ceiling.md)|  
|[COS](sql-query-cos.md)|[嬰兒 床](sql-query-cot.md)|[度](sql-query-degrees.md)|  
|[EXP](sql-query-exp.md)|[FLOOR](sql-query-floor.md)|[日誌](sql-query-log.md)|  
|[日誌10](sql-query-log10.md)|[PI](sql-query-pi.md)|[權力](sql-query-power.md)|  
|[RADIANS](sql-query-radians.md)|[RAND](sql-query-rand.md)|[輪](sql-query-round.md)|
|[標誌](sql-query-sign.md)|[罪](sql-query-sin.md)|[SQRT](sql-query-sqrt.md)|
|[廣場](sql-query-square.md)|[潭](sql-query-tan.md)|[TRUNC](sql-query-trunc.md)||  
  
除了 RAND，所有數學函數都是具決定性的函數。 這表示每次利用一組特定輸入值來呼叫它們時，都會傳回相同的結果。

## <a name="next-steps"></a>後續步驟

- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
- [使用者定義的函式](sql-query-udfs.md)
- [彙總](sql-query-aggregates.md)
