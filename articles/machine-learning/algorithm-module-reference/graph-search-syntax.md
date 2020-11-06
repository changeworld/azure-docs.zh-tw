---
title: 圖表搜尋查詢語法
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 設計工具中的搜尋查詢語法，在管線圖形中搜尋節點。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 8/24/2020
ms.openlocfilehash: 762581ea5b3183d62913e9ea6935bf7e4c4ae67f
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420762"
---
# <a name="graph-search-query-syntax"></a>圖表搜尋查詢語法

在本文中，您將瞭解 Azure Machine Learning 中的圖形搜尋查詢語法。 [圖表搜尋] 功能可讓您依名稱和屬性來搜尋節點。 

 ![顯示範例圖表搜尋體驗的動畫屏幕快照](media/search/graph-search.gif)

圖表搜尋支援對節點名稱和批註進行全文檢索關鍵字搜尋。 您也可以篩選節點屬性，例如 runStatus、duration、computeTarget。 關鍵字搜尋是以 Lucene 查詢為基礎。 完整的搜尋查詢看起來像這樣：  

**[lucene 查詢 |[篩選查詢]** 

您可以使用 Lucene 查詢或篩選查詢。 若要同時使用，請使用 **|** 分隔符號。 篩選查詢的語法比 Lucene 查詢更嚴格。 因此，如果客戶輸入可以剖析為兩者，則會套用篩選查詢。

 

## <a name="lucene-query"></a>Lucene 查詢

圖表搜尋使用 Lucene 簡單查詢作為節點 "name" 和 "comment" 的全文檢索搜尋語法。 支援下列 Lucene 運算子：

 
- 且/或
- 萬用字元符合 **？** 和 * *\** _ 運算子。

### <a name="examples"></a>範例

- 簡易搜尋： `JSON Data`

- AND/OR： `JSON AND Validation`

- 多個和/或： `(JSON AND Validation) OR (TSV AND Training)`

 
- 萬用字元比對： 
    - `machi?e learning`
    - `mach_ing`
 
>[!NOTE]
> 您無法以 "*" 字元啟動 Lucene 查詢。

##  <a name="filter-query"></a>篩選查詢

 
篩選查詢會使用下列模式：
 
`**[key1] [operator1] [value1]; [key2] [operator1] [value2];**`

 
您可以使用下列節點屬性作為索引鍵：

- runStatus
- 計算
- duration
- 重用

並使用下列運算子：

- 大於或等於： `>=`
- 小於或等於： `<=`
- 大： `>`
- 少： `<`
- 平等： `==`
- 包含： `=`
- NotEqual `!=`
- 在 `in`

 
 

### <a name="example"></a>範例

- 持續時間 > 100;
- {Failed，NotStarted} 中的狀態
- 在 {gpu-cluster} 中計算;runStatus in {Completed}

## <a name="technical-notes"></a>技術說明

- 多個篩選準則之間的關聯性為 "AND"
- 如果 `>=,  >,  <, or  <=` 選擇了，值會自動轉換為數字類型。 否則，會使用字串類型進行比較。
- 針對所有字串類型值，比較時不區分大小寫。
- 運算子 "In" 預期集合為值，集合語法為 `{name1, name2, name3}`
- 關鍵字之間將會略過空格