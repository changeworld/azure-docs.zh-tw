---
title: Azure 宇宙 DB 查詢語言中的 StringToNumber
description: 瞭解 Azure 宇宙 DB 中的 SQL 系統函數 StringToNumber。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5ca8d0c4a6d244823dda6f0f79a3cf5c743a12a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296417"
---
# <a name="stringtonumber-azure-cosmos-db"></a>字串編號（Azure 宇宙資料庫）
 返回轉換為 Number 的運算式。 如果無法轉換運算式，則返回未定義。  
  
## <a name="syntax"></a>語法
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   是要解析為 JSON Number 運算式的字串運算式。 JSON 中的數位必須是整數或浮點。 有關 JSON 格式的詳細資訊，請參閱[json.org](https://json.org/)  
  
## <a name="return-types"></a>傳回類型
  
  返回 Number 運算式或未定義。  
  
## <a name="examples"></a>範例
  
  下面的示例顯示了不同類型`StringToNumber`之間的表現。 

僅當編號之前或之後才允許空白。

```sql
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 以下為結果集。  
  
```json
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

在 JSON 中，有效的編號必須是整數或浮點數。

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 以下為結果集。  
  
```json
{{}}
```  

傳遞的運算式將作為 Number 運算式解析;如果為 Number 運算式，則為 "Number"運算式。這些輸入不計算為"數位"類型，因此返回未定義。 

```sql
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 以下為結果集。  
  
```json
{{}}
```  

## <a name="remarks"></a>備註

此系統功能不會利用索引。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure 宇宙 DB](sql-query-string-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
