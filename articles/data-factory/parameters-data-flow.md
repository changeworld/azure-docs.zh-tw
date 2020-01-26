---
title: 將已對應的資料流參數化
description: 瞭解如何從 data factory 管線參數化對應資料流程
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: c717d9ab44493d15589030073cd2ab260ef52e1c
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760210"
---
# <a name="parameterizing-mapping-data-flows"></a>將已對應的資料流參數化

對應中的資料流程 Azure Data Factory 支援使用參數。 您可以在資料流程定義內定義參數，然後在整個運算式中使用。 參數值可透過「執行資料流程」活動來設定呼叫管線。 您有三個選項可設定 [資料流程] 活動運算式中的值：

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

### <a name="passing-in-a-column-name-as-a-parameter"></a>傳入資料行名稱做為參數

常見的模式是將資料行名稱傳入做為參數值。 若要參考與參數相關聯的資料行，請使用 `byName()` 函數。 請記得使用轉換函數（例如 `toString()`）將資料行轉換成其適當的類型。

例如，如果您想要根據參數 `columnName`來對應字串資料行，您可以加入等於 `toString(byName($columnName))`的「衍生的資料行」轉換。

![傳入資料行名稱做為參數](media/data-flow/parameterize-column-name.png "以 paramete 的形式傳入資料行名稱")

## <a name="assign-parameter-values-from-a-pipeline"></a>從管線指派參數值

建立具有參數的資料流程之後，您可以使用「執行資料流程」活動從管線執行它。 將活動新增至管線畫布之後，您將會在活動的 [**參數**] 索引標籤中看到可用的資料流程參數。

![設定資料流程參數](media/data-flow/parameter-assign.png "設定資料流程參數")

如果您的參數資料類型是 [字串]，當您按一下文字方塊來設定參數值時，您可以選擇輸入管線或資料流程運算式。 如果您選擇 [管線運算式]，將會顯示 [管線運算式] 面板。 請務必使用 `'@{<expression>}'`在字串內插補點語法中包含管線函數，例如：

```'@{pipeline().RunId}'```

如果您的參數不是字串類型，您一定會看到資料流程運算式產生器。 在這裡，您可以輸入任何您想要的運算式或常值，以符合參數的資料類型。 以下是「運算式產生器」中的「資料流程運算式」和「常值字串」範例：

* ```toInteger(Role)```
* ```'this is my static literal string'```

每個對應資料流程可以有管線和資料流程運算式參數的任意組合。 

![資料流程參數範例](media/data-flow/parameter-example.png "資料流程參數範例")



## <a name="next-steps"></a>後續步驟
* [執行資料流程活動](control-flow-execute-data-flow-activity.md)
* [控制流程運算式](control-flow-expression-language-functions.md)
