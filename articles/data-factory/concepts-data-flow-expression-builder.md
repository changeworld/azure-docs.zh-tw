---
title: 映射資料流程的運算式產生器
description: 使用運算式產生器映射 Azure 資料工廠中的資料流程生成運算式
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/9/2019
ms.openlocfilehash: 1dd782092ce91f7b71a3a2a6f2ed1646ee39a7e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444542"
---
# <a name="build-expressions-in-mapping-data-flow"></a>在映射資料流程中生成運算式

在映射資料流程時，許多轉換屬性作為運算式輸入。 這些運算式由列值、參數、函數、運算子和文本組成，這些列值在運行時計算為 Spark 資料類型。

## <a name="open-expression-builder"></a>打開運算式產生器

Azure 資料工廠使用者體驗中的運算式編輯介面稱為運算式產生器。 輸入運算式邏輯時，資料工廠使用[IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019)代碼完成來突出顯示、語法檢查和自動完成。

![運算式產生器](media/data-flow/xpb1.png "運算式產生器")

在派生列和篩選器等轉換（運算式是強制性的）中，通過選擇藍色運算式框打開運算式產生器。

![藍色運算式框](media/data-flow/expressionbox.png "運算式產生器")

在匹配或按分組條件中引用列時，運算式可以從列中提取值。 要創建運算式，請選擇 **"計算列**"。

![計算列選項](media/data-flow/computedcolumn.png "運算式產生器")

如果運算式或文本值是有效的輸入，請選擇 **"添加動態內容**"以生成計算到文本值的運算式。

![添加動態內容選項](media/data-flow/add-dynamic-content.png "運算式產生器")

## <a name="expression-language-reference"></a>運算式語言引用

映射資料流程具有內建函數和運算子，可用於運算式。 有關可用函數的清單，請參閱[映射資料流程中的運算式函數](data-flow-expression-functions.md)。

## <a name="column-names-with-special-characters"></a>具有特殊字元的列名稱

當具有包含特殊字元或空格的列名稱時，使用大括弧括括該名稱以在運算式中引用它們。

```{[dbo].this_is my complex name$$$}```

## <a name="preview-expression-results"></a>預覽運算式結果

如果[偵錯模式](concepts-data-flow-debug-mode.md)已打開，則可以使用即時 Spark 群集查看運算式評估到的正在進行的預覽。 構建邏輯時，可以即時調試運算式。 

![正在進行的預覽](media/data-flow/exp4b.png "運算式資料預覽")

選擇 **"刷新"** 可根據源的即時示例更新運算式的結果。

![重新整理按鈕](media/data-flow/exp5.png "運算式資料預覽")

## <a name="string-interpolation"></a>字串插補

使用引號將文本字串文本與運算式一起括起來。 可以包括運算式函數、列和參數。 當參數包含在查詢字串中時，字串插值可用於避免大量使用字串串聯。 要使用運算式語法，請使用大括弧將其封閉，

字串插值的一些示例：

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="comment-expressions"></a>注釋運算式

使用單行和多行注釋語法向運算式添加注釋。

![單行和多行注釋語法](media/data-flow/comments.png "註解")

以下示例是有效的注釋：

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

如果將注釋放在運算式的頂部，則該注釋將顯示在轉換文字方塊中，以記錄轉換運算式。

![轉換文字方塊中的注釋](media/data-flow/comments2.png "註解")

## <a name="regular-expressions"></a>規則運算式

許多運算式語言函數使用正則運算式語法。 使用正則運算式函數時，運算式產生器會嘗試將反斜線\\（ ） 解釋為逸出字元序列。 在正則運算式中使用反斜線時，請將整個正則運算式封閉在反斜線中，\`或使用雙反斜線。

使用回記的示例：

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

使用雙斜杠的示例：

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="address-array-indexes"></a>位址陣列索引

使用返回陣列的運算式函數，請使用括弧 （*） 來處理返回陣列物件中的特定索引。 陣列基於這些陣列。

![運算式產生器陣列](media/data-flow/expb2.png "運算式資料預覽")

## <a name="keyboard-shortcuts"></a>鍵盤快速鍵

* Ctrl_K Ctrl_C：注釋整行。
* Ctrl_K Ctrl_U：無注釋。
* F1：提供編輯器説明命令。
* Alt_向下方向鍵：向下移動當前線。
* Alt_上方向鍵：向上移動當前線。
* Ctrl_空格鍵：顯示上下文説明。

## <a name="convert-to-dates-or-timestamps"></a>轉換為日期或時間戳記

要在時間戳記輸出中包含字串文本，請在 中```toString()```包裝轉換。

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

要將毫秒從紀元轉換為日期或時間戳記，請使用`toTimestamp(<number of milliseconds>)`。 如果時間以秒為單位，乘以 1，000。

```toTimestamp(1574127407*1000l)```

上一個運算式末尾的尾隨"l"表示轉換為長類型為內聯語法。

## <a name="next-steps"></a>後續步驟

[開始構建資料轉換運算式](data-flow-expression-functions.md)
