---
title: 將已對應的資料流參數化
description: 瞭解如何從 data factory 管線參數化對應資料流程
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 8e88e5e8a9fbe1881959c5183dc01b11ac681bdf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82780365"
---
# <a name="parameterizing-mapping-data-flows"></a>將已對應的資料流參數化

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

對應中的資料流程 Azure Data Factory 支援使用參數。 定義資料流程定義內的參數，並在整個運算式中使用它們。 參數值是由呼叫管線透過「執行資料流程」活動來設定。 您有三個選項可設定 [資料流程] 活動運算式中的值：

* 使用管線控制流程運算式語言來設定動態值
* 使用資料流程運算式語言來設定動態值
* 使用任一個運算式語言來設定靜態常值

使用此功能可讓您的資料流程程成為一般用途、彈性且可重複使用。 您可以使用這些參數將資料流程設定和運算式參數化。

## <a name="create-parameters-in-a-mapping-data-flow"></a>在對應資料流程中建立參數

若要將參數加入至您的資料流程，請按一下 [資料流程] 畫布的空白部分，以查看一般屬性。 在 [設定] 窗格中，您會看到名為 [**參數**] 的索引標籤。 選取 [**新增**] 以產生新的參數。 針對每個參數，您必須指派名稱、選取類型，並選擇性地設定預設值。

![建立資料流程參數](media/data-flow/create-params.png "建立資料流程參數")

## <a name="use-parameters-in-a-mapping-data-flow"></a>在對應資料流程中使用參數 

可以在任何資料流程運算式中參考參數。 參數是以 $ 開頭，而且是不可變的。 您會在 [**參數**] 索引標籤底下的 [運算式產生器] 中找到可用參數的清單。

![資料流程參數運算式](media/data-flow/parameter-expression.png "資料流程參數運算式")

您可以選取 [**新增參數**] 並指定名稱和類型，以快速新增其他參數。

![資料流程參數運算式](media/data-flow/new-parameter-expression.png "資料流程參數運算式")

## <a name="assign-parameter-values-from-a-pipeline"></a>從管線指派參數值

建立具有參數的資料流程之後，您可以使用「執行資料流程」活動從管線執行它。 將活動新增至管線畫布之後，您將會在活動的 [**參數**] 索引標籤中看到可用的資料流程參數。

指派參數值時，您可以使用[管線運算式語言](control-flow-expression-language-functions.md)或以 spark 類型為基礎的[資料流程運算式語言](data-flow-expression-functions.md)。 每個對應資料流程可以有管線和資料流程運算式參數的任意組合。

![設定資料流程參數](media/data-flow/parameter-assign.png "設定資料流程參數")

### <a name="pipeline-expression-parameters"></a>管線運算式參數

管線運算式參數可讓您參考與其他管線活動類似的系統變數、函式、管線參數和變數。 當您按一下 [**管線運算式**] 時，將會開啟一個側邊導覽，讓您使用運算式產生器來輸入運算式。

![設定資料流程參數](media/data-flow/parameter-pipeline.png "設定資料流程參數")

當參考時，會評估管線參數，然後在資料流程運算式語言中使用其值。 管線運算式類型不需要與資料流程參數類型相符。 

#### <a name="string-literals-vs-expressions"></a>字串常值與運算式

指派字串類型的管線運算式參數時，預設會加入引號，並將值評估為常值。 若要將參數值讀取為數據流運算式，請核取參數旁的 [運算式] 方塊。

![設定資料流程參數](media/data-flow/string-parameter.png "設定資料流程參數")

如果資料流程參數 `stringParam` 參考具有值的管線參數，則為 `upper(column1)` 。 

- 如果已核取 [運算式]，會 `$stringParam` 評估為 column1 全部大寫的值。
- 如果未核取 [運算式] （預設行為），則會 `$stringParam` 評估為`'upper(column1)'`

#### <a name="passing-in-timestamps"></a>傳入時間戳記

在管線運算式語言中，像是之類的系統變數和函式，會將 `pipeline().TriggerTime` `utcNow()` 時間戳記當做字串傳回，格式為 ' Yyyy-mm-dd \' T \' HH： MM： ss。SSSSSSZ'. 若要將這些轉換成 timestamp 類型的資料流程參數，請使用字串內插補點，在函式中包含所需的時間戳記 `toTimestamp()` 。 例如，若要將管線觸發程式時間轉換成資料流程參數，您可以使用 `toTimestamp(left('@{pipeline().TriggerTime}', 23), 'yyyy-MM-dd\'T\'HH:mm:ss.SSS')` 。 

![設定資料流程參數](media/data-flow/parameter-timestamp.png "設定資料流程參數")

> [!NOTE]
> 資料流程最多隻能支援3毫秒的數位。 `left()`函數是用來修剪其他數位。

#### <a name="pipeline-parameter-example"></a>管線參數範例

假設您有一個整數參數， `intParam` 它參考了 String 類型的管線參數 `@pipeline.parameters.pipelineParam` 。 

![設定資料流程參數](media/data-flow/parameter-pipeline-2.png "設定資料流程參數")

`@pipeline.parameters.pipelineParam`會 `abs(1)` 在執行時間指派的值。

![設定資料流程參數](media/data-flow/parameter-pipeline-4.png "設定資料流程參數")

當 `$intParam` 在運算式（例如衍生的資料行）中參考時，它會評估 `abs(1)` return `1` 。 

![設定資料流程參數](media/data-flow/parameter-pipeline-3.png "設定資料流程參數")

### <a name="data-flow-expression-parameters"></a>資料流程運算式參數

選取 [資料流程**運算式**] 將會開啟 [資料流程運算式產生器]。 您將能夠在整個資料流程中參考函式、其他參數和任何已定義的架構資料行。 這個運算式會在參考時評估為。

> [!NOTE]
> 如果您傳入不正確運算式，或參考不存在於該轉換中的架構資料行，則參數會評估為 null。


### <a name="passing-in-a-column-name-as-a-parameter"></a>傳入資料行名稱做為參數

常見的模式是將資料行名稱傳入做為參數值。 如果資料行定義于資料流程架構中，您可以直接將它參考為字串運算式。 如果未在架構中定義資料行，請使用 `byName()` 函數。 請記得使用轉換函數（例如），將資料行轉換成其適當的類型 `toString()` 。

例如，如果您想要根據參數來對應字串資料行 `columnName` ，您可以加入等於的「衍生的資料行」轉換 `toString(byName($columnName))` 。

![傳入資料行名稱做為參數](media/data-flow/parameterize-column-name.png "傳入資料行名稱做為參數")

## <a name="next-steps"></a>後續步驟
* [執行資料流程活動](control-flow-execute-data-flow-activity.md)
* [控制流程運算式](control-flow-expression-language-functions.md)
