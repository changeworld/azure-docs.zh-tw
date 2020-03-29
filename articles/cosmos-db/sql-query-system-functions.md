---
title: Azure Cosmos DB 查詢語言中的系統函數
description: 瞭解 Azure Cosmos DB 中的內置和使用者定義的 SQL 系統功能。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f41adbb726313ef095084d079dc7852736e0c06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870525"
---
# <a name="system-functions-azure-cosmos-db"></a>系統功能（Azure 宇宙資料庫）

 Cosmos DB 提供許多內建 SQL 函式。 內建函式的分類如下所示。  
  
|函式群組|描述|作業|  
|--------------|-----------------|-----------------| 
|[陣列函數](sql-query-array-functions.md)|下列陣列函式會對陣列輸入值執行作業，並傳回數值、布林值或陣列值。 | [ARRAY_CONCAT](sql-query-array-concat.md)， [ARRAY_CONTAINS](sql-query-array-contains.md)， [ARRAY_LENGTH](sql-query-array-length.md)， [ARRAY_SLICE](sql-query-array-slice.md) |
|[日期和時間函數](sql-query-date-time-functions.md)|日期和時間函數允許您以兩種形式獲取當前 UTC 日期和時間;數值為 unix 紀元（以毫秒為單位）或作為符合 ISO 8601 格式的字串的數位時間戳記。 | [獲取當前日期時間](sql-query-getcurrentdatetime.md)獲取[目前時間戳](sql-query-getcurrenttimestamp.md) |
|[數學函數](sql-query-mathematical-functions.md)|每個數學函數都會執行計算，通常以提供做為引數的輸入值為基礎，並且會傳回數值。 | [ABS，](sql-query-abs.md) [ACOS，](sql-query-acos.md) [ASIN，](sql-query-asin.md) [ATAN，](sql-query-atan.md) [ATN2](sql-query-atn2.md)，[天花板](sql-query-ceiling.md)， [COS，](sql-query-cos.md) [COT，](sql-query-cot.md)[學位](sql-query-degrees.md)， [EXP，](sql-query-exp.md)[地板](sql-query-floor.md)， [LOG，](sql-query-log.md) [PI，](sql-query-pi.md)[電源](sql-query-power.md)， [RADIANS，](sql-query-radians.md)[蘭德](sql-query-rand.md)，[圓，](sql-query-round.md) [SIGN，](sql-query-sign.md) [SIN，](sql-query-sin.md) [SQRT，](sql-query-sqrt.md) [SQUARE，](sql-query-square.md) [TAN，](sql-query-tan.md) [TRUNC](sql-query-trunc.md) [LOG10](sql-query-log10.md) |
|[空間函數](sql-query-spatial-functions.md)|下列空間函數會對空間物件輸入值執行作業，並傳回數值或布林值。 | [ST_DISTANCE，](sql-query-st-distance.md) [ST_INTERSECTS](sql-query-st-intersects.md)， [ST_ISVALID](sql-query-st-isvalid.md)， [ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md)， [ST_WITHIN](sql-query-st-within.md) |
|[字串函數](sql-query-string-functions.md)|下列字串函式會對字串輸入值執行作業，並傳回字串、數值或布林值。 | [CONCAT，](sql-query-concat.md) [Contains](sql-query-contains.md)， [ENDSWITH，](sql-query-endswith.md) [INDEX_OF，](sql-query-index-of.md)[UPPER](sql-query-upper.md)[左](sql-query-left.md)，[長度](sql-query-length.md)，[下](sql-query-lower.md)部， [LTRIM，](sql-query-ltrim.md)[替換](sql-query-replace.md)，[複製](sql-query-replicate.md)，[反轉](sql-query-reverse.md)，[右](sql-query-right.md)， [RTRIM，](sql-query-rtrim.md) [STARTSWITH，](sql-query-startswith.md)[字串托Array，](sql-query-stringtoarray.md)[字串托布林，](sql-query-stringtoboolean.md)[字串編號](sql-query-stringtonumber.md)，[字串物件](sql-query-stringtoobject.md)， [SUBSTRING，](sql-query-substring.md)[托林](sql-query-tostring.md)[，](sql-query-trim.md) [StringToNull](sql-query-stringtonull.md) |
|[類型檢查函式](sql-query-type-checking-functions.md)|類型檢查函數可讓您檢查 SQL 查詢中的運算式類型。 | [IS_ARRAY、](sql-query-is-array.md) [IS_BOOL](sql-query-is-bool.md)、 [IS_DEFINED](sql-query-is-defined.md)、 [IS_Null](sql-query-is-null.md) [IS_NUMBER](sql-query-is-number.md)、 [IS_OBJECT](sql-query-is-object.md) [、](sql-query-is-string.md) [IS_PRIMITIVE](sql-query-is-primitive.md)IS_STRING |

## <a name="built-in-versus-user-defined-functions-udfs"></a>內建函數與使用者定義函數 （UF）

如果您當前使用的是使用者定義的函數 （UDF），而內建函數現在可用，則相應的內建函數將更快、更高效。

## <a name="built-in-versus-ansi-sql-functions"></a>內置與 ANSI SQL 函數

Cosmos DB 函數和 ANSI SQL 函數之間的主要區別是 Cosmos DB 函數設計為能夠很好地處理無架構和混合架構資料。 例如，如果屬性丟失或具有類似`unknown`的非數值值，則跳過該項，而不是返回錯誤。

## <a name="next-steps"></a>後續步驟

- [Azure 宇宙 DB 簡介](introduction.md)
- [陣列函數](sql-query-array-functions.md)
- [日期和時間函數](sql-query-date-time-functions.md)
- [數學函數](sql-query-mathematical-functions.md)
- [空間函數](sql-query-spatial-functions.md)
- [字串函數](sql-query-string-functions.md)
- [類型檢查函式](sql-query-type-checking-functions.md)
- [使用者定義的函式](sql-query-udfs.md)
- [彙總](sql-query-aggregates.md)
