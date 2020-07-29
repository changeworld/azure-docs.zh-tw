---
title: 套用 SQL 轉換
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的 [套用 SQL 轉換] 模組，在輸入資料集上執行 SQLite 查詢以轉換資料。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 2e44a4861e2522b766aab9c7151d76c471dd2d8c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76314533"
---
# <a name="apply-sql-transformation"></a>套用 SQL 轉換

本文說明 Azure Machine Learning 設計工具（預覽）的模組。

使用 [套用 SQL 轉換] 模組，您可以：
  
-   建立結果的資料表，並將資料集儲存在可攜式 SQLite 資料庫中。  
  
-   資料型別，在執行自訂轉換，或建立彙總。  
  
-   執行 SQL 查詢陳述式來篩選或改變資料並傳回查詢結果為資料表。  

> [!IMPORTANT]
> 此模組中使用的 SQL 引擎是**SQLite**。 如需 SQLite 語法的詳細資訊，請參閱[Sqlite 瞭解的 SQL](https://www.sqlite.org/index.html) ，以取得詳細資訊。  

## <a name="how-to-configure-apply-sql-transformation"></a>如何設定 Apply SQL 轉換  

此模組最多可能需要以三個資料集做為輸入。 當您參考連接到每個輸入埠的資料集時，您必須使用名稱 `t1` 、 `t2` 和 `t3` 。 這些數字表示輸入連接埠的索引。  
  
其餘的參數是 SQL 查詢，會使用 SQLite 語法。 在 [ **SQL 腳本**] 文字方塊中輸入多行時，請使用分號來結束每個語句。 否則，插入換行符號會轉換成空格。  

此模組支援 SQLite 語法的所有標準陳述式。 如需不支援的語句清單，請參閱[技術](#technical-notes)提示一節。

##  <a name="technical-notes"></a>技術說明  

本章節包含執行詳細資料、秘訣和常見問題的解答。

-   埠1上一律需要輸入。  
  
-   對於包含空格或其他特殊字元的資料行識別碼，請在參考或子句中的資料行時，一律以方括弧或雙引號括住資料行識別碼 `SELECT` `WHERE` 。  
  
### <a name="unsupported-statements"></a>不支援的語句  

雖然 SQLite 支援大部分的 ANSI SQL 標準，它不包含許多商業的關聯式資料庫系統所支援的功能。 如需詳細資訊，請參閱[SQLite 所瞭解的 SQL](http://www.sqlite.org/lang.html)。 此外，建立 SQL 語句時，請注意下列限制：  
  
- 不過，不像大多數的 SQL 資料庫系統，SQLite 使用動態輸入的值，而非指定類型至資料行。 它弱型別，並允許隱含型別轉換。  
  
- `LEFT OUTER JOIN`已實作為，但不是 `RIGHT OUTER JOIN` 或 `FULL OUTER JOIN` 。  

- 您可以使用`RENAME TABLE`和`ADD COLUMN`陳述式與`ALTER TABLE`命令，但其他子句不支援，包括`DROP COLUMN`，`ALTER COLUMN`，和`ADD CONSTRAINT`。  
  
- 您可以建立一個檢視內 SQLite，但之後檢視是唯讀。 無法執行`DELETE`，`INSERT`，或`UPDATE`檢視上的陳述式。 不過，您可以建立會引發觸發程序，在嘗試`DELETE`，`INSERT`，或`UPDATE`檢視上並執行其他作業的觸發程序主體中。  
  

除了官方 SQLite 網站上所提供的不支援函式清單之外，下列 wiki 還提供其他不支援的功能清單： [SQLite-不支援的 SQL](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
