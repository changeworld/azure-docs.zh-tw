---
title: Azure Data Factory 對應資料流程中的資料行模式
description: 使用 Azure Data Factory 對應資料流程中的資料行模式來建立一般化資料轉換模式
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: aacec8830948e08f66d71da88897670f7ef43788
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81606125"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>在對應資料流程中使用資料行模式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

數個對應資料流程轉換可讓您根據模式（而非硬式編碼的資料行名稱）來參考範本資料行。 這種比對稱為「資料*行模式*」。 您可以根據名稱、資料類型、資料流程或位置定義模式來比對資料行，而不需要精確的功能變數名稱。 在兩種情況下，資料行模式很有用：

* 如果傳入來源欄位經常變更（例如變更文字檔或 NoSQL 資料庫中的資料行的案例）。 此案例稱為[架構漂移](concepts-data-flow-schema-drift.md)。
* 如果您想要在大型資料行群組上執行一般作業。 例如，想要將其資料行名稱中具有「總計」的每個資料行轉換成 double。

資料行模式目前可用於 [衍生的資料行]、[匯總]、[選取] 和 [接收] 轉換。

## <a name="column-patterns-in-derived-column-and-aggregate"></a>衍生的資料行和匯總中的資料行模式

若要在匯總轉換的 [衍生的資料行] 或 [匯總] 索引標籤中加入資料行模式，請按一下現有資料行右邊的加號圖示。 選取 [**新增資料行模式**]。 

![資料行模式](media/data-flow/columnpattern.png "資料行模式")

使用[運算式](concepts-data-flow-expression-builder.md)產生器來輸入比對條件。 根據資料 `name` 行的、、和，建立符合資料行的布林運算式 `type` `stream` `position` 。 此模式會影響任何漂移或已定義的資料行，其中條件會傳回 true。

比對條件底下的兩個運算式方塊會指定受影響資料行的新名稱和值。 使用 `$$` 來參考符合欄位的現有值。 左側運算式方塊會定義名稱，右運算式方塊則會定義值。

![資料行模式](media/data-flow/columnpattern2.png "資料行模式")

上述資料行模式會比對 double 類型的每個資料行，並針對每個相符專案建立一個匯總資料行。 新資料行的名稱是與 ' _total ' 串連的相符資料行名稱。 新資料行的值是現有 double 值的已進位匯總總和。

若要確認符合條件是否正確，您可以在 [**檢查**] 索引標籤中驗證已定義資料行的輸出架構，或在 [**資料預覽**] 索引標籤中取得資料的快照集。 

![資料行模式](media/data-flow/columnpattern3.png "資料行模式")

## <a name="rule-based-mapping-in-select-and-sink"></a>Select 和 sink 中以規則為基礎的對應

對應來源中的資料行並選取轉換時，您可以新增固定對應或以規則為基礎的對應。 根據 `name` 、 `type` 、和資料行進行比對 `stream` `position` 。 您可以有固定和以規則為基礎之對應的任意組合。 根據預設，具有大於50資料行的所有投影都會預設為以規則為基礎的對應，這會在每個資料行上符合，並輸出輸入的名稱。 

若要新增以規則為基礎的對應，請按一下 [**新增對應**]，然後選取 [以**規則為主的對應**]。

![以規則為基礎的對應](media/data-flow/rule2.png "規則型對應")

每個以規則為基礎的對應都需要兩個輸入：要比對的條件和每個對應資料行的名稱。 這兩個值都是透過[運算式](concepts-data-flow-expression-builder.md)產生器來輸入。 在 [左側運算式] 方塊中，輸入您的布林值比對條件。 在 [右運算式] 方塊中，指定符合的資料行將對應的目標。

![以規則為基礎的對應](media/data-flow/rule-based-mapping.png "規則型對應")

使用 `$$` 語法來參考相符資料行的輸入名稱。 使用上述影像做為範例，假設使用者想要比對名稱少於六個字元的所有字串資料行。 如果其中一個傳入資料行名為 `test` ，則運算式 `$$ + '_short'` 會重新命名資料行 `test_short` 。 如果這是唯一存在的對應，則不符合條件的所有資料行都會從輸出資料中卸載。

模式同時符合漂移和已定義的資料行。 若要查看哪些定義的資料行是由規則所對應，請按一下規則旁的眼鏡圖示。 使用資料預覽來驗證您的輸出。

### <a name="regex-mapping"></a>Regex 對應

如果您按一下向下的 v 形圖示，就可以指定 RegEx 對應條件。 Regex 對應條件符合所有符合指定 RegEx 條件的資料行名稱。 這可與以標準規則為基礎的對應搭配使用。

![以規則為基礎的對應](media/data-flow/regex-matching.png "規則型對應")

上述範例符合 RegEx 模式 `(r)` 或任何包含小寫 r 的資料行名稱。 與標準規則型對應類似，所有相符的資料行都是由右邊的條件使用語法來改變 `$$` 。

### <a name="rule-based-hierarchies"></a>以規則為基礎的階層

如果您定義的投射具有階層，您可以使用以規則為基礎的對應來對應階層個子。 指定要對應其個子的比對條件和複雜資料行。 每個相符的 subcolumn 都會使用右側指定的 [名稱 as] 規則來輸出。

![以規則為基礎的對應](media/data-flow/rule-based-hierarchy.png "規則型對應")

上述範例符合所有個子的複雜資料行 `a` 。 `a`包含兩個個子 `b` 和 `c` 。 輸出架構會包含兩個數據行 `b` ，而 `c` [名稱為] 條件為 `$$` 。

## <a name="pattern-matching-expression-values"></a>模式比對運算式值。

* `$$`在執行時間轉譯為每個相符項的名稱或值
* `name`代表每個傳入資料行的名稱
* `type`代表每個傳入資料行的資料類型
* `stream`表示與每個資料流程相關聯的名稱，或您流程中的轉換
* `position`這是資料流程中資料行的序數位置

## <a name="next-steps"></a>後續步驟
* 深入瞭解資料轉換的對應資料流程[運算式語言](data-flow-expression-functions.md)
* 使用[接收轉換](data-flow-sink.md)中的資料行模式，並選取 [使用以規則為基礎的對應進行[轉換](data-flow-select.md)]
