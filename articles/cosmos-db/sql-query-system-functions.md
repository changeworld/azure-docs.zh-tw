---
title: Azure Cosmos DB 查詢語言的系統函數
description: 瞭解 Azure Cosmos DB 中內建和使用者定義的 SQL 系統函數。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f41adbb726313ef095084d079dc7852736e0c06
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74870525"
---
# <a name="system-functions-azure-cosmos-db"></a>系統函數（Azure Cosmos DB）

 Cosmos DB 提供許多內建 SQL 函式。 內建函式的分類如下所示。  
  
|函式群組|Description|Operations|  
|--------------|-----------------|-----------------| 
|[陣列函數](sql-query-array-functions.md)|下列陣列函式會對陣列輸入值執行作業，並傳回數值、布林值或陣列值。 | [ARRAY_CONCAT](sql-query-array-concat.md)、 [ARRAY_CONTAINS](sql-query-array-contains.md)、 [ARRAY_LENGTH](sql-query-array-length.md)、 [ARRAY_SLICE](sql-query-array-slice.md) |
|[日期和時間函數](sql-query-date-time-functions.md)|日期和時間函數可讓您以兩種形式取得目前的 UTC 日期和時間;數值時間戳記，其值為 Unix epoch （以毫秒為單位），或符合 ISO 8601 格式的字串。 | [GetCurrentDateTime](sql-query-getcurrentdatetime.md)、 [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md) |
|[數學函數](sql-query-mathematical-functions.md)|每個數學函數都會執行計算，通常以提供做為引數的輸入值為基礎，並且會傳回數值。 | [ABS](sql-query-abs.md)、 [ACOS](sql-query-acos.md)、 [ASIN](sql-query-asin.md)、 [ATAN](sql-query-atan.md)、 [ATN2](sql-query-atn2.md)、[天花板](sql-query-ceiling.md)、 [COS](sql-query-cos.md)、 [COT](sql-query-cot.md)、[度數](sql-query-degrees.md)、 [EXP](sql-query-exp.md)、 [FLOOR](sql-query-floor.md)、 [LOG](sql-query-log.md)、 [LOG10](sql-query-log10.md)、 [PI](sql-query-pi.md)、 [POWER](sql-query-power.md)、 [RADIANS](sql-query-radians.md)、 [RAND](sql-query-rand.md)、 [ROUND](sql-query-round.md)、 [SIGN](sql-query-sign.md)、 [SIN](sql-query-sin.md)、 [SQRT](sql-query-sqrt.md)、[正方形](sql-query-square.md)、 [TAN](sql-query-tan.md)、 [TRUNC](sql-query-trunc.md) |
|[空間函數](sql-query-spatial-functions.md)|下列空間函數會對空間物件輸入值執行作業，並傳回數值或布林值。 | [ST_DISTANCE](sql-query-st-distance.md)、 [ST_INTERSECTS](sql-query-st-intersects.md)、 [ST_ISVALID](sql-query-st-isvalid.md)、 [ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md)、 [ST_WITHIN](sql-query-st-within.md) |
|[字串函數](sql-query-string-functions.md)|下列字串函式會對字串輸入值執行作業，並傳回字串、數值或布林值。 | [CONCAT](sql-query-concat.md)、 [CONTAINS](sql-query-contains.md)、 [ENDSWITH](sql-query-endswith.md)、 [INDEX_OF](sql-query-index-of.md)、 [LEFT](sql-query-left.md)、 [LENGTH](sql-query-length.md)、 [LOWER](sql-query-lower.md)、 [LTRIM](sql-query-ltrim.md)、 [REPLACE](sql-query-replace.md)、[複寫](sql-query-replicate.md)、 [REVERSE](sql-query-reverse.md)、 [RIGHT](sql-query-right.md)、 [RTRIM](sql-query-rtrim.md)、 [STARTSWITH](sql-query-startswith.md)、 [StringToArray](sql-query-stringtoarray.md)、 [StringToBoolean](sql-query-stringtoboolean.md)、 [StringToNull](sql-query-stringtonull.md)、 [StringToNumber](sql-query-stringtonumber.md)、 [StringToObject](sql-query-stringtoobject.md)、 [SUBSTRING](sql-query-substring.md)、 [ToString](sql-query-tostring.md)、 [TRIM](sql-query-trim.md)、 [UPPER](sql-query-upper.md) |
|[類型檢查函數](sql-query-type-checking-functions.md)|類型檢查函數可讓您檢查 SQL 查詢中的運算式類型。 | [IS_ARRAY](sql-query-is-array.md)、 [IS_BOOL](sql-query-is-bool.md)、 [IS_DEFINED](sql-query-is-defined.md)、 [IS_Null](sql-query-is-null.md)、 [IS_NUMBER](sql-query-is-number.md)、 [IS_OBJECT](sql-query-is-object.md)、 [IS_PRIMITIVE](sql-query-is-primitive.md)、 [IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>內建與使用者定義函數（Udf）

如果您目前使用的使用者定義函數（UDF）已可使用內建函數，則對應的內建函數會更快速地執行，而且更有效率。

## <a name="built-in-versus-ansi-sql-functions"></a>內建與 ANSI SQL 函數

Cosmos DB 函式和 ANSI SQL 函數的主要差異在於，Cosmos DB 函數是設計來搭配無架構和混合架構資料運作。 例如，如果屬性遺失或具有非數值的值（例如 `unknown` ），則會略過該專案，而不會傳回錯誤。

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB 簡介](introduction.md)
- [陣列函數](sql-query-array-functions.md)
- [日期和時間函式](sql-query-date-time-functions.md)
- [數學函數](sql-query-mathematical-functions.md)
- [空間函數](sql-query-spatial-functions.md)
- [字串函數](sql-query-string-functions.md)
- [類型檢查函數](sql-query-type-checking-functions.md)
- [使用者定義的函式](sql-query-udfs.md)
- [彙總](sql-query-aggregates.md)
