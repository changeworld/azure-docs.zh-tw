---
title: Azure 資料工廠映射資料串流的欄模式
description: 使用 Azure 資料工廠映射資料串流的欄模式建立通用資料轉換模式
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: aacec8830948e08f66d71da88897670f7ef43788
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606125"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>在映射資料串態使用欄模式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

多個映射資料流轉換允許您基於模式引用範本列,而不是硬編碼的列名稱。 此符合稱為*欄模式*。 您可以根據名稱、資料類型、流或位置定義模式以匹配列,而不是要求確切的欄位名稱。 有兩種情況的列模式很有用:

* 如果傳入源欄位經常更改,例如更改文本檔或 NoSQL 資料庫中的列。 這個專案稱為[架構漂移](concepts-data-flow-schema-drift.md)。
* 如果要對一組較大的列執行常見操作。 例如,想要將列名稱中"總計"的每個列轉換為雙列。

列模式當前在派生列、聚合、選擇和接收器轉換中可用。

## <a name="column-patterns-in-derived-column-and-aggregate"></a>衍生欄位與集合模式

要在派生列或聚合變換的聚合選項卡中添加列模式,請單擊現有列右側的加號圖示。 選擇 **「新增欄」模式**。 

![欄模式](media/data-flow/columnpattern.png "資料行模式")

使用[運算式產生器](concepts-data-flow-expression-builder.md)輸入匹配條件。 建立一個布爾運算式,該表示式根據`name`列`type`的`stream``position`列匹配列。 模式將影響條件返回 true 的任何列、漂移或定義。

匹配條件下方的兩個表達式框指定受影響列的新名稱和值。 用於`$$`引用匹配欄位的現有值。 左側運算式框定義名稱,右側表達式框定義值。

![欄模式](media/data-flow/columnpattern2.png "資料行模式")

上述列模式匹配雙精度類型的每一列,併為每個匹配創建一個聚合列。 新列的名稱是匹配列的名稱,與"_total"串聯。 新列的值是現有雙精度值的舍入聚合總和。

要驗證匹配條件是否正確,可以在 **「檢查」** 選項卡中驗證已定義列的輸出架構,或在 **「數據預覽」** 選項卡中獲取數據的快照。 

![欄模式](media/data-flow/columnpattern3.png "資料行模式")

## <a name="rule-based-mapping-in-select-and-sink"></a>選擇與接收器中的對應規則的映射

映射來源中的列並選擇轉換時,可以添加固定對應或基於規則的映射。 根據`name`、、`type``stream`列`position`匹配。 可以具有固定映射和基於規則的映射的任意組合。 默認情況下,所有大於 50 列的預測將預設為基於規則的映射,該映射與每列匹配並輸出輸入的名稱。 

要新增基於規則的映射,請按**下 「新增映射」** 並選擇 **「 標準」**。

![基於規則的對應](media/data-flow/rule2.png "規則型對應")

每個基於規則的映射都需要兩個輸入:要匹配的條件以及每個映射列的名稱。 這兩個值都是通過[表達式生成器](concepts-data-flow-expression-builder.md)輸入的。 在左側運算式框中,輸入布爾匹配條件。 在正確的運算式框中,指定匹配列將映射到的內容。

![基於規則的對應](media/data-flow/rule-based-mapping.png "規則型對應")

使用`$$`語法引用匹配列的輸入名稱。 以上面的圖像為例,假設使用者希望匹配名稱短於六個字的所有字串列。 如果命名`test`了一個傳入欄,則`$$ + '_short'`表示式將重新命名 。`test_short` 如果這是唯一存在的映射,則不符合條件的所有列都將從輸出的數據中刪除。

模式匹配漂移列和定義的欄。 要查看規則映射的已定義的列,請單擊規則旁邊的眼鏡圖示。 使用數據預覽驗證輸出。

### <a name="regex-mapping"></a>雷格克斯對應

如果按一下向下的 V 形圖示,則可以指定正則表達式映射條件。 正規表示式對應條件與匹配指定正規表示式條件的所有列名稱匹配。 這可以與基於規則的標準映射結合使用。

![基於規則的對應](media/data-flow/regex-matching.png "規則型對應")

上述範例與 regex`(r)`模式 或包含小寫 r 的任何列名稱匹配。 與基於標準規則的映射類似,使用`$$`語法使用右側的條件會更改所有匹配列。

### <a name="rule-based-hierarchies"></a>基於規則的層次結構

如果定義的投影具有層次結構,則可以使用基於規則的映射來映射層次結構子列。 指定要映射的匹配條件和要映射的子列的複雜列。 每個匹配的子列都將使用右側指定的"名稱為"規則進行輸出。

![基於規則的對應](media/data-flow/rule-based-hierarchy.png "規則型對應")

上述示例與複雜列的所有子列`a`匹配。 `a`包含兩個子列`b``c`與 。 輸出架構將包括兩欄`b`,`c`並且 「名稱為」`$$`條件為 。

## <a name="pattern-matching-expression-values"></a>模式匹配表達式值。

* `$$`轉換為執行時每個符合項目的名稱或值
* `name`顯示每個傳入欄的名稱
* `type`顯示每個傳入欄的資料型態
* `stream`表示與每個流關聯的名稱或流中的轉換
* `position`是資料串列的位級位置

## <a name="next-steps"></a>後續步驟
* 瞭解有關資料轉換的映射資料串[流 表示式語言](data-flow-expression-functions.md)的更多資訊
* 在[接收器轉換](data-flow-sink.md)中使用列模式,並使用基於規則的對應[選擇轉換](data-flow-select.md)
