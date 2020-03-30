---
title: Azure 宇宙 DB 查詢語言中的 StringToBoolean
description: 瞭解 Azure 宇宙 DB 中的 SQL 系統函數 StringToBoolean。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ce11db91eff51e669f0917fbf34b1d560d0e9f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296536"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>字串 ToBoolean（Azure 宇宙資料庫）
 返回轉換為布林的運算式。 如果無法轉換運算式，則返回未定義。  
  
## <a name="syntax"></a>語法
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   是要解析為布林運算式的字串運算式。  
  
## <a name="return-types"></a>傳回類型
  
  返回布林運算式或未定義。  
  
## <a name="examples"></a>範例
  
  下面的示例顯示了不同類型`StringToBoolean`之間的表現。 
 
 以下是具有有效輸入的示例。

空白僅在"真"/"假"之前或之後允許。

```sql
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 以下為結果集。  
  
```json
[{"b1": true, "b2": false, "b3": false}]
```  

以下是輸入不正確示例。

 布林是區分大小寫的，必須用所有小寫字元（即"真"和"假"）書寫。

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

以下為結果集。  
  
```json
[{}]
``` 

傳遞的運算式將作為布林運算式解析;如果傳遞的運算式，則為布林運算式。這些輸入不計算鍵入布林，因此返回未定義。

```sql
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

以下為結果集。  
  
```json
[{}]
```  

## <a name="remarks"></a>備註

此系統功能不會利用索引。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure 宇宙 DB](sql-query-string-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
