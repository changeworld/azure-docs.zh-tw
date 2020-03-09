---
title: 良好的範例語句-LUIS
titleSuffix: Azure Cognitive Services
description: 語句是應用程式需要解譯的使用者輸入。 收集您認為使用者會輸入的片語。 納入意義相同但以不同單字長度和單字位置建構的語句。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: diberry
ms.openlocfilehash: 7412677773b60a1894a6ece7251e797bfddee091
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396936"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>了解適合您 LUIS 應用程式的語句

**語句**是應用程式需要解譯的使用者輸入。 若要將 LUIS 定型以從中擷取意圖和實體，務必要針對每個意圖擷取各種不同的範例語句。 主動學習或持續將新語句定型的程序，對 LUIS 提供的機器學習智慧非常重要。

收集您認為使用者會輸入的語句。 包括意義相同但結構不同的語句：

* 語句長度 - 適用於您用戶端應用程式的短、中和長句子
* 字組與片語長度 
* 字組位置 - 位於語句開頭、中間與結尾的實體
* 文法 
* 複數表示
* 詞幹分析
* 名詞和動詞的選擇
* 標點符號 - 使用各種正確、不正確和沒有文法的表示方式

## <a name="how-to-choose-varied-utterances"></a>如何選擇各種語句

當您剛開始為 LUIS 模型[新增範例語句](luis-how-to-add-example-utterances.md)時，請將以下幾個原則謹記在心。

### <a name="utterances-arent-always-well-formed"></a>語句不見得格式正確

它可能是一個句子，如「為我預訂飛往巴黎的機票」，或句子片段如「預訂」或「巴黎的班機」。  使用者常發生拼字錯誤。 規劃應用程式時，請考慮是否要先使用 [Bing 拼字檢查](luis-tutorial-bing-spellcheck.md)來更正使用者輸入，再將其傳遞至 LUIS。 

如果未檢查使用者語句的拼字，您應該針對包含錯字與拼字錯誤的語句將 LUIS 定型。

### <a name="use-the-representative-language-of-the-user"></a>使用使用者的代表語言

選擇語句時，請注意您認為是常見的字詞或片語，對於用戶端應用程式的一般使用者而言可能並不正確。 他們可能不具領域經驗。 使用只有當使用者是專家時才會用的字詞與片語時，請小心謹慎。

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>選擇不同的詞彙以及片語

您會發現即使努力建立各種的句子模式，有些詞彙仍會不斷重複。

以下面這些範例語句為例：

|範例語句|
|--|
|如何取得電腦？|
|何處取得電腦？|
|我想要取得電腦，如何著手呢？|
|我何時可擁有電腦？| 

這裡的核心詞彙「電腦」並不會有任何不同。 可使用桌上型電腦、膝上型電腦、工作站，甚至只稱為機器來替代。 LUIS 可以智慧地從內容推斷同義字，但當您建立定型的語句時，一定會改變它們。

## <a name="example-utterances-in-each-intent"></a>每個意圖的範例語句

每個意圖都必須至少要有 15 個範例語句。 如果是沒有任何範例語句的意圖，則無法將 LUIS 定型。 如果您有一或多個範例語句的意圖，LUIS 可能無法精確預測意圖。 

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>對每個製作反覆項目新增多個 15 個語句構成的群組

請不要在模型的每個反覆項目中新增大量語句。 新增 15 個語句。 再次[定型](luis-how-to-train.md)、[發佈](luis-how-to-publish-app.md)及[測試](luis-interactive-test.md)。  

LUIS 會利用由 LUIS 模型建立者精挑細選的語句來建置有效的模型。 新增太多語句只會導致產生混淆，並沒有用。

最好從少量語句開始，然後[檢閱端點語句](luis-how-to-review-endpoint-utterances.md)，以正確地預測意圖和擷取實體。

## <a name="utterance-normalization"></a>語句正規化

語句正規化是在定型和預測期間忽略標點符號和變音符號效果的程式。

## <a name="utterance-normalization-for-diacritics-and-punctuation"></a>變音符號和標點符號的語句正規化

