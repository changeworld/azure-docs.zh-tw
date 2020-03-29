---
title: 實體類型 - LUIS
titleSuffix: Azure Cognitive Services
description: 實體從話語中提取資料。 實體類型使您能夠預測資料提取。 有兩種類型的實體：機器學習和非機器學習。 請務必瞭解在話語中正在處理的實體類型。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 6ee156efb5512c92d86ba05513b6a2b91df4eae8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221517"
---
# <a name="entities-and-their-purpose-in-luis"></a>實體及其在 LUIS 中的目的

實體的主要目的是為用戶端應用程式提供可預測的資料提取。 _可選_的次要用途是增強對意圖或其他具有描述項的實體的預測。

有兩種類型的實體：

* 機器學習 - 從上下文
* 非機器學習 - 用於預構建實體的精確文本匹配、模式匹配或檢測

機器學習的實體提供最廣泛的資料提取選擇。 非機器學習實體通過文本匹配工作，可以獨立使用，也可以作為機器學習實體[的約束](#design-entities-for-decomposition)。

## <a name="entities-represent-data"></a>實體代表資料

實體是您希望從話語中提取的資料，例如名稱、日期、產品名稱或任何重要的單片語。 一個語句可以包含許多實體，也可以完全不包含實體。 用戶端應用程式_可能需要_資料來執行其任務。

實體需要在模型中每個意圖的所有培訓陳述中一致地標記。

 您可以定義自己的實體或使用預構建的實體來為常見概念（如[datetimeV2、ddinal、](luis-reference-prebuilt-datetimev2.md)[電子郵件](luis-reference-prebuilt-email.md)和[電話號碼](luis-reference-prebuilt-phonenumber.md)）節省時間。 [ordinal](luis-reference-prebuilt-ordinal.md)

|語句|單位|資料|
|--|--|--|
|購買 3 張到紐約的機票|預先建置的號碼<br>Location.Destination|3<br>紐約|
|購買 1 張 3 月 5 日從紐約到倫敦的機票|Location.Origin<br>Location.Destination<br>預先建置的 datetimeV2|紐約<br>London<br>2018 年 3 月 5 日|

### <a name="entities-are-optional"></a>實體是可選的

意圖是必要的，實體則為選擇性。 您不需要為應用程式中的每個概念建立實體，只需針對用戶端應用程式執行動作所需的部分建立實體即可。

如果陳述沒有用戶端應用程式所需的資料，則無需添加實體。 隨著應用程式的發展以及對資料的新需求被確定，您可以稍後向 LUIS 模型添加適當的實體。

## <a name="entity-compared-to-intent"></a>實體與意圖的比較

實體表示要提取的話語中的資料概念。

陳述可以選擇包括實體。 相比之下，_需要_預測話語的意圖，並代表整個陳述。 LUIS 要求示例陳述包含在意圖中。

請考慮以下 4 個表述：

|語句|預測的意圖|提取的實體|說明|
|--|--|--|--|
|説明|help|-|沒有要提取的。|
|發送內容|發送內容|-|沒有要提取的。 尚未訓練模型在此上下文中提取`something`，也沒有收件者。|
|給鮑勃送禮物|發送內容|`Bob`, `present`|模型已使用[人名](luis-reference-prebuilt-person.md)預構建實體進行訓練，該實體已提取名稱`Bob`。 機器學習的實體已用於提取`present`。|
|給鮑勃送一盒巧克力|發送內容|`Bob`, `box of chocolates`|兩個重要資料`Bob`以及`box of chocolates`實體已提取。|

## <a name="design-entities-for-decomposition"></a>用於分解的設計實體

要使頂級實體成為機器學習實體，這是很好的實體設計。 這允許隨著時間的推移更改實體設計，並使用**子元件**（子實體），可以選擇使用**約束**和**描述項**，將頂級實體分解到用戶端應用程式所需的部分。

設計分解允許 LUIS 將深度實體解析返回到用戶端應用程式。 這使用戶端應用程式能夠專注于商務規則，並將資料解析留給 LUIS。

### <a name="machine-learned-entities-are-primary-data-collections"></a>機器學習的實體是主要資料收集

[**機器學習的實體**](tutorial-machine-learned-entity.md)是頂級資料單元。 子元件是機器學習實體的子實體。

機器學習的實體根據通過培訓話語學到的上下文觸發。 **約束**是應用於機器學習實體的可選規則，該實體根據非機器學習實體（如[List](reference-entity-list.md)或[Regex）](reference-entity-regular-expression.md)的精確文本匹配定義進一步限制觸發。 例如，`size`機器學習的實體可以具有`sizeList`清單實體的約束，該約束僅`size`在遇到`sizeList`實體中包含的值時才觸發該實體。

[**描述項**](luis-concept-feature.md)是應用這些功能來提高預測詞或短語的相關性。 它們稱為*描述項，* 因為它們用於*描述*意圖或實體。 描述項描述資料的特徵或屬性，例如 LUIS 觀察和學習的重要單詞或短語。

在 LUIS 應用中創建片語清單功能時，預設情況下將全域啟用該功能，並對所有意圖和實體均勻應用。 但是，如果將片語清單應用為機器學習實體（或*模型*）的描述項（功能），則其範圍將縮小到僅應用於該模型，並且不再與所有其他模型一起使用。 使用片語清單作為模型的描述項有助於分解，説明其應用到的模型的準確性。

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>實體類型

您可以根據擷取資料的方式和資料在擷取之後的呈現方式，來選擇實體。

|實體類型|目的|
|--|--|
|[**機器學習**](tutorial-machine-learned-entity.md)|機器學習的實體在話語中從上下文中學習。 實體的父分組，無論實體類型如何。 這使得示例陳述中放置位置的變化顯著。 |
|[**清單**](reference-entity-list.md)|專案清單及其同義字提取與**確切的文本匹配**。|
|[**模式.任何**](reference-entity-pattern-any.md)|難以判斷實體結尾的實體。 |
|[**預建**](luis-reference-prebuilt-entities.md)|已接受過提取特定類型資料（如 URL 或電子郵件）的培訓。 在開放原始碼 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 專案中已定義部分這些預建實體。 如果目前不支援您的特定文化特性或實體，請向專案提出。|
|[**正則運算式**](reference-entity-regular-expression.md)|使用正則運算式進行**精確文本匹配**。|

## <a name="extracting-contextually-related-data"></a>提取上下文相關的資料

陳述可能包含兩個或多個實體的發生，其中資料的含義基於話語中的上下文。 例如，預訂具有兩個位置的航班（始發地和目的地）的表述。

`Book a flight from Seattle to Cairo`

需要提取實體的兩`location`個示例。 用戶端應用程式需要知道每個位置的類型，以便完成購票。

有兩種技術用於提取上下文相關資料：

 * 實體`location`是機器學習的實體，使用兩個子元件實體捕獲 和`origin``destination`（首選）
 * 實體`location`使用 和**roles** `origin``destination`

多個實體可以存在於一個陳述中，如果使用它們的上下文沒有意義，則可以提取它們而不使用分解或角色。 例如，如果陳述包含位置清單，`I want to travel to Seattle, Cairo, and London.`則，這是一個清單，其中每個專案沒有其他含義。

### <a name="using-subcomponent-entities-of-a-machine-learned-entity-to-define-context"></a>使用機器學習實體的子元件實體定義上下文

您可以使用[**機器學習的實體**](tutorial-machine-learned-entity.md)提取描述預訂航班操作的資料，然後將頂級實體分解為用戶端應用程式所需的單獨部分。

在此示例中，`Book a flight from Seattle to Cairo`可以`travelAction`對頂級實體進行標記以提取`flight from Seattle to Cairo`。 然後創建兩個子元件實體，稱為`origin``destination`和 ，同時應用預構建`geographyV2`實體的約束。 在培訓陳述中，`origin`和`destination`標記適當.

### <a name="using-entity-role-to-define-context"></a>使用實體角色定義上下文

角色是基於話語中上下文的實體的命名別名。 角色可以與任何預先建置或自訂實體型別一起使用，並在範例表達和模式中使用。 在此示例中，`location`實體需要 和 和`origin``destination`兩個角色在示例陳述中標記。

如果 LUIS`location`找到，但無法確定角色，則仍返回位置實體。 用戶端應用程式需要跟進一個問題，以確定使用者的意思位置類型。


## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>如果您所需的實體超出實體數目上限

如果您需要超過限制，請聯繫支援人員。 若要這樣做，請收集您系統的相關詳細資訊、前往 [LUIS](luis-reference-regions.md#luis-website) 網站，然後選取 [Support] \(支援\)****。 如果您的 Azure 訂用帳戶包含支援服務，請與 [Azure 技術支援人員](https://azure.microsoft.com/support/options/)連絡。

## <a name="entity-prediction-status"></a>實體預測狀態

LUIS 門戶顯示實體在示例陳述中何時具有與您選擇的實體不同的實體預測。 此不同分數基於當前訓練的模型。

## <a name="next-steps"></a>後續步驟

了解良好[語句](luis-concept-utterance.md)的相關概念。

請參閱[新增實體](luis-how-to-add-entities.md)，以深入了解如何將實體新增至 LUIS 應用程式。

請參閱[教程：在語言理解 （LUIS） 中使用機器學習實體從使用者陳述中提取結構化資料，](tutorial-machine-learned-entity.md)瞭解如何使用機器學習實體從陳述中提取結構化資料。
 
