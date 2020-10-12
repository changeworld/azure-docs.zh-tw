---
title: 對應資料流程中的運算式產生器
description: 使用運算式產生器在 Azure Data Factory 中對應資料流程來建立運算式
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/14/2020
ms.openlocfilehash: 4297cc83ab3fa280e15480aefcd5aef8734c65ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531008"
---
# <a name="build-expressions-in-mapping-data-flow"></a>在對應資料流程中建立運算式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

在對應資料流程中，許多轉換屬性都是以運算式的形式輸入。 這些運算式是由在執行時間評估為 Spark 資料類型的資料行值、參數、函數、運算子和常值所組成。 對應資料流程的專用體驗旨在協助您建立這些稱為「 **運算式**產生器」的運算式。 使用  [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense) 程式碼完成以醒目提示、語法檢查和自動完成，運算式產生器的設計目的是讓建立資料流程變得更容易。 本文說明如何使用運算式產生器來有效地建立您的商務邏輯。

![運算式產生器](media/data-flow/expresion-builder.png "運算式產生器")

## <a name="open-expression-builder"></a>Open Expression Builder

開啟運算式產生器的進入點有多個。 這些全都取決於資料流程轉換的特定內容。 最常見的使用案例是轉換，例如 [衍生](data-flow-derived-column.md) 的資料行和 [匯總](data-flow-aggregate.md) ，使用者可以使用資料流程運算式語言來建立或更新資料行。 您可以選取資料行清單上方的 [ **開啟運算式** 產生器] 來開啟運算式產生器。 您也可以按一下資料行內容，並將運算式產生器直接開啟至該運算式。

![Open Expression Builder 衍生](media/data-flow/open-expression-builder-derive.png "Open Expression Builder 衍生")

在某些轉換（例如 [篩選]）中，按一下 [藍色運算式] 文字方塊將會開啟 [運算式產生 [器](data-flow-filter.md)]。 

![藍色運算式方塊](media/data-flow/expressionbox.png "運算式產生器")

當您參考相符或分組依據條件中的資料行時，運算式可以從資料行中解壓縮值。 若要建立運算式，請選取 [ **計算資料行**]。

![計算資料行選項](media/data-flow/computedcolumn.png "運算式產生器")

如果運算式或常值為有效輸入，請選取 [ **加入動態內容** ] 來建立評估為常值的運算式。

![新增動態內容選項](media/data-flow/add-dynamic-content.png "運算式產生器")

## <a name="expression-elements"></a>Expression 元素

在對應資料流程中，運算式可以包含資料行值、參數、函數、區域變數、運算子和常值。 這些運算式必須評估為 Spark 資料類型，例如字串、布林值或整數。

![Expression 元素](media/data-flow/expression-elements.png "Expression 元素")

### <a name="functions"></a>函式

對應資料流程具有可在運算式中使用的內建函數和運算子。 如需可用函數的清單，請參閱 [對應資料流程語言參考](data-flow-expression-functions.md)。

#### <a name="address-array-indexes"></a>位址陣列索引

處理傳回陣列類型的資料行或函式時，請使用括弧 ( [] ) 來存取特定的元素。 如果索引不存在，運算式就會評估為 Null。

![Expression Builder 陣列](media/data-flow/expression-array.png "運算式資料預覽")

> [!IMPORTANT]
> 在對應資料流程中，陣列是以1為基礎的意義，也就是第一個元素是由索引一開始參考。 例如，myArray [1] 會存取名為 ' myArray ' 之陣列的第一個元素。

### <a name="input-schema"></a>輸入結構描述

如果您的資料流程在其任何來源中使用已定義的架構，您可以在許多運算式中依名稱參考資料行。 如果您使用架構漂移，則可以使用或函數明確地參考資料行， `byName()` `byNames()` 或使用資料行模式進行比對。

#### <a name="column-names-with-special-characters"></a>具有特殊字元的資料行名稱

當您有包含特殊字元或空格的資料行名稱時，請以大括弧括住名稱，以便在運算式中參考它們。

```{[dbo].this_is my complex name$$$}```

### <a name="parameters"></a>參數

