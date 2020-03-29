---
title: 套用 SQL 轉換
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習中使用應用 SQL 轉換模組在輸入資料集上運行 SQLite 查詢以轉換資料。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 2e44a4861e2522b766aab9c7151d76c471dd2d8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314533"
---
# <a name="apply-sql-transformation"></a>套用 SQL 轉換

本文介紹 Azure 機器學習設計器（預覽）模組。

使用應用 SQL 轉換模組，您可以：
  
-   建立結果的資料表，並將資料集儲存在可攜式 SQLite 資料庫中。  
  
-   資料型別，在執行自訂轉換，或建立彙總。  
  
-   執行 SQL 查詢陳述式來篩選或改變資料並傳回查詢結果為資料表。  

> [!IMPORTANT]
> 本模組中使用的 SQL 引擎是**SQLite**。 有關 SQLite 語法的詳細資訊，請參閱[SQLite 理解的 SQL](https://www.sqlite.org/index.html)瞭解更多資訊。  

## <a name="how-to-configure-apply-sql-transformation"></a>如何配置應用 SQL 轉換  

此模組最多可能需要以三個資料集做為輸入。 引用連接到每個輸入埠的資料集時，必須使用名稱`t1`和`t2`。 `t3` 這些數字表示輸入連接埠的索引。  
  
其餘的參數是 SQL 查詢，會使用 SQLite 語法。 在**SQL Script**文字方塊中鍵入多行時，請使用分號終止每個語句。 否則，插入換行符號會轉換成空格。  

此模組支援 SQLite 語法的所有標準陳述式。 有關不支援的語句的清單，請參閱[技術說明](#technical-notes)部分。

##  <a name="technical-notes"></a>技術說明  

本節包含實現詳細資訊、提示和對常見問題的解答。

-   埠 1 始終需要輸入。  
  
-   對於包含空格或其他特殊字元的列識別碼，在引用`SELECT`或`WHERE`子句中的列時，始終將列識別碼括在方括弧或雙引號中。  
  
### <a name="unsupported-statements"></a>不支援的語句  

雖然 SQLite 支援大部分的 ANSI SQL 標準，它不包含許多商業的關聯式資料庫系統所支援的功能。 有關詳細資訊，請參閱[SQLite 理解的 SQL。](http://www.sqlite.org/lang.html) 此外，在創建 SQL 語句時，請注意以下限制：  
  
- 不過，不像大多數的 SQL 資料庫系統，SQLite 使用動態輸入的值，而非指定類型至資料行。 它弱型別，並允許隱含型別轉換。  
  
- `LEFT OUTER JOIN`已實現，但不`RIGHT OUTER JOIN`實現`FULL OUTER JOIN`或 。  

- 您可以使用`RENAME TABLE`和`ADD COLUMN`陳述式與`ALTER TABLE`命令，但其他子句不支援，包括`DROP COLUMN`，`ALTER COLUMN`，和`ADD CONSTRAINT`。  
  
- 您可以建立一個檢視內 SQLite，但之後檢視是唯讀。 無法執行`DELETE`，`INSERT`，或`UPDATE`檢視上的陳述式。 不過，您可以建立會引發觸發程序，在嘗試`DELETE`，`INSERT`，或`UPDATE`檢視上並執行其他作業的觸發程序主體中。  
  

除了官方 SQLite 網站上提供的不支援的功能清單外，以下 wiki 還提供其他不支援的功能清單[：SQLite - 不支援的 SQL](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>後續步驟

請參閱 Azure 機器學習[可用的模組集](module-reference.md)。 
