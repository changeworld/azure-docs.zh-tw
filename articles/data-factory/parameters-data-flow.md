---
title: 將已對應的資料流參數化
description: 瞭解如何從 data factory 管線將對應資料流程參數化
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 564c7cf6e9627db08d543b964ce476e71bfb473d
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040745"
---
# <a name="parameterizing-mapping-data-flows"></a>將已對應的資料流參數化

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Azure Data Factory 中的對應資料流程支援使用參數。 定義您的資料流程定義內部的參數，並在整個運算式中使用它們。 參數值是透過「執行資料流程」活動所設定的呼叫管線。 您有三個選項可設定資料流程活動運算式中的值：

* 使用管線控制流程運算式語言來設定動態值
* 使用資料流程運算式語言來設定動態值
* 使用任一個運算式語言來設定靜態常值

您可以使用這項功能，讓您的資料流程程成為一般用途、有彈性且可重複使用。 您可以使用這些參數來參數化資料流程設定和運算式。

## <a name="create-parameters-in-a-mapping-data-flow"></a>在對應資料流程中建立參數

若要將參數加入至您的資料流程，請按一下 [資料流程] 畫布的空白部分，以查看一般屬性。 在 [設定] 窗格中，您會看到一個名為 [ **參數** ] 的索引標籤。 選取 [ **新增** ] 以產生新的參數。 針對每個參數，您必須指派名稱、選取類型，並選擇性地設定預設值。

![建立資料流程參數](media/data-flow/create-params.png "建立資料流程參數")

## <a name="use-parameters-in-a-mapping-data-flow"></a>在對應資料流程中使用參數 

您可以在任何資料流程運算式中參考參數。 參數以 $ 開頭，且不可變。 您可以在 [ **參數** ] 索引標籤下的 [運算式產生器] 中找到可用參數的清單。

![螢幕擷取畫面：顯示 [參數] 索引標籤中可用的參數。](media/data-flow/parameter-expression.png "資料流程參數運算式")

您可以藉由選取 [ **新增參數** ] 並指定名稱和類型，快速新增其他參數。

![螢幕擷取畫面：顯示 [參數] 索引標籤中已新增參數的參數。](media/data-flow/new-parameter-expression.png "資料流程參數運算式")

## <a name="assign-parameter-values-from-a-pipeline"></a>從管線指派參數值

當您使用參數建立資料流程之後，就可以從具有「執行資料流程」活動的管線來執行它。 將活動新增至管線畫布之後，您會在活動的 [ **參數** ] 索引標籤中看到可用的資料流程參數。

指派參數值時，您可以使用 [管線運算式語言](control-flow-expression-language-functions.md) 或以 spark 類型為基礎的資料流程 [運算式語言](data-flow-expression-functions.md) 。 每個對應資料流程可以有任意組合的管線和資料流程運算式參數。

![螢幕擷取畫面顯示 [參數] 索引標籤，其中包含針對 myparam 值選取的資料流程運算式。](media/data-flow/parameter-assign.png "設定資料流程參數")

### <a name="pipeline-expression-parameters"></a>管線運算式參數

管線運算式參數可讓您參考類似于其他管線活動的系統變數、函式、管線參數和變數。 當您按一下 [ **管線運算式** ] 時，會開啟側邊導覽，讓您可以使用運算式產生器來輸入運算式。

![螢幕擷取畫面：顯示 [運算式產生器] 窗格。](media/data-flow/parameter-pipeline.png "設定資料流程參數")

參考時，會評估管線參數，然後在資料流程運算式語言中使用它們的值。 管線運算式類型不需要符合資料流程參數類型。 

#### <a name="string-literals-vs-expressions"></a>字串常值與運算式

指派字串類型的管線運算式參數時，會加入預設的引號，並將值評估為常值。 若要將參數值讀取為數據流運算式，請核取參數旁的 [運算式] 方塊。

![螢幕擷取畫面：顯示為參數選取的 [資料流程參數] 窗格運算式。](media/data-flow/string-parameter.png "設定資料流程參數")

若資料流程參數 `stringParam` 參考具有值的管線參數 `upper(column1)` 。 

- 如果已核取 [運算式]，則會 `$stringParam` 評估為全部大寫的 column1 值。
- 如果未檢查運算式 (預設行為) ，則會  `$stringParam` 評估為 `'upper(column1)'`

#### <a name="passing-in-timestamps"></a>傳入時間戳記

在管線運算式語言中，系統變數（例如和函式）會將 `pipeline().TriggerTime` `utcNow()` 時間戳記傳回為格式為 ' Yyyy-mm-dd \' T \' HH： MM： ss 的字串。SSSSSSZ'. 若要將這些參數轉換為 timestamp 類型的資料流程參數，請使用字串插補在函式中包含所需的時間戳記 `toTimestamp()` 。 例如，若要將管線觸發時間轉換成資料流程參數，您可以使用 `toTimestamp(left('@{pipeline().TriggerTime}', 23), 'yyyy-MM-dd\'T\'HH:mm:ss.SSS')` 。 

![螢幕擷取畫面顯示 [參數] 索引標籤，您可以在其中輸入觸發時間。](media/data-flow/parameter-timestamp.png "設定資料流程參數")

> [!NOTE]
> 資料流程最多隻能支援3毫秒的數位。 `left()`函數是用來修剪其他數位。

#### <a name="pipeline-parameter-example"></a>管線參數範例

假設您有一個整數參數， `intParam` 參考字串類型的管線參數 `@pipeline.parameters.pipelineParam` 。 

![螢幕擷取畫面顯示 [參數] 索引標籤，其中包含名為 stringParam 和 intParam 的參數。](media/data-flow/parameter-pipeline-2.png "設定資料流程參數")

`@pipeline.parameters.pipelineParam` 會 `abs(1)` 在執行時間指派值給。

![螢幕擷取畫面顯示 [參數] 索引標籤的 [參數] 索引標籤值為 b s (1) 選取。](media/data-flow/parameter-pipeline-4.png "設定資料流程參數")

當 `$intParam` 在衍生資料行之類的運算式中參考時，它將會評估 `abs(1)` return `1` 。 

![顯示資料行值的螢幕擷取畫面。](media/data-flow/parameter-pipeline-3.png "設定資料流程參數")

### <a name="data-flow-expression-parameters"></a>資料流程運算式參數

選取 [資料流程 **運算式** ] 將會開啟 [資料流程運算式產生器]。 您將能夠參考函式、其他參數，以及整個資料流程中定義的架構資料行。 此運算式將在參考時評估為。

> [!NOTE]
> 如果您傳入不正確運算式或參考不存在於該轉換中的架構資料行，參數將會評估為 null。


### <a name="passing-in-a-column-name-as-a-parameter"></a>以參數形式傳遞資料行名稱

常見的模式是將資料行名稱作為參數值傳入。 如果資料行是在資料流程架構中定義，您可以直接將它參考為字串運算式。 如果未在架構中定義資料行，請使用 `byName()` 函數。 請記得使用轉換函式（例如）將資料行轉換成其適當的類型 `toString()` 。

例如，如果您想要根據參數對應字串資料行 `columnName` ，您可以將「衍生的資料行」轉換加入等於 `toString(byName($columnName))` 。

![以參數形式傳遞資料行名稱](media/data-flow/parameterize-column-name.png "以參數形式傳遞資料行名稱")

## <a name="next-steps"></a>後續步驟
* [執行資料流程活動](control-flow-execute-data-flow-activity.md)
* [控制流程運算式](control-flow-expression-language-functions.md)
