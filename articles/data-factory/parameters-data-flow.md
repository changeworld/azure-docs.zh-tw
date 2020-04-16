---
title: 將已對應的資料流參數化
description: 瞭解如何參數化資料工廠導管的映射資料串流
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 82660cdb4ab6523bae7608fe3b071f20cb3603f8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419165"
---
# <a name="parameterizing-mapping-data-flows"></a>將已對應的資料流參數化

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

在 Azure 數據工廠中映射數據流支援參數的使用。 您可以在資料串定義中定義參數,然後在整個運算式中使用這些參數。 參數值可以通過調用管道通過執行數據流活動進行設置。 有三個選項用於設定資料串流活動運算式的值:

* 使用導管控制串式語言設定動態值
* 使用資料串流表示式語言設定動態值
* 使用任一表示式語言設定靜態文字值

使用此功能可使資料流具有通用性、靈活性和可重用性。 您可以使用這些參數參數化資料流設定和運算式。

## <a name="create-parameters-in-a-mapping-data-flow"></a>建立式參數

要向資料流添加參數,請單擊數據流畫布的空白部分以查看常規屬性。 在「設定」窗格中,您將看到一個名為 **「參數**」的選項卡。 選擇 **"新建'** 以產生新參數。 對於每個參數,必須指定名稱、選擇類型,並選擇設置預設值。

![建立資料串流參數](media/data-flow/create-params.png "建立資料串流參數")

## <a name="use-parameters-in-a-mapping-data-flow"></a>在映射資料串使用參數 

參數可以在任何數據流表達式中引用。 參數以 $ 開頭,不可變。 您可以在「**參數」** 選項卡下找到表示式產生器內部可用參數的清單。

![資料串流參數運算式](media/data-flow/parameter-expression.png "資料串流參數運算式")

您可以通過選擇 **「新增」參數**並指定名稱和類型來快速添加其他參數。

![資料串流參數運算式](media/data-flow/new-parameter-expression.png "資料串流參數運算式")

### <a name="passing-in-a-column-name-as-a-parameter"></a>將欄名稱為參數傳遞

常見的模式是將列名稱作為參數值傳遞。 要參考與參數關聯的欄,請使用函數`byName()`。 請記住,使用強制轉換函數(如`toString()`)將列轉換為其適當類型。

例如,如果要基於參數`columnName`映射字串列,則可以添加等於`toString(byName($columnName))`的派生列變換。

![將欄名稱為參數傳遞](media/data-flow/parameterize-column-name.png "將欄位名稱為輔助項目傳遞")

## <a name="assign-parameter-values-from-a-pipeline"></a>從導管分配參數值

使用參數創建資料流後,可以使用執行數據流活動從管道執行資料流。 將活動添加到管道畫布後,將在活動的 **「參數」** 選項卡中顯示可用數據流參數。

![設定資料串流參數](media/data-flow/parameter-assign.png "設定資料串流參數")

如果參數資料類型為字串,則當您按下文字框以設置參數值時,可以選擇輸入管道或資料流運算式。 如果選擇管道表達式,將顯示管道表達式面板。 請確定在字串插值語法中包含導管函數,`'@{<expression>}'`例如:

```'@{pipeline().RunId}'```

如果參數不是類型字串,則始終將顯示數據流表達式生成器。 在這裡,您可以輸入您希望與參數數據類型匹配的任何運算式或文本值。 下面是資料串流表示式的範例與表示式產生器的字面字串:

* ```toInteger(Role)```
* ```'this is my static literal string'```

每個映射數據流可以具有管道和數據流運算式參數的任意組合。 

![資料流參數範例](media/data-flow/parameter-example.png "資料流參數範例")



## <a name="next-steps"></a>後續步驟
* [執行資料串流活動](control-flow-execute-data-flow-activity.md)
* [控制流運算式](control-flow-expression-language-functions.md)
