---
title: 構建 LUIS 應用的最佳做法
titleSuffix: Azure Cognitive Services
description: 瞭解最佳實踐，以便從 LUIS 應用的模型中獲得最佳結果。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: b4be79338db71ad83204fae971da0b77885a8070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74280917"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>構建語言理解 （LUIS） 應用的最佳做法
使用應用創作過程生成 LUIS 應用： 

* 構建語言模型（意圖和實體）
* 添加一些培訓示例陳述（每個意圖 15-30 個）
* 發佈到終結點
* 從端點測試 

[發佈](luis-how-to-publish-app.md)應用後，請使用開發生命週期從終結點添加功能、發佈和測試。 不要通過添加更多示例陳述來開始下一個創作週期，因為這不會讓 LUIS 使用真實使用者陳述瞭解您的模型。 

在當前示例和終結點陳述集返回自信、高預測分數之前，不要展開陳述。 使用[主動學習](luis-concept-review-endpoint-utterances.md)提高分數。 




## <a name="do-and-dont"></a>建議事項和避免事項
以下清單包含 LUIS 應用程式的最佳做法：

|要做的事|不要做的事|
|--|--|
|[定義不同的意圖](#do-define-distinct-intents)<br>[向意圖添加描述項](#do-add-descriptors-to-intents) |[將許多範例語句新增至意圖](#dont-add-many-example-utterances-to-intents)<br>[使用很少或簡單的實體](#dont-use-few-or-simple-entities) |
|[找出每個意圖的太攏統與太特定之間的最佳點](#do-find-sweet-spot-for-intents)|[使用 LUIS 作為定型平台](#dont-use-luis-as-a-training-platform)|
|[使用版本反覆運算構建應用](#do-build-your-app-iteratively-with-versions)<br>[生成實體進行模型分解](#do-build-for-model-decomposition)|[新增許多相同格式的範例語句而忽略其他格式](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[在以後的反覆運算中添加模式](#do-add-patterns-in-later-iterations)|[混合意圖和實體的定義](#dont-mix-the-definition-of-intents-and-entities)|
|[讓您的語句在所有意圖之間達到平衡](#balance-your-utterances-across-all-intents)，除了 None 意圖之外。<br>[將範例語句新增至 None 意圖](#do-add-example-utterances-to-none-intent)|[創建具有所有可能值的描述項](#dont-create-descriptors-with-all-the-possible-values)|
|[利用主動式學習的建議功能](#do-leverage-the-suggest-feature-for-active-learning)|[新增太多模式](#dont-add-many-patterns)|
|[通過批次處理測試監控應用的性能](#do-monitor-the-performance-of-your-app)|[新增每一個範例語句都進行定型和發佈](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>請務必定義不同的意圖
請確定每個意圖的詞彙都僅適用於該意圖，而未與不同的意圖重疊。 例如，如果您想要有一個處理旅遊安排 (例如航班和飯店) 的應用程式，則可以選擇讓這些主體區域成為語句內具有特定資料實體的個別意圖或相同意圖。

如果兩個意圖之間的詞彙相同，請將意圖結合，然後使用實體。 

請思考一下以下的範例語句：

|範例語句|
|--|
|預訂班機|
|預訂飯店|

`Book a flight`並使用`Book a hotel`相同的詞彙`book a `。 此格式相同，因此與`flight`和`hotel`提取的實體的不同單詞應具有相同的意圖。 

## <a name="do-add-descriptors-to-intents"></a>確實向意圖添加描述項

描述項有助於描述意圖的功能。 描述項可以是對該意圖很重要的單詞的片語清單，也可以是該意圖重要的實體。 

## <a name="do-find-sweet-spot-for-intents"></a>請務必找出意圖的最佳點
請使用來自 LUIS 的預測資料來判斷您的意圖是否重疊。 重疊的意圖會混淆 LUIS。 結果會造成最高分的意圖太接近另一個意圖。 由於 LUIS 不會每次都使用完全相同的資料路徑來進行定型，因此重疊的意圖有可能在定型中成為第一或第二。 您會希望每個意圖的語句分數都儘量拉開，以便避免發生這類不確定情況。 良好的意圖區別應該每次都產生預期的最高分意圖。 
 
<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>使用版本反覆運算構建應用

每個撰寫循環都應該在新[版本](luis-concept-version.md) (從現有版本複製) 之內。 

## <a name="do-build-for-model-decomposition"></a>為模型分解進行生成

模型分解的典型過程是：

* 基於用戶端應用的使用者意圖創建**意圖**
* 根據實際使用者輸入添加 15-30 個示例陳述
* 在示例陳述中標注頂級資料概念
* 將資料概念分解為子元件
* 向子元件添加描述項（功能）
* 向意圖添加描述項（功能） 

創建意圖並添加示例陳述後，下面的示例將描述實體分解。 

首先確定要在話語中提取的完整資料概念。 這是您的機器學習實體。 然後將短語分解為其部分。 這包括標識子元件（作為實體），以及描述項和約束。 

例如，如果要提取位址，可以調用`Address`頂部機器學習實體。 創建位址時，請標識其某些子元件，如街道位址、城市、州和郵遞區號。 

通過將郵遞區號**約束**為正則運算式，繼續分解這些元素。 將街道位址分解為街道編號（使用預建編號）、街道名稱和街道類型的部分。 街道類型可以使用**描述項**清單（如大道、圓形、道路和車道）來描述。

V3 創作 API 允許模型分解。 

## <a name="do-add-patterns-in-later-iterations"></a>在以後的反覆運算中添加模式

在添加模式之前，您應該瞭解應用的表[率](luis-concept-patterns.md)，因為模式的權重比示例陳述更重，並且會扭曲置信度。 

瞭解應用的去向後，在應用應用於應用時添加模式。 您無需在每個[反覆運算](luis-concept-app-iteration.md)中添加它們。 

在模型設計開始時添加它們沒有任何壞處，但在使用話語測試模型後，更容易看到每個模式如何更改模型。 
 
<!--

### Phrase lists

[Phrase lists](luis-concept-feature.md) allow you to define dictionaries of words related to your app domain. Seed your phrase list with a few words then use the suggest feature so LUIS knows about more words in the vocabulary specific to your app. A Phrase List improves intent detection and entity classification by boosting the signal associated with words or phrases that are significant to your app. 

Don't add every word to the vocabulary since the phrase list isn't an exact match. 

For more information:
* Concept: [Phrase list features in your LUIS app](luis-concept-feature.md)
* How-to: [Use phrase lists to boost signal of word list](luis-how-to-add-features.md)



### Patterns

Real user utterances from the endpoint, very similar to each other, may reveal patterns of word choice and placement. The [pattern](luis-concept-patterns.md) feature takes this word choice and placement along with regular expressions to improve your prediction accuracy. A regular expression in the pattern allows for words and punctuation you intend to ignore while still matching the pattern. 

Use pattern's [optional syntax](luis-concept-patterns.md) for punctuation so punctuation can be ignored. Use the [explicit list](luis-concept-patterns.md#explicit-lists) to compensate for pattern.any syntax issues. 

For more information:
* Concept: [Patterns improve prediction accuracy](luis-concept-patterns.md)
* How-to: [How to add Patterns to improve prediction accuracy](luis-how-to-model-intent-pattern.md)
-->

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>在所有意圖中平衡你的話語

為了讓 LUIS 預測能正確，每個意圖的範例語句數量必須相對相等 (None 意圖除外)。 

如果您有一個包含 100 個範例語句的意圖和一個包含 20 個範例語句的意圖，100 個語句的意圖會有較高的預測評等。  

## <a name="do-add-example-utterances-to-none-intent"></a>請務必將範例語句新增至 None 意圖

此意圖是回退意圖，指示應用程式外部的所有內容。 請在您 LUIS 應用程式的其餘部分，每 10 個範例語句中，便將一個範例語句新增至 None 意圖。

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>請務必利用主動式學習的建議功能

請定期使用[主動式學習](luis-how-to-review-endpoint-utterances.md)的**檢閱端點語句**，而不要將更多範例語句新增至意圖。 由於應用程式會不斷接收端點語句，因此這份清單會不斷成長並變更。

## <a name="do-monitor-the-performance-of-your-app"></a>請務必監視您應用程式的效能

請使用[批次測試](luis-concept-batch-test.md)集合來監視預測準確性。 

保留一組不用作[示例陳述](luis-concept-utterance.md)或終結點陳述的單獨陳述。 持續針對測試集改善應用程式。 調適測試集以反映真實的使用者語句。 使用此測試集來評估應用程式的每個反覆項目或版本。 

## <a name="dont-add-many-example-utterances-to-intents"></a>請勿將許多範例語句新增至意圖

發佈應用後，僅在開發生命週期過程中添加來自活動學習的話語。 如果語句太類似，則請新增模式。 

## <a name="dont-use-few-or-simple-entities"></a>不要使用很少或簡單的實體

實體是為數據提取和預測而構建的。 每個意圖都有機器學習的實體來描述意圖中的資料，這一點很重要。 這有助於 LUIS 預測意圖，即使您的用戶端應用程式不需要使用提取的實體。 

## <a name="dont-use-luis-as-a-training-platform"></a>請勿使用 LUIS 作為定型平台

LUIS 是語言模型定義域特定的。 不是用來作為一般的自然語言訓練平台使用。 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>請勿新增許多相同格式的範例語句而忽略其他格式

LUIS 會預期意圖的語句中有所變化。 語句可以在改變的同時仍保有相同的整體意義。 變化可以包括語句長度、單字選擇，以及單字位置。 

|請勿使用相同的格式|請務必使用變化格式|
|--|--|
|購買一張到西雅圖的機票<br>購買一張到巴黎的機票<br>購買一張到奧蘭多的機票|購買 1 張到西雅圖的機票<br>預約兩個下週一到巴黎的紅眼航班機位<br>我想要預訂 3 張到奧蘭多的春假機票|

第二欄使用不同的動詞 (購買、預約、預訂)、不同的量詞 (1、兩、3) 及不同的單字排列，但全都具有購買旅遊機票的相同意圖。 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>請勿混合意圖和實體的定義

請針對您 Bot 將執行的任何動作建立意圖。 使用實體作為促成該動作的參數。 

對於將預訂航空公司航班的機器人，創建**BookFlight**意向。 請勿為每個航空公司或每個目的地都建立意圖。 請使用這些資料片段作為[實體](luis-concept-entity-types.md)，然後在範例語句中標示它們。 

## <a name="dont-create-descriptors-with-all-the-possible-values"></a>不要創建具有所有可能值的描述項

在描述項[片語清單中](luis-concept-feature.md)提供幾個示例，但並不是每個單詞。 LUIS 會將內容一般化並納入考量。 

## <a name="dont-add-many-patterns"></a>請勿新增許多模式

請勿新增太多[模式](luis-concept-patterns.md)。 LUIS 旨在透過更少的範例快速學習。 請勿不必要地讓系統多載。

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>請勿對每一個範例語句都進行定型和發佈

請新增 10 或 15 個語句之後，再進行定型和發佈。 這將可讓您了解對預測準確性的影響。 新增單一語句對分數可能不會有明顯的影響。 

## <a name="next-steps"></a>後續步驟

* 了解如何在 LUIS 應用程式中[規劃您的應用程式](luis-how-plan-your-app.md)。
