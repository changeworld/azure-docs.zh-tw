---
title: 實體類型-LUIS
titleSuffix: Azure Cognitive Services
description: 實體會從語句中解壓縮資料。 實體類型可讓您進行可預測的資料提取。 實體有兩種類型：機器學習和非機器學習。 請務必知道您在語句中使用哪一種類型的實體。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 808e110ccb45b0b4f7bf34a43597c1f7a7bc0fed
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422574"
---
# <a name="entities-and-their-purpose-in-luis"></a>實體及其在 LUIS 中的用途

實體的主要用途是提供用戶端應用程式可預測的資料提取。 _選擇性_的次要目的是要使用描述項來提升意圖或其他實體的預測。

實體有兩種類型：

* 機器學習-從內容
* 非機器學習-針對完全相符的文字、模式比對，或由預先建立的實體偵測

機器學習的實體提供最廣泛的資料提取選擇。 非機器學習的實體會透過文字比對來進行處理，而且可以獨立使用，或作為機器學習實體的[條件約束](#design-entities-for-decomposition)。

## <a name="entities-represent-data"></a>實體代表資料

實體是您想要從語句中提取的資料，例如名稱、日期、產品名稱或任何重要的字組。 一個語句可以包含許多實體，也可以完全不包含實體。 用戶端應用程式_可能_需要資料來執行其工作。

實體必須針對模型中的每個意圖，以一致的方式在所有定型語句中標示。

 您可以定義自己的實體，或使用預先建立的實體來節省一般概念（例如[datetimeV2](luis-reference-prebuilt-datetimev2.md)、[序數](luis-reference-prebuilt-ordinal.md)、[電子郵件](luis-reference-prebuilt-email.md)和[電話號碼](luis-reference-prebuilt-phonenumber.md)）的時間。

|語句|單位|資料|
|--|--|--|
|購買 3 張到紐約的機票|預先建置的號碼<br>Location.Destination|3<br>紐約|
|購買 1 張 3 月 5 日從紐約到倫敦的機票|Location.Origin<br>Location.Destination<br>預先建置的 datetimeV2|紐約<br>倫敦<br>2018 年 3 月 5 日|

### <a name="entities-are-optional"></a>實體是選擇性的

意圖是必要的，實體則為選擇性。 您不需要為應用程式中的每個概念建立實體，只需針對用戶端應用程式執行動作所需的部分建立實體即可。

如果您的語句沒有用戶端應用程式所需的資料，您就不需要新增實體。 當您的應用程式開發和識別出新的資料需求時，您可以稍後再將適當的實體新增至您的 LUIS 模型。

## <a name="entity-compared-to-intent"></a>實體與意圖的比較

實體代表您要解壓縮之語句內的資料概念。

語句可以選擇性地包含實體。 相較之下，語句的意圖預測是_必要_的，代表整個語句。 LUIS 需要意圖中包含範例語句。

請考慮下列4語句：

|語句|預測的意圖|已解壓縮的實體|說明|
|--|--|--|--|
|説明|help|-|沒有要解壓縮的內容。|
|傳送內容|sendSomething|-|沒有要解壓縮的內容。 尚未訓練此模型在此內容中解壓縮 `something`，也沒有任何收件者。|
|傳送目前的 Bob|sendSomething|`Bob`，`present`|已使用[personName](luis-reference-prebuilt-person.md)預先建立的實體（已將名稱 `Bob`解壓縮）來定型模型。 已使用機器學習的實體來解壓縮 `present`。|
|將巧克力的方區塊轉送給 Bob|sendSomething|`Bob`，`box of chocolates`|實體已將兩個重要的資料片段（`Bob` 和 `box of chocolates`）解壓縮。|

## <a name="design-entities-for-decomposition"></a>設計用於分解的實體

這是很好的實體設計，讓您的頂層實體成為機器學習的實體。 這可讓您在一段時間內變更實體**設計，並**選擇性**地使用** **子元件**（子實體）來將最上層實體分解成用戶端應用程式所需的元件。

分解的設計可讓 LUIS 將深度的實體解析傳回給您的用戶端應用程式。 這可讓您的用戶端應用程式專注于商務規則，並將資料解析留給 LUIS。

### <a name="machine-learned-entities-are-primary-data-collections"></a>機器學習的實體是主要資料集合

[**機器學習的實體**](tutorial-machine-learned-entity.md)是最上層的資料單位。 子元件是機器學習實體的子實體。

機器學習的實體會根據透過定型語句學習到的內容觸發。 **條件約束**是套用至機器學習實體的選擇性規則，會根據非機器學習的實體（例如[清單](reference-entity-list.md)或[Regex](reference-entity-regular-expression.md)）的精確文字比對定義，進一步限制觸發。 例如，`size` 機器學習的實體可以有 `sizeList` 清單實體的條件約束，限制只有在遇到 `sizeList` 實體內所包含的值時，才會觸發 `size` 實體。

[**描述**](luis-concept-feature.md)項是為了提升預測之單字或片語的相關性而套用的功能。 因為它們是用來*描述*意圖或實體，所以稱為 *「描述項*」。 描述元會說明資料的特性或屬性，例如重要的單字或片語。 該 LUIS 會觀察並學習。

當您在 LUIS 應用程式中建立片語清單功能時，預設會全域啟用，並會在所有意圖和實體之間平均套用。 不過，如果您將片語清單套用為機器學習實體（或*模型*）的描述項（功能），則其範圍會縮減為只套用至該模型，而且不再與所有其他模型搭配使用。 使用片語清單做為模型的描述項有助於分解，方法是協助其適用之模型的精確度。

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
|[**機器學習**](tutorial-machine-learned-entity.md)|機器學習實體會從語句中的內容學習。 實體的父群組，不論實體類型為何。 這會使位置變得語句顯著。 |
|[**清單**](reference-entity-list.md)|以**完全相符文字**解壓縮的專案及其同義字清單。|
|[**Pattern.any**](reference-entity-pattern-any.md)|難以判斷實體結尾的實體。 |
|[**預建**](luis-reference-prebuilt-entities.md)|已定型，可將特定類型的資料（例如 URL 或電子郵件）解壓縮。 在開放原始碼 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 專案中已定義部分這些預建實體。 如果目前不支援您的特定文化特性或實體，請向專案提出。|
|[**規則運算式**](reference-entity-regular-expression.md)|會使用正則運算式來比對**完全相符的文字**。|

## <a name="extracting-contextually-related-data"></a>解壓縮內容相關資料

語句可能包含兩個或更多出現的實體，其中資料的意義是根據語句內的內容。 例如，有兩個位置、來源和目的地的航班預約語句。

`Book a flight from Seattle to Cairo`

需要解壓縮 `location` 實體的兩個範例。 用戶端應用程式必須知道每個的位置類型，才能完成票證購買。

有兩種方法可以用來解壓縮內容相關資料：

 * `location` 實體是機器學習的實體，並使用兩個子元件實體來捕捉 `origin` 和 `destination` （慣用）
 * `location` 實體會使用 `origin` 和的兩個**角色**`destination`

語句中可以有多個實體，而且如果使用的內容沒有任何意義，就可以不使用分解或角色來解壓縮。 例如，如果語句包含位置的清單，`I want to travel to Seattle, Cairo, and London.`，這就是一個清單，其中的每個專案都不會有其他意義。

### <a name="using-subcomponent-entities-of-a-machine-learned-entity-to-define-context"></a>使用機器學習實體的子元件實體來定義內容

您可以使用[**機器學習的實體**](tutorial-machine-learned-entity.md)來解壓縮描述預訂航班動作的資料，然後將最上層實體分解成用戶端應用程式所需的個別元件。

在此範例中 `Book a flight from Seattle to Cairo`，您可以 `travelAction` 最上層實體並加上標籤，以將 `flight from Seattle to Cairo`解壓縮。 接著會建立兩個子元件實體（稱為 `origin` 和 `destination`），並將條件約束套用到預先建立的 `geographyV2` 實體。 在 [定型] 語句中，`origin` 和 `destination` 會適當地加上標籤。

### <a name="using-entity-role-to-define-context"></a>使用實體角色來定義內容

角色是以語句內的內容為基礎之實體的名稱別名。 角色可以與任何預先建置或自訂實體型別一起使用，並在範例表達和模式中使用。 在此範例中，`location` 實體需要兩個 `origin` 和 `destination` 角色，而且兩者都需要在範例語句中標示。

如果 LUIS 找到 `location`，但無法判斷角色，則仍會傳回 location 實體。 用戶端應用程式需要追蹤問題，以判斷使用者所代表的位置類型。


## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>如果您所需的實體超出實體數目上限

如果您需要超過限制，請聯絡支援人員。 若要這樣做，請收集關於您系統的詳細資訊、前往 [LUIS](luis-reference-regions.md#luis-website) 網站，然後選取 [支援]。 如果您的 Azure 訂用帳戶包含支援服務，請與 [Azure 技術支援人員](https://azure.microsoft.com/support/options/)連絡。

## <a name="entity-prediction-status"></a>實體預測狀態

LUIS 入口網站會顯示實體（在範例中為語句）與您所選取的實體具有不同的實體預測。 這種不同的分數是以目前定型的模型為基礎。

## <a name="next-steps"></a>後續步驟

了解良好[語句](luis-concept-utterance.md)的相關概念。

請參閱[新增實體](luis-how-to-add-entities.md)，以深入了解如何將實體新增至 LUIS 應用程式。

請參閱[教學課程：在 Language Understanding （LUIS）中使用機器學習的實體從使用者語句解壓縮結構化資料](tutorial-machine-learned-entity.md)，以瞭解如何使用機器學習的實體從語句中解壓縮結構化資料。
