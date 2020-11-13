---
title: 套用 SQL 轉換
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的「套用 SQL 轉換」模組，在輸入資料集上執行 SQLite 查詢來轉換資料。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/12/2020
ms.openlocfilehash: c66fbe59fd5b2660d02bfca285f78666d64569fe
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94555595"
---
# <a name="apply-sql-transformation"></a>套用 SQL 轉換

本文描述 Azure Machine Learning 表設計工具的模組。

使用「套用 SQL 轉換」模組，您可以：
  
-   建立結果的資料表，並將資料集儲存在可攜式 SQLite 資料庫中。  
  
-   資料型別，在執行自訂轉換，或建立彙總。  
  
-   執行 SQL 查詢陳述式來篩選或改變資料並傳回查詢結果為資料表。  

> [!IMPORTANT]
> 此課程模組中使用的 SQL 引擎為 **SQLite** 。 如需 SQLite 語法的詳細資訊，請參閱 [由 Sqlite 瞭解的 SQL](https://www.sqlite.org/index.html)。
> 此模組會將資料增加至 SQLite （在記憶體資料庫中），因此模組執行需要更多的記憶體，而且可能會遇到 `Out of memory` 錯誤。 請確定您的電腦有足夠的 RAM。

## <a name="how-to-configure-apply-sql-transformation"></a>如何設定套用 SQL 轉換  

此模組最多可能需要以三個資料集做為輸入。 當您參考連接到每個輸入埠的資料集時，您必須使用名稱 `t1` 、 `t2` 和 `t3` 。 這些數字表示輸入連接埠的索引。  

以下是說明如何聯結兩個數據表的範例程式碼。 t1 和 t2 是連接到「套用 **SQL 轉換** 」之左方和中間輸入埠的兩個資料集：

```sql
SELECT t1.*
    , t3.Average_Rating
FROM t1 join
    (SELECT placeID
        , AVG(rating) AS Average_Rating
    FROM t2
    GROUP BY placeID
    ) as t3
on t1.placeID = t3.placeID
```
  
其餘的參數是 SQL 查詢，會使用 SQLite 語法。 在 [ **SQL 腳本** ] 文字方塊中輸入多行時，請使用分號來結束每個語句。 否則，插入換行符號會轉換成空格。  

此模組支援 SQLite 語法的所有標準陳述式。 如需不支援的語句清單，請參閱 [技術附注](#technical-notes) 一節。

##  <a name="technical-notes"></a>技術說明  

本節包含對常見問題的執行詳細資料、秘訣和解答。

-   埠1上一律需要輸入。  
  
-   對於包含空格或其他特殊字元的資料行識別碼，在參考或子句中的資料行時，一律以方括弧或雙引號括住資料行 `SELECT` 識別碼 `WHERE` 。  
  
### <a name="unsupported-statements"></a>不支援的語句  

雖然 SQLite 支援大部分的 ANSI SQL 標準，它不包含許多商業的關聯式資料庫系統所支援的功能。 如需詳細資訊，請參閱 [由 SQLite 瞭解的 SQL](http://www.sqlite.org/lang.html)。 此外，在建立 SQL 語句時，請留意下列限制：  
  
- 不過，不像大多數的 SQL 資料庫系統，SQLite 使用動態輸入的值，而非指定類型至資料行。 它弱型別，並允許隱含型別轉換。  
  
- `LEFT OUTER JOIN` 是實作為，但不是 `RIGHT OUTER JOIN` 或 `FULL OUTER JOIN` 。  

- 您可以使用`RENAME TABLE`和`ADD COLUMN`陳述式與`ALTER TABLE`命令，但其他子句不支援，包括`DROP COLUMN`，`ALTER COLUMN`，和`ADD CONSTRAINT`。  
  
- 您可以建立一個檢視內 SQLite，但之後檢視是唯讀。 無法執行`DELETE`，`INSERT`，或`UPDATE`檢視上的陳述式。 不過，您可以建立會引發觸發程序，在嘗試`DELETE`，`INSERT`，或`UPDATE`檢視上並執行其他作業的觸發程序主體中。  
  

除了官方 SQLite 網站上提供的不支援函式清單之外，下列 wiki 還提供其他不支援的功能清單： [SQLite-不支援的 SQL](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
