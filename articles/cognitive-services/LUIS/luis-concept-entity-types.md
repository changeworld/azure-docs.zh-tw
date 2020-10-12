---
title: 實體類型-LUIS
description: 實體會從預測執行時間的使用者語句中解壓縮資料。 _選擇性_的次要用途是使用實體做為功能，以提高意圖或其他實體的預測。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 398d18642052726af4d4920443bad515ec0b5bef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316557"
---
# <a name="extract-data-with-entities"></a>使用實體將資料解壓縮

實體會從預測執行時間的使用者語句中解壓縮資料。 _選擇性_的次要用途是使用實體做為功能，以提高意圖或其他實體的預測。

有數種類型的實體：

* [機器學習實體](reference-entity-machine-learned-entity.md) -這是主要實體。 在使用其他實體之前，您應該使用此實體類型來設計您的架構。
* 用於作為必要 [功能](luis-concept-feature.md) 的非機器學習-適用于完全相符的文字、模式比對，或由預建實體偵測
* [模式。任何](#patternany-entity)-可將自由格式的文字（例如來自[模式](reference-entity-pattern-any.md)的書籍標題）解壓縮

機器學習實體提供最廣泛的資料提取選項。 非機器學習實體的運作方式是使用文字比對，並作為機器學習實體或意圖的 [必要功能](#design-entities-for-decomposition) 。

## <a name="entities-represent-data"></a>實體代表資料

實體是您想要從語句中提取的資料，例如名稱、日期、產品名稱或任何重要的單字群組。 一個語句可以包含許多實體，也可以完全不包含實體。 用戶端應用程式 _可能_ 需要資料來執行其工作。

在模型中每個意圖的所有定型語句中，都必須以一致的方式標記實體。

 您可以定義自己的實體，或使用預先建立的實體來節省常見概念的時間，例如 [datetimeV2](luis-reference-prebuilt-datetimev2.md)、 [序數](luis-reference-prebuilt-ordinal.md)、 [電子郵件](luis-reference-prebuilt-email.md)和 [電話號碼](luis-reference-prebuilt-phonenumber.md)。

|語句|實體|資料|
|--|--|--|
|購買 3 張到紐約的機票|預先建置的號碼<br>Destination|3<br>紐約|


### <a name="entities-are-optional-but-recommended"></a>實體是選擇性的，但建議使用

雖然需要 [意圖](luis-concept-intent.md) ，但實體是選擇性的。 您不需要為應用程式中的每個概念建立實體，但僅適用于用戶端應用程式需要資料的位置，或是實體作為其他實體或意圖的提示。

當您的應用程式開發和識別新的資料需求時，您稍後可以將適當的實體加入至 LUIS 模型。

<a name="entity-compared-to-intent"></a>

## <a name="entity-represents-data-extraction"></a>實體代表資料解壓縮

實體代表 _語句內_的資料概念。 意圖會分類 _整個語句_。

請考慮下列四個語句：

|語句|預測的意圖|已解壓縮的實體|說明|
|--|--|--|--|
|説明|help|-|沒有可解壓縮的專案。|
|傳送一些內容|sendSomething|-|沒有可解壓縮的專案。 模型沒有必要的功能可 `something` 在此內容中進行解壓縮，且未指定任何收件者。|
|傳送 Bob 存在|sendSomething|`Bob`, `present`|此模型會藉 `Bob` 由加入預建實體的必要功能來進行解壓縮 `personName` 。 已使用機器學習實體進行解壓縮 `present` 。|
|將巧克力的 box 傳送給 Bob|sendSomething|`Bob`, `box of chocolates`|機器學習實體已將兩個重要的資料片段（ `Bob` 和 `box of chocolates` ）解壓縮。|

## <a name="label-entities-in-all-intents"></a>標記所有意圖中的實體

無論預測意圖為何，實體都會將資料解壓縮。 請務必標示所有意圖中的 _所有_ 範例語句。 `None`遺漏實體標記的意圖會造成混淆，即使其他意圖有更多定型語句也一樣。

## <a name="design-entities-for-decomposition"></a>設計分解的實體

機器學習實體可讓您設計要分解的應用程式架構，將大型概念細分為子實體。

針對分解進行設計，可讓 LUIS 將深度的實體解析傳回給用戶端應用程式。 這可讓您的用戶端應用程式專注于商務規則，並將資料解析保留給 LUIS。

機器學習實體會根據透過範例語句學習到的內容而觸發。

[**機器學習實體**](tutorial-machine-learned-entity.md) 是最上層擷取器。 子實體是機器學習服務實體的子實體。

## <a name="effective-machine-learned-entities"></a>有效的機器學習實體

若要有效地建立機器學習實體：

* 您的標籤應該在意圖中保持一致。 這包括您在包含此實體的 **None** 意圖中提供的語句。 否則模型將無法有效判斷順序。
* 如果您有具有子實體的機器學習實體，請確定實體和子實體的不同訂單和變化會顯示在標記的語句中。 標記的範例語句應該包含所有有效的表單，並包含出現且不存在且也會在語句中重新排序的實體。
* 您應該避免將實體過度學習到非常固定的集合。 當模型未妥善一般化時，會發生**過度學習**，而且在機器學習模型中是常見的問題。 這表示應用程式無法適當地處理新資料。 接著，您應該改變加上標籤的範例語句，讓應用程式能夠在您提供的有限範例之外進行一般化。 您應變更不同的子實體，讓模型有足夠的變更，以考慮更多概念，而不是只考慮所顯示的範例。

## <a name="effective-prebuilt-entities"></a>有效的預建實體

若要建立可將一般資料（例如 [預建實體](luis-reference-prebuilt-entities.md)所提供的資料）解壓縮的有效實體，建議您執行下列程式。

藉由將您自己的資料帶入實體做為功能，來改善資料的解壓縮。 如此一來，您資料中的所有其他標籤都會學習應用程式中的人員名稱的內容。

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>實體類型

父系的列應該是機器學習實體。 列可使用非機器學習實體作為 [功能](luis-concept-feature.md)。

您可以根據擷取資料的方式和資料在擷取之後的呈現方式，來選擇實體。

|實體類型|目的|
|--|--|
|[**機器學習**](tutorial-machine-learned-entity.md)|從已加上標籤的範例中，解壓縮已標記的複雜資料。 |
|[**清單**](reference-entity-list.md)|使用 **完全相符文字**進行解壓縮的專案清單及其同義字。|
|[**模式。任何**](#patternany-entity)|找出實體結尾的實體很難判斷，因為實體是自由格式的。 僅適用于 [模式](luis-concept-patterns.md)。|
|[**預建**](luis-reference-prebuilt-entities.md)|已定型以解壓縮特定種類的資料，例如 URL 或電子郵件。 在開放原始碼 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 專案中已定義部分這些預建實體。 如果目前不支援您的特定文化特性或實體，請向專案提出。|
|[**規則運算式**](reference-entity-regular-expression.md)|使用正則運算式進行 **完全相符的文字**。|


## <a name="extraction-versus-resolution"></a>解壓縮與解析

當資料出現在語句中時，實體會將資料解壓縮。 實體不會變更或解析資料。 如果文字是實體的有效值，則實體不會提供任何解析。

有一些方法可將解析帶到解壓縮中，但您應該注意這會限制應用程式免于變化和錯誤的能力。

清單實體和正則運算式 (文字比對) 實體可做為列的 [必要功能](luis-concept-feature.md#required-features) ，並作為解壓縮的篩選準則。 您應謹慎使用這項功能，以防止應用程式進行預測。

## <a name="extracting-contextually-related-data"></a>解壓縮內容相關資料

語句可能包含兩個或多個出現的實體，其中資料的意義是以語句中的內容為基礎。 其中一個範例是預約航班的語句，其中有兩個地理位置，也就是來源和目的地。

`Book a flight from Seattle to Cairo`

這兩個位置必須以用戶端應用程式知道每個位置的類型來進行解壓縮，才能完成票證購買。

若要將來源和目的地解壓縮，請建立兩個子實體作為票證訂單機器學習實體的一部分。 針對每個子實體，建立使用 geographyV2 的必要功能。

<a name="using-component-constraints-to-help-define-entity"></a>
<a name="using-subentity-constraints-to-help-define-entity"></a>

### <a name="using-required-features-to-constrain-entities"></a>使用必要功能來限制實體

深入瞭解 [必要功能](luis-concept-feature.md)

## <a name="patternany-entity"></a>Pattern.any 實體

模式。任何僅適用于 [模式](luis-concept-patterns.md)。

<a name="if-you-need-more-than-the-maximum-number-of-entities"></a>
## <a name="exceeding-app-limits-for-entities"></a>超過實體的應用程式限制

如果您需要超過 [限制](luis-limits.md#model-limits)，請聯絡支援人員。 若要這樣做，請收集您系統的相關詳細資訊、前往 [LUIS](luis-reference-regions.md#luis-website) 網站，然後選取 [Support] \(支援\)****。 如果您的 Azure 訂用帳戶包含支援服務，請與 [Azure 技術支援人員](https://azure.microsoft.com/support/options/)連絡。

## <a name="entity-prediction-status-and-errors"></a>實體預測狀態和錯誤

LUIS 入口網站會顯示當實體的實體預測與您針對範例語句所選取的實體不同時。 此不同分數是以目前定型的模型為基礎。 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="LUIS 入口網站會顯示當實體的實體預測與您針對範例語句所選取的實體不同時。":::

若文字會在 [範例語句] 中反白顯示，而語句線的範例中會有右邊的錯誤指標，顯示為紅色三角形。 

使用此資訊來解決使用下列一或多項的實體錯誤：
* 反白顯示的文字為 mislabeled。 修正、審查、更正和重新定型。 
* 建立實體的 [功能](luis-concept-feature.md) ，以協助識別實體的概念
* 使用實體新增更多 [範例語句](luis-concept-utterance.md) 和標籤
* 針對可協助識別實體概念的預測端點所收到的任何語句，[查看主動式學習建議](luis-concept-review-endpoint-utterances.md)。

## <a name="next-steps"></a>後續步驟

了解良好[語句](luis-concept-utterance.md)的相關概念。

請參閱[新增實體](luis-how-to-add-entities.md)，以深入了解如何將實體新增至 LUIS 應用程式。

請參閱 [教學課程： Language Understanding 中的機器學習實體從使用者語句中解壓縮結構化資料 (LUIS) ](tutorial-machine-learned-entity.md) ，以瞭解如何使用機器學習實體從語句中解壓縮結構化資料。