當您建立或匯入應用程式時，會定義語句正規化，因為它是應用程式 JSON 檔案中的設定。 預設會關閉語句正規化設定。 

變音符號是文字中的標記或符號，例如： 

```
İ ı Ş Ğ ş ğ ö ü
```

如果您的應用程式開啟正規化，則在 [**測試**] 窗格中，批次測試和端點查詢的分數會針對所有使用變音符號或標點符號的語句進行變更。

在 `settings` 參數中，為您的 LUIS JSON 應用程式檔開啟變音符號或標點符號的語句正規化。

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"},
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

正規化**標點符號**表示在您的模型經過定型之前，在您的端點查詢預測之前，會從語句中移除標點符號。 

正規化**變音符號**會以一般字元，在語句中以變音符號取代字元。 例如： `Je parle français` 會變成 `Je parle francais`。 

正規化並不表示您不會在範例語句或預測回應中看到標點符號和變音符號，只是在定型和預測期間會忽略它們。


### <a name="punctuation-marks"></a>標點符號

標點符號在 LUIS 中是個別的語彙基元。 在結尾處包含句號的語句，以及在結尾不包含句號的語句是兩個不同的語句，而且可能會得到兩個不同的預測。 

如果標點符號不正規化，LUIS 預設不會忽略標點符號，因為有些用戶端應用程式可能會對這些標記有重要性。 請確定您的範例語句應有使用標點符號和不使用標點符號兩種版本，讓這兩種樣式傳回相同的相對分數。 

請確定模型會在範例語句（包含和不含標點符號）中處理標點符號，或在[模式](luis-concept-patterns.md)中，使用特殊語法更容易忽略標點符號： `I am applying for the {Job} position[.]`

如果標點符號在您的用戶端應用程式中沒有特定意義，請考慮以正規化標點符號來[忽略標點符號](#utterance-normalization)。 

### <a name="ignoring-words-and-punctuation"></a>忽略單字和標點符號

如果您想要忽略模式中的特定單字或標點符號，請搭配使用[模式](luis-concept-patterns.md#pattern-syntax)與_略_過方括弧的語法，`[]`。 

## <a name="training-utterances"></a>將語句定型

定型通常不具確定性：版本或應用程式間的語句預測會稍微不同。 您可以更新[版本設定](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API，以 `UseAllTrainingData`名稱/值配對來使用所有定型資料，藉此移除非確定性的定型。

## <a name="testing-utterances"></a>測試語句 

開發人員應該透過將語句傳送至[預測端點](luis-how-to-azure-subscription.md) URL，以實際流量測試其 LUIS 應用程式。 這些語句可用來改善使用[檢閱語句](luis-how-to-review-endpoint-utterances.md)的意圖和實體效能。 使用 LUIS 網站測試窗格提交的測試，不會透過端點傳送，因此也不會提供給主動學習。 

## <a name="review-utterances"></a>檢閱語句

在將模型定型、發佈及接收[端點](luis-glossary.md#endpoint)查詢之後，請[檢閱 LUIS 所建議的語句](luis-how-to-review-endpoint-utterances.md)。 LUIS 會選取對意圖或實體而言分數低的端點語句。 

## <a name="best-practices"></a>最佳作法

檢閱[最佳做法](luis-concept-best-practices.md)，並將其套用為一般撰寫週期的一部分。

## <a name="label-for-word-meaning"></a>單字意義的標籤

如果單字選擇或單字排列相同，但意義不同，請勿以實體標記它。 

下列語句中的 `fair` 一字是一個同形異義字。 其拼字相同，但意義不同：

|語句|
|--|
|What kind of county fairs are happening in the Seattle area this summer?|
|Is the current rating for the Seattle review fair?|

如果您想要讓事件實體尋找所有事件資料，請標記第一個語句中的 `fair` 一字，但不要標記第二個語句中的該字。


## <a name="next-steps"></a>後續步驟
如需將 LUIS 應用程式定型以了解使用者語句的詳細資訊，請參閱[新增範例語句](luis-how-to-add-example-utterances.md)。

