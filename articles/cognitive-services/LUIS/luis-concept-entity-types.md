---
title: 實體類型-LUIS
description: 實體會在預測執行時間從使用者語句中提取資料。 _選擇性_的次要用途是使用實體做為功能來提升意圖或其他實體的預測。
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 8751bdd52bb1c3738103dc074184a3cf72bfeb09
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88207729"
---
# <a name="extract-data-with-entities"></a>使用實體來解壓縮資料

實體會在預測執行時間從使用者語句中提取資料。 _選擇性_的次要用途是使用實體做為功能來提升意圖或其他實體的預測。

實體有幾種類型：

* [機器學習服務實體](reference-entity-machine-learned-entity.md) -這是主要實體。 使用其他實體之前，您應該使用此實體類型來設計架構。
* 非機器學慣用來做為必要 [功能](luis-concept-feature.md) -針對完全相符的文字、模式比對，或由預先建立的實體偵測
* [Pattern. any](#patternany-entity) -從[模式](reference-entity-pattern-any.md)中將自由格式的文字（例如書籍標題）解壓縮

機器學習服務實體提供最廣泛的資料提取選擇。 非機器學習實體會依照文字比對來執行，並作為機器學習實體或意圖的 [必要功能](#design-entities-for-decomposition) 。

## <a name="entities-represent-data"></a>實體代表資料

實體是您想要從語句中提取的資料，例如名稱、日期、產品名稱或任何重要的字組。 一個語句可以包含許多實體，也可以完全不包含實體。 用戶端應用程式 _可能_ 需要資料來執行其工作。

實體必須針對模型中的每個意圖，以一致的方式在所有定型語句中標示。

 您可以定義自己的實體，或使用預先建立的實體來節省一般概念（例如 [datetimeV2](luis-reference-prebuilt-datetimev2.md)、 [序數](luis-reference-prebuilt-ordinal.md)、 [電子郵件](luis-reference-prebuilt-email.md)和 [電話號碼](luis-reference-prebuilt-phonenumber.md)）的時間。

|語句|單位|資料|
|--|--|--|
|購買 3 張到紐約的機票|預先建置的號碼<br>Destination|3<br>紐約|


### <a name="entities-are-optional-but-recommended"></a>實體是選擇性的，但建議使用

雖然 [意圖](luis-concept-intent.md) 是必要的，但實體是選擇性的。 您不需要為應用程式中的每個概念建立實體，只適用于用戶端應用程式需要資料或實體作為另一個實體或意圖之提示或信號的使用者。

當您的應用程式開發和識別出新的資料需求時，您可以稍後再將適當的實體新增至您的 LUIS 模型。

<a name="entity-compared-to-intent"></a>

## <a name="entity-represents-data-extraction"></a>實體代表資料提取

實體代表 _語句內_的資料概念。 意圖會分類 _整個語句_。

請考慮下列四個語句：

|語句|預測的意圖|已解壓縮的實體|說明|
|--|--|--|--|
|説明|help|-|沒有要解壓縮的內容。|
|傳送內容|sendSomething|-|沒有要解壓縮的內容。 此模型在此內容中沒有要解壓縮的必要功能 `something` ，而且沒有任何指定的收件者。|
|傳送目前的 Bob|sendSomething|`Bob`, `present`|模型會藉 `Bob` 由加入預建實體的必要功能來解壓縮 `personName` 。 已使用機器學習實體來解壓縮 `present` 。|
|將巧克力的方區塊轉送給 Bob|sendSomething|`Bob`, `box of chocolates`|機器學習實體已將兩個重要的資料片段（ `Bob` 和 `box of chocolates` ）解壓縮。|

## <a name="label-entities-in-all-intents"></a>為所有意圖中的實體加上標籤

不論預測的意圖為何，實體都會解壓縮資料。 請務必標記所有意圖中的 _所有_ 範例語句。 `None`遺漏實體標記的意圖會造成混淆，即使其他意圖的定型語句較多。

## <a name="design-entities-for-decomposition"></a>設計用於分解的實體

機器學習服務實體可讓您設計應用程式架構以進行分解，將大型概念分解成子實體。

分解的設計可讓 LUIS 將深度的實體解析傳回給您的用戶端應用程式。 這可讓您的用戶端應用程式專注于商務規則，並將資料解析留給 LUIS。

機器學習實體會根據透過範例語句所學習到的內容觸發。

[**機器學習實體**](tutorial-machine-learned-entity.md) 是頂層擷取器。 子實體是機器學習實體的子系實體。

## <a name="effective-machine-learned-entities"></a>有效的機器學習實體

若要有效率地建立機器學習的實體：

* 您的標籤在意圖中應該一致。 這包括您在包含此實體的 **None** 意圖中提供的語句。 否則，此模型將無法有效地判斷序列。
* 如果您有機器學習的實體具有子實體，請確定實體和子實體的不同訂單和變體會顯示在標示的語句中。 標記的範例語句應包含所有有效的表單，並包含出現且不存在的實體，而且也會在語句內重新排序。
* 您應該避免將實體過度學習到非常固定的集合。 當模型未妥善一般化，且在機器學習模型中是常見的問題時，就會發生**過度學習**。 這表示應用程式無法適當地處理新資料。 接著，您應該改變加上標籤的範例語句，讓應用程式能夠一般化超過您所提供的有限範例。 您應該改變不同的子實體與模型的足夠變更，以考慮更多概念，而不只是所示的範例。

## <a name="effective-prebuilt-entities"></a>有效的預建實體

若要建立可將通用資料（例如預先建立的 [實體](luis-reference-prebuilt-entities.md)所提供的）解壓縮的有效實體，我們建議您執行下列程式。

藉由將您自己的資料帶入實體做為功能，來改善資料的提取。 如此一來，您資料中的所有額外標籤就會瞭解人員名稱在應用程式中的存在內容。

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>實體類型

父系的列應該是機器學習實體。 列可以使用非機器學習實體做為 [功能](luis-concept-feature.md)。

您可以根據擷取資料的方式和資料在擷取之後的呈現方式，來選擇實體。

|實體類型|目的|
|--|--|
|[**機器學習**](tutorial-machine-learned-entity.md)|從已加上標籤的範例中，解壓縮學習的複雜資料。 |
|[**名單**](reference-entity-list.md)|以 **完全相符文字**解壓縮的專案及其同義字清單。|
|[**Pattern。**](#patternany-entity)|找不到實體結尾的實體很容易判斷，因為實體是自由形式的。 僅適用于 [模式](luis-concept-patterns.md)。|
|[**預建**](luis-reference-prebuilt-entities.md)|已定型，可將特定類型的資料（例如 URL 或電子郵件）解壓縮。 在開放原始碼 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 專案中已定義部分這些預建實體。 如果目前不支援您的特定文化特性或實體，請向專案提出。|
|[**規則運算式**](reference-entity-regular-expression.md)|會使用正則運算式來比對 **完全相符的文字**。|


## <a name="extraction-versus-resolution"></a>提取與解析

當資料出現在語句中時，實體會將資料解壓縮。 實體不會變更或解析資料。 如果文字是實體的有效值，實體將不會提供任何解析。

有一些方法可以將解決方案帶入解壓縮中，但您應該要注意，這會限制應用程式不受變化和錯誤影響的能力。

列出實體和正則運算式 (文字對應) 實體可用來做為列的 [必要功能](luis-concept-feature.md#required-features) ，並做為提取的篩選準則。 您應該小心使用這一點，不要妨礙應用程式的預測能力。

## <a name="extracting-contextually-related-data"></a>解壓縮內容相關資料

語句可能包含兩個或更多出現的實體，其中資料的意義是根據語句內的內容。 例如，有兩個地理位置、來源和目的地的航班預約語句。

`Book a flight from Seattle to Cairo`

這兩個位置必須以用戶端應用程式知道每個位置的類型，才能完成票證購買的方式進行解壓縮。

若要將來源和目的地解壓縮，請建立兩個子實體作為票證訂單機器學習實體的一部分。 針對每個子實體，建立使用 geographyV2 的必要功能。

<a name="using-component-constraints-to-help-define-entity"></a>
<a name="using-subentity-constraints-to-help-define-entity"></a>

### <a name="using-required-features-to-constrain-entities"></a>使用必要功能來限制實體

深入瞭解 [必要功能](luis-concept-feature.md)

## <a name="patternany-entity"></a>Pattern.any 實體

模式。任何只能在 [模式](luis-concept-patterns.md)中使用。

<a name="if-you-need-more-than-the-maximum-number-of-entities"></a>
## <a name="exceeding-app-limits-for-entities"></a>超過實體的應用程式限制

如果您需要超過 [限制](luis-limits.md#model-limits)，請聯絡支援人員。 若要這樣做，請收集您系統的相關詳細資訊、前往 [LUIS](luis-reference-regions.md#luis-website) 網站，然後選取 [Support] \(支援\)****。 如果您的 Azure 訂用帳戶包含支援服務，請與 [Azure 技術支援人員](https://azure.microsoft.com/support/options/)連絡。

## <a name="entity-prediction-status-and-errors"></a>實體預測狀態和錯誤

當實體的實體預測與您為範例語句所選取的實體不同時，LUIS 入口網站就會顯示。 這種不同的分數是以目前定型的模型為基礎。 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="當實體的實體預測與您為範例語句所選取的實體不同時，LUIS 入口網站就會顯示。":::

若文字會在範例語句內反白顯示，而範例語句行會在右側出現錯誤指示器，顯示為紅色三角形。 

使用這項資訊來解決實體錯誤，使用下列一或多個專案：
* 反白顯示的文字為標記錯誤。 修正、審查、更正和重新定型。 
* 建立實體的 [功能](luis-concept-feature.md) 以協助識別實體的概念
* 使用實體新增更多 [範例語句](luis-concept-utterance.md) 和標籤
* 請參閱預測端點上所收到之任何語句的[主動式學習建議](luis-concept-review-endpoint-utterances.md)，可協助識別實體的概念。

## <a name="next-steps"></a>後續步驟

了解良好[語句](luis-concept-utterance.md)的相關概念。

請參閱[新增實體](luis-how-to-add-entities.md)，以深入了解如何將實體新增至 LUIS 應用程式。

請參閱 [教學課程：在 Language Understanding 中使用機器學習服務實體將結構化資料從使用者語句中取出 (LUIS) ](tutorial-machine-learned-entity.md) ，以瞭解如何使用機器學習服務實體從語句中解壓縮結構化資料。

