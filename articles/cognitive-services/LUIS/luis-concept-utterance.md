---
title: 很好的例子話語 - LUIS
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221315"
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

這裡的核心術語"電腦"並不多樣化。 可使用桌上型電腦、膝上型電腦、工作站，甚至只稱為機器來替代。 LUIS 可以從上下文智慧地推斷同義字，但當您為訓練創建話語時，最好更改它們。

## <a name="example-utterances-in-each-intent"></a>每個意圖的範例語句

每個意圖都必須至少要有 15 個範例語句。 如果是沒有任何範例語句的意圖，則無法將 LUIS 定型。 如果您的意圖是包含一個或極少數示例陳述，LUIS 可能無法準確預測意圖。 

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>對每個製作反覆項目新增多個 15 個語句構成的群組

請不要在模型的每個反覆項目中新增大量語句。 新增 15 個語句。 再次[定型](luis-how-to-train.md)、[發佈](luis-how-to-publish-app.md)及[測試](luis-interactive-test.md)。  

LUIS 會利用由 LUIS 模型建立者精挑細選的語句來建置有效的模型。 新增太多語句只會導致產生混淆，並沒有用。

最好從少量語句開始，然後[檢閱端點語句](luis-how-to-review-endpoint-utterances.md)，以正確地預測意圖和擷取實體。

## <a name="utterance-normalization"></a>陳述正常化

共化正常化是在訓練和預測過程中忽略標點符號和變音符號的影響的過程。

## <a name="utterance-normalization-for-diacritics-and-punctuation"></a>變音符號和標點符號的可正常化

在創建或導入應用時，定義"提交"正常化，因為它是應用 JSON 檔中的設置。 預設情況下，話語正常化設置處於關閉狀態。 

變音符號是文本中的標記或符號，例如： 

```
İ ı Ş Ğ ş ğ ö ü
```

如果應用打開正常化，**則"測試"** 窗格、批次處理測試和終結點查詢中的分數將更改使用變音符號或標點符號的所有陳述。

在`settings`參數中打開變音符號或標點符號對 LUIS JSON 應用檔的話語正常化。

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"},
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

正常化**標點**符號意味著在模型接受培訓之前和在預測終結點查詢之前，標點符號將從陳述中刪除。 

正常化**變音符號**將字元替換為一般字元的話語中的變音符號。 例如：`Je parle français`變為`Je parle francais`。 

正常化並不意味著你不會在示例陳述或預測回應中看到標點符號和變音符號，而只是表示在訓練和預測過程中會忽略它們。


### <a name="punctuation-marks"></a>標點符號

標點符號在 LUIS 中是個別的語彙基元。 包含末尾的句點與不包含末尾句的表述是兩個不同的陳述，並且可能會獲得兩個不同的預測。 

如果標點符號未正常化，LUIS 不會忽略標點符號，預設情況下，因為某些用戶端應用程式可能會在這些標記上放置顯著性。 請確定您的範例語句應有使用標點符號和不使用標點符號兩種版本，讓這兩種樣式傳回相同的相對分數。 

請確定模型會在範例語句 (含標點符號和不含標點符號) 或在更容易使用特殊語法來忽略標點符號的[模式](luis-concept-patterns.md)中處理標點符號：`I am applying for the {Job} position[.]`

如果標點符號在用戶端應用程式中沒有特定的含義，請考慮通過正常化標點符號[來忽略標點](#utterance-normalization)符號。 

### <a name="ignoring-words-and-punctuation"></a>忽略單字和標點符號

如果要忽略模式中的特定單詞或標點符號，[請使用帶有平方](luis-concept-patterns.md#pattern-syntax)括弧_的忽略_語法的模式`[]`。 

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

