---
title: 將已對應的資料流參數化
description: 瞭解如何參數化資料工廠管道的映射資料流程
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: c717d9ab44493d15589030073cd2ab260ef52e1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760210"
---
# <a name="parameterizing-mapping-data-flows"></a>將已對應的資料流參數化

在 Azure 資料工廠中映射資料流程支援參數的使用。 您可以在資料流程定義中定義參數，然後在整個運算式中使用這些參數。 參數值可以通過調用管道通過執行資料流程活動進行設置。 有三個選項用於設置資料流程活動運算式中的值：

* 使用管道控制流運算式語言設置動態值
* 使用資料流程運算式語言設置動態值
* 使用任一運算式語言設置靜態文本值

使用此功能可使資料流程具有通用性、靈活性和再使用性。 您可以使用這些參數參數化資料流程設置和運算式。

## <a name="create-parameters-in-a-mapping-data-flow"></a>在映射資料流程中創建參數

要向資料流程添加參數，請按一下資料流程畫布的空白部分以查看常規屬性。 在"設置"窗格中，您將看到一個名為 **"參數**"的選項卡。 選擇 **"新建"** 以生成新參數。 對於每個參數，必須指定名稱、選擇類型，並選擇設置預設值。

![創建資料流程參數](media/data-flow/create-params.png "創建資料流程參數")

## <a name="use-parameters-in-a-mapping-data-flow"></a>在映射資料流程中使用參數 

參數可以在任何資料流程運算式中引用。 參數以 $ 開頭，不可變。 您可以在"**參數"** 選項卡下找到運算式產生器內部可用參數的清單。

![資料流程參數運算式](media/data-flow/parameter-expression.png "資料流程參數運算式")

您可以通過選擇 **"新建"參數**並指定名稱和類型來快速添加其他參數。

![資料流程參數運算式](media/data-flow/new-parameter-expression.png "資料流程參數運算式")

### <a name="passing-in-a-column-name-as-a-parameter"></a>將列名稱作為參數傳遞

常見的模式是將列名稱作為參數值傳遞。 要引用與參數關聯的列，請使用 函數`byName()`。 請記住，使用強制轉換函數（如`toString()`）將列轉換為其適當類型。

例如，如果要基於參數`columnName`映射字串列，則可以添加等於`toString(byName($columnName))`的派生列變換。

![將列名稱作為參數傳遞](media/data-flow/parameterize-column-name.png "將列名稱作為輔助項傳遞")

## <a name="assign-parameter-values-from-a-pipeline"></a>從管道分配參數值

使用參數創建資料流程後，可以使用執行資料流程活動從管道執行資料流程。 將活動添加到管道畫布後，將在活動的 **"參數"** 選項卡中顯示可用資料流程參數。

![設置資料流程參數](media/data-flow/parameter-assign.png "設置資料流程參數")

如果參數資料類型為字串，則當您按一下文字方塊以設置參數值時，可以選擇輸入管道或資料流程運算式。 如果選擇管道運算式，將顯示管道運算式面板。 請確保在字串插值語法中包含管道函數，`'@{<expression>}'`例如：

```'@{pipeline().RunId}'```

如果參數不是類型字串，則始終將顯示資料流程運算式產生器。 在這裡，您可以輸入您希望與參數資料類型匹配的任何運算式或文本值。 下面是資料流程運算式的示例和運算式產生器的字面字串：

* ```toInteger(Role)```
* ```'this is my static literal string'```

每個映射資料流程可以具有管道和資料流程運算式參數的任意組合。 

![資料流程參數示例](media/data-flow/parameter-example.png "資料流程參數示例")



## <a name="next-steps"></a>後續步驟
* [執行資料流程活動](control-flow-execute-data-flow-activity.md)
* [控制流運算式](control-flow-expression-language-functions.md)
