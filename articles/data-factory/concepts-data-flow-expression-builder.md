---
title: 對應資料流程中的運算式產生器
description: 在 Azure Data Factory 中，使用運算式產生器在對應資料流程中建立運算式
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: f522812f762b55ec61794101e6cd1ec15fb171ca
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212111"
---
# <a name="build-expressions-in-mapping-data-flow"></a>對應資料流程中的組建運算式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

在對應資料流程中，許多轉換屬性都是以運算式的形式輸入。 這些運算式是由資料行值、參數、函數、運算子，以及在執行時間評估為 Spark 資料類型的常值所組成。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tkur]

## <a name="open-expression-builder"></a>Open 運算式產生器

Azure Data Factory 使用者體驗中的運算式編輯介面稱為「運算式產生器」。 當您輸入運算式邏輯時，Data Factory 會使用 [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) 程式碼完成來進行反白顯示、語法檢查和自動完成。

![運算式產生器](media/data-flow/xpb1.png "運算式產生器")

在 [衍生的資料行和篩選] 之類的轉換（其中運算式是強制的）中，選取 [藍色運算式] 方塊來開啟 [運算式產生器]。

![藍色運算式方塊](media/data-flow/expressionbox.png "運算式產生器")

當您參考相符或群組依據條件中的資料行時，運算式可以從資料行提取值。 若要建立運算式，請選取 [ **計算資料行**]。

![計算資料行選項](media/data-flow/computedcolumn.png "運算式產生器")

在運算式或常值是有效輸入的情況下，選取 [ **新增動態內容** ] 以建立評估為常值的運算式。

![新增動態內容選項](media/data-flow/add-dynamic-content.png "運算式產生器")

## <a name="expression-language-reference"></a>運算式語言參考

對應的資料流程具有內建函數和運算子，可用於運算式中。 如需可用函數的清單，請參閱 [對應資料流程中的運算式函數](data-flow-expression-functions.md)。

## <a name="column-names-with-special-characters"></a>含特殊字元的資料行名稱

當您有包含特殊字元或空格的資料行名稱時，請用大括弧括住名稱，以在運算式中參考它們。

```{[dbo].this_is my complex name$$$}```

## <a name="preview-expression-results"></a>預覽運算式結果

如果已開啟 [ [偵錯工具] 模式](concepts-data-flow-debug-mode.md) ，您可以使用即時 Spark 叢集查看運算式評估結果的進行中預覽。 當您建立邏輯時，您可以即時地對運算式進行偵錯工具。 

![進行中預覽](media/data-flow/exp4b.png "運算式資料預覽")

選取 **[** 重新整理]，以針對您的來源即時取樣來更新運算式的結果。

![重新整理按鈕](media/data-flow/exp5.png "運算式資料預覽")

## <a name="string-interpolation"></a>字串插補

使用引號將常值字串文字與運算式一起括住。 您可以包含運算式函數、資料行和參數。 字串插補有助於避免在查詢字串中包含參數時，大量使用字串串連。 若要使用運算式語法，請將它括在大括弧中，

字串插補的一些範例：

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="comment-expressions"></a>批註運算式

使用單行和多行批註語法，將批註新增至您的運算式。

下列範例是有效的批註：

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```

如果您將批註放在運算式的頂端，它會出現在 [轉換] 文字方塊中，以記錄轉換運算式。

![轉換文字方塊中的批註](media/data-flow/comments2.png "註解")

## <a name="regular-expressions"></a>規則運算式

許多運算式語言函數使用正則運算式語法。 當您使用正則運算式函數時，運算式產生器會嘗試將反斜線 (\\) 成逸出字元序列。 當您在正則運算式中使用反斜線時，請將整個 RegEx 括在倒引號 () 中， \` 或使用雙反斜線。

使用倒引號的範例：

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

使用雙斜線的範例：

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="address-array-indexes"></a>位址陣列索引

如果運算式函式傳回陣列，請使用括弧 ( [] ) 來定址傳回陣列物件內的特定索引。 陣列是以其為基礎。

![運算式產生器陣列](media/data-flow/expb2.png "運算式資料預覽")

## <a name="keyboard-shortcuts"></a>鍵盤快速鍵

* Ctrl + K Ctrl + C：批註整行。
* Ctrl + K Ctrl + U：取消批註。
* F1：提供編輯器說明命令。
* Alt + 向下鍵：向下移動目前的行。
* Alt + 向上鍵：向上移動目前的行。
* Ctrl + 空格鍵：顯示內容說明。

## <a name="convert-to-dates-or-timestamps"></a>轉換成日期或時間戳記

若要在時間戳記輸出中包含字串常值，請將轉換包裝在中 ```toString()``` 。

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

若要將毫秒從 epoch 轉換成日期或時間戳記，請使用 `toTimestamp(<number of milliseconds>)` 。 如果時間是以秒為單位，則乘以1000。

```toTimestamp(1574127407*1000l)```

前一個運算式結尾的尾端 "l" 表示轉換成 long 類型做為內嵌語法。

## <a name="find-time-from-epoch-or-unix-time"></a>從 epoch 或 Unix 時間尋找時間

toLong ( currentTimestamp ( # A2-toTimestamp ( ' 1970-01-01 00：00： 00.000 '，' yyyy-MM-dd HH： MM： ss。SSS ' ) ) * 1000l

## <a name="next-steps"></a>後續步驟

[開始建立資料轉換運算式](data-flow-expression-functions.md)
