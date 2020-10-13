---
title: Azure Data Factory 對應資料流程中的資料行模式
description: 在 Azure Data Factory 對應資料流程中使用資料行模式建立一般化資料轉換模式
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/16/2020
ms.openlocfilehash: 74656401d7b0ef12cf509674921a6a5153ce992d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91282913"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>在對應資料流程中使用資料行模式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

許多對應的資料流程轉換可讓您根據模式參考樣板資料行，而不是以硬式編碼的資料行名稱為基礎。 這種比對稱為資料 *行模式*。 您可以根據名稱、資料類型、資料流程、來源或位置來定義要比對資料行的模式，而不需要完全相同的功能變數名稱。 有兩種案例可以用來提供資料行模式：

* 如果傳入來源欄位經常變更，例如變更文字檔或 NoSQL 資料庫中的資料行。 此案例稱為 [架構漂移](concepts-data-flow-schema-drift.md)。
* 如果您想要對大型資料行群組進行一般作業。 例如，想要將其資料行名稱中有 ' total ' 的每個資料行轉換成雙精度浮點數。

資料行模式目前可用於衍生的資料行、匯總、選取和接收轉換。

## <a name="column-patterns-in-derived-column-and-aggregate"></a>衍生的資料行和匯總中的資料行模式

若要在「衍生的資料行」、「匯總」或「視窗」轉換中加入資料行模式，請按一下資料行清單上方的 [ **加入** ]，或現有衍生資料行旁的加號圖示。 選擇 [ **加入資料行模式**]。

![資料行模式](media/data-flow/add-column-pattern.png "資料行模式")

使用 [運算式](concepts-data-flow-expression-builder.md) 產生器來輸入比對條件。 根據資料 `name` 行的、 `type` 、 `stream` 、 `origin` 和，建立符合資料行的布林運算式 `position` 。 此模式會影響任何漂移或定義的資料行，其中條件會傳回 true。

比對條件下的兩個運算式方塊會指定受影響之資料行的新名稱和值。 用 `$$` 來參考相符欄位的現有值。 左運算式方塊會定義名稱，而右邊運算式方塊會定義值。

![資料行模式](media/data-flow/edit-column-pattern.png "資料行模式")

上述資料行模式會比對 double 類型的每個資料行，並建立每個相符專案的一個衍生資料行。 藉由將命名 `$$` 為 [資料行名稱] 欄位，就會使用相同的名稱更新每個相符的資料行。 每個資料行的值都是四捨五入為兩個小數點的現有值。

若要驗證符合條件是否正確，您可以在 [ **檢查** ] 索引標籤中驗證已定義之資料行的輸出架構，或在 [ **資料預覽** ] 索引標籤中取得資料的快照集。 

![資料行模式](media/data-flow/columnpattern3.png "資料行模式")

## <a name="rule-based-mapping-in-select-and-sink"></a>Select 和 sink 中以規則為基礎的對應

對應來源和選取轉換中的資料行時，您可以加入固定對應或以規則為基礎的對應。 根據、、、 `name` `type` 和資料 `stream` `origin` 行進行 `position` 比對。 您可以使用固定和以規則為基礎的對應組合。 依預設，具有大於50資料行的所有投影都會預設為以規則為基礎的對應，以符合每個資料行，並輸出輸入的名稱。 

若要加入以規則為基礎的對應，請按一下 [ **新增對應** ]，然後選取 [ **規則型對應**]。

![以規則為基礎的對應](media/data-flow/rule2.png "規則型對應")

每個以規則為基礎的對應都需要兩個輸入：要比對的條件，以及命名每個對應資料行的名稱。 這兩個值都是透過 [運算式](concepts-data-flow-expression-builder.md)產生器來輸入的。 在 [左運算式] 方塊中，輸入您的布林值比對條件。 在 [右運算式] 方塊中，指定要對應的相符資料行。

![以規則為基礎的對應](media/data-flow/rule-based-mapping.png "規則型對應")

使用 `$$` 語法來參考相符資料行的輸入名稱。 使用上述影像作為範例，假設使用者想要比對名稱少於六個字元的所有字串資料行。 如果有一個傳入的資料行命名為 `test` ，運算式 `$$ + '_short'` 就會將資料行重新命名 `test_short` 。 如果這是唯一存在的對應，就會從輸出資料中卸載不符合條件的所有資料行。

模式符合漂移和定義的資料行。 若要查看規則所對應的定義資料行，請按一下規則旁邊的 [眼鏡] 圖示。 使用資料預覽來驗證您的輸出。

### <a name="regex-mapping"></a>Regex 對應

如果您按一下向下箭號圖示，則可以指定 RegEx 對應條件。 Regex 對應條件符合所有符合指定之 RegEx 條件的資料行名稱。 這可以與標準規則型對應搭配使用。

![以規則為基礎的對應](media/data-flow/regex-matching.png "規則型對應")

上述範例符合 RegEx 模式 `(r)` 或包含小寫 r 的任何資料行名稱。 類似于標準規則型對應，所有相符的資料行都是使用語法來改變右邊的條件 `$$` 。

### <a name="rule-based-hierarchies"></a>以規則為基礎的階層

如果您定義的投射具有階層，您可以使用以規則為基礎的對應來對應階層個子。 指定要對應其個子的比對條件和複雜資料行。 每個相符的 subcolumn 都會使用右側指定的「名稱即」規則進行輸出。

![以規則為基礎的對應](media/data-flow/rule-based-hierarchy.png "規則型對應")

上述範例符合複雜資料行的所有個子 `a` 。 `a` 包含兩個個子 `b` 和 `c` 。 輸出架構會包含兩個數據行 `b` ，而「 `c` 名稱」條件為 `$$` 。

## <a name="pattern-matching-expression-values"></a>模式比對運算式值。

* `$$` 在執行時間轉譯為每個相符項的名稱或值
* `name` 表示每個傳入資料行的名稱。
* `type` 表示每個傳入資料行的資料類型
* `stream` 代表與您流程中的每個資料流程或轉換相關聯的名稱
* `position` 是資料流程中資料行的序數位置。
* `origin` 這是產生或上次更新資料行時的轉換

## <a name="next-steps"></a>後續步驟
* 深入瞭解資料轉換的對應資料流程[運算式語言](data-flow-expression-functions.md)
* 在[接收轉換](data-flow-sink.md)中使用資料行模式，並使用以規則為基礎的對應[選取轉換](data-flow-select.md)
