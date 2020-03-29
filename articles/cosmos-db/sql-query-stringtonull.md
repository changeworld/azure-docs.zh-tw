---
title: 在 Azure 宇宙 DB 查詢語言中字串ToNull
description: 瞭解 Azure 宇宙 DB 中的 SQL 系統函數 StringToNull。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7e17547f88465103b61eabec04978ea806ffa2c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296434"
---
# <a name="stringtonull-azure-cosmos-db"></a>字串到空（Azure 宇宙資料庫）
 返回轉換為 null 的運算式。 如果無法轉換運算式，則返回未定義。  
  
## <a name="syntax"></a>語法
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   是要解析為空運算式的字串運算式。
  
## <a name="return-types"></a>傳回類型
  
  返回空運算式或未定義。  
  
## <a name="examples"></a>範例
  
  下面的示例顯示了不同類型`StringToNull`之間的表現。 

以下是具有有效輸入的示例。

 空白僅在"空"之前或之後允許。

```sql
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 以下為結果集。  
  
```json
[{"n1": null, "n2": null, "n3": true}]
```  

以下是輸入不正確示例。

Null 區分大小寫，必須用所有小寫字元（即"null"）編寫。

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 以下為結果集。  
  
```json
[{}]
```  

傳遞的運算式將解析為空運算式;因此，將解析為 null 運算式。這些輸入不計算為鍵入 null，因此返回未定義。

```sql
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
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
