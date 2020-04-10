---
title: 映射資料串流的運算式產生器
description: 使用表示式產生器映射 Azure 資料工廠中的資料流程產生運算式
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 196d917d31eb08af80587bba30d9f7e67bf8cbea
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991700"
---
# <a name="build-expressions-in-mapping-data-flow"></a>在映射資料流程產生式運算式

在映射數據流時,許多轉換屬性作為表達式輸入。 這些運算式由列值、參數、函數、運算符和文本組成,這些列值在運行時計算為 Spark 數據類型。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tkur]

## <a name="open-expression-builder"></a>開啟表示式產生器

Azure 資料工廠用戶體驗中的運算式編輯界面稱為運算式生成器。 輸入表示式邏輯時,數據工廠使用[IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019)代碼完成來突出顯示、語法檢查和自動完成。

![運算式產生器](media/data-flow/xpb1.png "運算式產生器")

在派生列和篩選器等轉換(運算式是強制性的)中,通過選擇藍色表達式框打開表達式生成器。

![藍色運算式框](media/data-flow/expressionbox.png "運算式產生器")

在匹配或按分組條件中引用列時,表達式可以從列中提取值。 要建立運算式,請選擇 **「計算列**」 。。

![計算欄選項](media/data-flow/computedcolumn.png "運算式產生器")

如果表示式或文字值是有效的輸入,請選擇 **「添加動態內容**」以生成計算到文本值的運算式。

![新增動態內容選項](media/data-flow/add-dynamic-content.png "運算式產生器")

## <a name="expression-language-reference"></a>運算為人

映射資料流具有內建函數和運算符,可用於運算式。 有關可用函數的清單,請參閱[映射資料串流中的運算式函數](data-flow-expression-functions.md)。

## <a name="column-names-with-special-characters"></a>具有特殊字元的欄位名稱

當具有包含特殊字元或空格的列名稱時,使用大括弧括括該名稱以在表達式中引用它們。

```{[dbo].this_is my complex name$$$}```

## <a name="preview-expression-results"></a>預覽表示式結果

如果[調試模式](concepts-data-flow-debug-mode.md)已打開,則可以使用即時 Spark 群集查看表達式評估到的正在進行的預覽。 構建邏輯時,可以實時調試表達式。 

![正在進行的預覽](media/data-flow/exp4b.png "運算式資料預覽")

選擇 **「刷新」** 可根據源的即時範例更新表達式的結果。

![重新整理按鈕](media/data-flow/exp5.png "運算式資料預覽")

## <a name="string-interpolation"></a>字串插補

使用引號將文字字串文本與運算式一起括起來。 可以包括表達式函數、列和參數。 當參數包含在查詢字串中時,字串插值可用於避免大量使用字串串聯。 要使用表達式語法,請使用大括弧將其封閉,

字串插值的一些範例:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="comment-expressions"></a>註解表示式

使用單行和多行註釋語法向表達式添加註釋。

![單行和多行註解語法](media/data-flow/comments.png "註解")

以下範例是有效的註解:

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

如果將注釋放在表達式的頂部,則該註釋將顯示在轉換文本框中,以記錄轉換表達式。

![轉換文字框的註解](media/data-flow/comments2.png "註解")

## <a name="regular-expressions"></a>規則運算式

許多表達式語言函數使用正則表達式語法。 使用正則運算式函數時,表達式生成器會嘗試將反斜杠\\() 解釋為轉義字元序列。 在正則運算式中使用反斜杠時,請將整個正則表達式封閉在反斜杠中,\`或使用雙反斜杠。

使用回記的範例:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

使用雙斜杠的範例:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="address-array-indexes"></a>地址陣列索引

使用返回陣列的運算式函數,請使用括弧 (*) 來處理返回陣列物件中的特定索引。 陣列基於這些陣列。

![運算式產生器陣組](media/data-flow/expb2.png "運算式資料預覽")

## <a name="keyboard-shortcuts"></a>鍵盤快速鍵

* Ctrl_K Ctrl_C:註釋整行。
* Ctrl_K Ctrl_U:無註釋。
* F1:提供編輯器説明命令。
* Alt_向下箭頭鍵:向下移動當前線。
* Alt_上箭頭鍵:向上移動當前線。
* Ctrl_空格鍵:顯示上下文説明。

## <a name="convert-to-dates-or-timestamps"></a>轉換為日期或時間戳

要在時間戳輸出中包含字串文本,請在中```toString()```包裝轉換。

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

要將毫秒從紀元轉換為日期或時間戳,請使用`toTimestamp(<number of milliseconds>)`。 如果時間以秒為單位,乘以 1,000。

```toTimestamp(1574127407*1000l)```

上一個表達式末尾的尾隨"l"表示轉換為長類型為內聯語法。

## <a name="next-steps"></a>後續步驟

[開始編譯資料轉換表示式](data-flow-expression-functions.md)