參數是在執行時間透過管線傳遞至資料流程的值。 若要參考參數，請按一下 [ **運算式** 專案] 視圖中的參數，或使用其名稱前面的貨幣符號來參考它。 例如，會參考名為 parameter1 的參數 `$parameter1` 。 若要深入瞭解，請參閱將 [對應資料流程參數](parameters-data-flow.md)化。

### <a name="locals"></a>本機

如果您要跨多個資料行共用邏輯，或想要可區隔您的邏輯，您可以在衍生的 column\. 內建立本機 若要參考本機，請在 [ **運算式** 專案] 視圖中按一下 [本機]，或使用其名稱前面的冒號進行參考。 例如，會參考名為 local1 的本機 `:local1` 。 深入瞭解 [衍生資料行檔](data-flow-derived-column.md#locals)中的區域變數。

## <a name="preview-expression-results"></a>預覽運算式結果

如果開啟 [debug 模式](concepts-data-flow-debug-mode.md) ，您就可以互動方式使用 debug 叢集來預覽運算式評估結果。 選取 **[** 資料預覽] 旁的 [重新整理]，以更新資料預覽的結果。 您可以看到每個資料列的輸出，並指定輸入資料行。

![進行中預覽](media/data-flow/preview-expression.png "運算式資料預覽")

## <a name="string-interpolation"></a>字串插補

當您建立使用運算式專案的長字串時，請使用字串插補來輕鬆地建立複雜字串邏輯。 字串插補可避免在查詢字串中包含參數時大量使用字串串連。 使用雙引號將常值字串文字與運算式一起括住。 您可以包含運算式函數、資料行和參數。 若要使用運算式語法，請以大括弧括住。

字串插補的一些範例：

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

* ```"{:playerName} is a {:playerRating} player"```

## <a name="commenting-expressions"></a>批註運算式

使用單行和多行批註語法，將批註新增至您的運算式。

下列範例是有效的批註：

* ```/* This is my comment */```

* ```/* This is a```
* ```multi-line comment */```

如果您將批註放在運算式頂端，它會出現在 [轉換] 文字方塊中，以記載轉換運算式。

![轉換文字方塊中的批註](media/data-flow/comment-expression.png "註解")

## <a name="regular-expressions"></a>規則運算式

許多運算式語言函數都會使用正則運算式語法。 當您使用正則運算式函式時，運算式產生器會嘗試將反斜線 (\\) 成 escape 字元序列。 當您在正則運算式中使用反斜線時，請將整個 RegEx 括在倒引號 (\`) 或使用雙反斜線。

使用倒引號的範例：

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

使用雙斜線的範例：

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="keyboard-shortcuts"></a>鍵盤快速鍵

以下是「運算式產生器」中可用快速鍵的清單。 大部分的 intellisense 快速鍵都可在建立運算式時使用。

* Ctrl + K Ctrl + C：整行批註。
* Ctrl + K Ctrl + U：取消批註。
* F1：提供編輯器説明命令。
* Alt + 向下鍵：下移目前的行。
* Alt + 向上鍵：下移目前的行。
* Ctrl + 空格鍵：顯示內容説明。

## <a name="commonly-used-expressions"></a>常用的運算式

### <a name="convert-to-dates-or-timestamps"></a>轉換成日期或時間戳記

若要在時間戳記輸出中包含字串常值，請將您的轉換包裝在中 ```toString()``` 。

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

若要將毫秒從 epoch 轉換成日期或時間戳記，請使用 `toTimestamp(<number of milliseconds>)` 。 如果時間是以秒為單位，乘以1000。

```toTimestamp(1574127407*1000l)```

前一個運算式結尾的尾端 "l" 表示轉換為 long 型別做為內嵌語法。

### <a name="find-time-from-epoch-or-unix-time"></a>尋找來自 epoch 或 Unix 時間的時間

toLong ( currentTimestamp ( # A2-toTimestamp ( ' 1970-01-01 00：00： 00.000 '，' yyyy-mm-dd HH： MM： ss。SSS ' ) ) * 1000l

## <a name="next-steps"></a>後續步驟

[開始建立資料轉換運算式](data-flow-expression-functions.md)
