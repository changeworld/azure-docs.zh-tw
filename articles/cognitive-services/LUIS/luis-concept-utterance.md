---
title: 良好的範例語句-LUIS
description: 表達是應用程式需要解譯的使用者輸入。 收集您認為使用者會輸入的片語。 納入意義相同但以不同單字長度和單字位置建構的語句。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 96b9754908f437ccf81e002e9e9dd17af0bab4e3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95019070"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>了解適合您 LUIS 應用程式的語句

**語句** 是應用程式需要解譯的使用者輸入。 若要將 LUIS 定型以從中擷取意圖和實體，務必要針對每個意圖擷取各種不同的範例語句。 主動學習或繼續訓練新語句的程式，對於 LUIS 所提供的機器學習智慧而言是不可或缺的。

收集您認為使用者會輸入的語句。 包括意義相同但結構不同的語句：

* 語句長度 - 適用於您用戶端應用程式的短、中和長句子
* 字組與片語長度
* 字組位置 - 位於語句開頭、中間與結尾的實體
* 文法
* 複數表示
* 詞幹分析
* 名詞和動詞的選擇
* [標點符號](luis-reference-application-settings.md#punctuation-normalization) -使用正確、不正確和無文法的絕佳種類

## <a name="how-to-choose-varied-utterances"></a>如何選擇各種語句

當您剛開始為 LUIS 模型[新增範例語句](./luis-how-to-add-entities.md)時，請將以下幾個原則謹記在心。

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

這裡的核心詞彙「電腦」並不不同。 可使用桌上型電腦、膝上型電腦、工作站，甚至只稱為機器來替代。 LUIS 可讓您以智慧的方式從內容推斷同義字，但當您建立定型的語句時，最好隨時變更它們。

## <a name="example-utterances-in-each-intent"></a>每個意圖的範例語句

每個意圖都必須至少要有 15 個範例語句。 如果是沒有任何範例語句的意圖，則無法將 LUIS 定型。 如果您有一個或少數範例語句的意圖，LUIS 可能無法精確預測意圖。

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>對每個製作反覆項目新增多個 15 個語句構成的群組

請不要在模型的每個反覆項目中新增大量語句。 新增 15 個語句。 再次[定型](luis-how-to-train.md)、[發佈](luis-how-to-publish-app.md)及[測試](luis-interactive-test.md)。

LUIS 會利用由 LUIS 模型建立者精挑細選的語句來建置有效的模型。 新增太多語句只會導致產生混淆，並沒有用。

最好從少量語句開始，然後[檢閱端點語句](luis-how-to-review-endpoint-utterances.md)，以正確地預測意圖和擷取實體。

## <a name="utterance-normalization"></a>語句正規化

語句正規化是在定型和預測期間忽略文字類型（例如標點符號和變音符號）效果的程式。

語句正規化設定預設為關閉。 這些設定包括：

* Word 表單
* 調
* 標點符號

如果您開啟正規化設定，則會針對該正規化設定的所有語句變更 [ **測試** ] 窗格中的分數、批次測試和端點查詢。

當您在 LUIS 入口網站中複製版本時，版本設定會繼續使用新複製的版本。

在 [ **管理** ] 區段的 [ **應用程式設定** ] 頁面或 [更新版本設定 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings)上，透過 LUIS 入口網站設定版本設定。 在 [參考](luis-reference-application-settings.md)中深入瞭解這些正規化變更。

### <a name="word-forms"></a>Word 表單

將 **word forms** 正規化會忽略在根以外擴充的單字差異。

<a name="utterance-normalization-for-diacritics-and-punctuation"></a>

### <a name="diacritics"></a>調

文字內的符號標記或正負號，例如：

```
İ ı Ş Ğ ş ğ ö ü
```

### <a name="punctuation-marks"></a>標點符號
正規化 **標點符號** 表示在您的模型定型之前，以及在您的端點查詢獲得預測之前，將會從語句中移除標點符號。

標點符號在 LUIS 中是個別的語彙基元。 在結尾包含句號的語句，與結尾不包含句點的語句是兩個不同的語句，而且可能會得到兩個不同的預測。

如果標點符號未正規化，LUIS 預設不會忽略標點符號，因為某些用戶端應用程式可能會對這些標記進行重要性。 請確定您的範例語句應有使用標點符號和不使用標點符號兩種版本，讓這兩種樣式傳回相同的相對分數。

請確定模型會在範例語句 (含標點符號和不含標點符號) 或在更容易使用特殊語法來忽略標點符號的[模式](luis-concept-patterns.md)中處理標點符號：`I am applying for the {Job} position[.]`

如果標點符號在您的用戶端應用程式中沒有特定意義，請考慮將標點符號正規化以 [忽略標點符號](#utterance-normalization) 。

### <a name="ignoring-words-and-punctuation"></a>忽略單字和標點符號

如果您想要忽略模式中的特定單字或標點符號，請使用具有方括弧 _忽略_ 語法的 [模式](luis-concept-patterns.md#pattern-syntax) `[]` 。

<a name="training-utterances"></a>

## <a name="training-with-all-utterances"></a>使用所有語句進行訓練

定型通常不具確定性：版本或應用程式間的語句預測會稍微不同。
您可以更新[版本設定](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API，以 `UseAllTrainingData`名稱/值配對來使用所有定型資料，藉此移除非確定性的定型。

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


## <a name="next-steps"></a>下一步
如需將 LUIS 應用程式定型以了解使用者語句的詳細資訊，請參閱[新增範例語句](./luis-how-to-add-entities.md)。