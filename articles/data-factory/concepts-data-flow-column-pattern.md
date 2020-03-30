---
title: Azure 資料工廠映射資料流程的列模式
description: 使用 Azure 資料工廠映射資料流程的列模式創建通用資料轉換模式
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: bfb4eeef482c4944e75b7805642bc93c23195208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065526"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>在映射資料流程時使用列模式

多個映射資料流程轉換允許您基於模式引用範本列，而不是硬式編碼列名稱。 此匹配稱為*列模式*。 您可以根據名稱、資料類型、流或位置定義模式以匹配列，而不是要求確切的欄位名稱。 有兩種情況的列模式很有用：

* 如果傳入源欄位經常更改，例如更改文字檔或 NoSQL 資料庫中的列。 此方案稱為[架構漂移](concepts-data-flow-schema-drift.md)。
* 如果要對一組較大的列執行常見操作。 例如，想要將列名稱中"總計"的每個列轉換為雙列。

列模式當前在派生列、聚合、選擇和接收器轉換中可用。

## <a name="column-patterns-in-derived-column-and-aggregate"></a>派生列和聚合中的列模式

要在派生列或聚合變換的聚合選項卡中添加列模式，請按一下現有列右側的加號圖示。 選擇 **"添加列"模式**。 

![列模式](media/data-flow/columnpattern.png "資料行模式")

使用[運算式產生器](concepts-data-flow-expression-builder.md)輸入匹配條件。 創建一個布林運算式，該運算式根據`name`列`type`的`stream`列`position`匹配列。 模式將影響條件返回 true 的任何列、漂移或定義。

匹配條件下方的兩個運算式框指定受影響列的新名稱和值。 用於`$$`引用匹配欄位的現有值。 左側運算式框定義名稱，右側運算式框定義值。

![列模式](media/data-flow/columnpattern2.png "資料行模式")

上述列模式匹配雙精度類型的每一列，並為每個匹配創建一個聚合列。 新列的名稱是匹配列的名稱，與"_total"串聯。 新列的值是現有雙精度值的舍入聚合總和。

要驗證匹配條件是否正確，可以在 **"檢查"** 選項卡中驗證已定義列的輸出架構，或在 **"資料預覽"** 選項卡中獲取資料的快照。 

![列模式](media/data-flow/columnpattern3.png "資料行模式")

## <a name="rule-based-mapping-in-select-and-sink"></a>選擇和接收器中的基於規則的映射

映射源中的列並選擇轉換時，可以添加固定映射或基於規則的映射。 根據`name`、、`type``stream`和`position`列匹配。 可以具有固定映射和基於規則的映射的任意組合。 預設情況下，所有大於 50 列的預測將預設為基於規則的映射，該映射與每列匹配並輸出輸入的名稱。 

要添加基於規則的映射，請按一下 **"添加映射"** 並選擇**基於規則的映射**。

![基於規則的映射](media/data-flow/rule2.png "規則型對應")

每個基於規則的映射都需要兩個輸入：要匹配的條件以及每個映射列的名稱。 這兩個值都是通過[運算式產生器](concepts-data-flow-expression-builder.md)輸入的。 在左側運算式框中，輸入布林匹配條件。 在正確的運算式框中，指定匹配列將映射到的內容。

![基於規則的映射](media/data-flow/rule-based-mapping.png "規則型對應")

使用`$$`語法引用匹配列的輸入名稱。 以上面的圖像為例，假設使用者希望匹配名稱短于六個字元的所有字串列。 如果命名`test`了一個傳入列，則`$$ + '_short'`運算式將重命名該列`test_short`。 如果這是唯一存在的映射，則不符合條件的所有列都將從輸出的資料中刪除。

模式匹配漂移列和定義的列。 要查看規則映射的已定義的列，請按一下規則旁邊的眼鏡圖示。 使用資料預覽驗證輸出。

### <a name="regex-mapping"></a>雷格克斯映射

如果按一下向下的 V 形圖示，則可以指定正則運算式映射條件。 正則運算式映射條件與匹配指定正則運算式條件的所有列名稱匹配。 這可以與基於規則的標準映射結合使用。

![基於規則的映射](media/data-flow/regex-matching.png "規則型對應")

上述示例與 RegEx 模式`(r)`或包含小寫 r 的任何列名稱匹配。 與基於標準規則的映射類似，使用`$$`語法使用右側的條件會更改所有匹配列。

### <a name="rule-based-hierarchies"></a>基於規則的層次結構

如果定義的投影具有層次結構，則可以使用基於規則的映射來映射層次結構子列。 指定要映射的匹配條件和要映射的子列的複雜列。 每個匹配的子列都將使用右側指定的"名稱為"規則進行輸出。

![基於規則的映射](media/data-flow/rule-based-hierarchy.png "規則型對應")

上述示例與複雜列的所有子列`a`匹配。 `a`包含兩個子列`b`和`c`。 輸出架構將包括兩列`b`，並且`c`"名稱為"條件為`$$`。

## <a name="pattern-matching-expression-values"></a>模式匹配運算式值。

* `$$`轉換為運行時每個匹配項的名稱或值
* `name`表示每個傳入列的名稱
* `type`表示每個傳入列的資料類型
* `stream`表示與每個流關聯的名稱，或流中的轉換
* `position`是資料流程中列的位級位置

## <a name="next-steps"></a>後續步驟
* 瞭解有關資料轉換的映射資料流程[運算式語言](data-flow-expression-functions.md)的更多資訊
* 在[接收器轉換](data-flow-sink.md)中使用列模式，並使用基於規則的映射[選擇變換](data-flow-select.md)
