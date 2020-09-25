---
title: 資料解壓縮-LUIS
description: 使用意圖和實體從語句文字中提取資料。 瞭解可從 Language Understanding (LUIS) 解壓縮的資料類型。
ms.service: cognitive-services
ms.subservice: language-understanding
author: diberry
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: c69971b36bf945086d1a8f398043444071acea5c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91324819"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>使用意圖與實體從表達文字擷取資料
LUIS 可讓您從使用者的自然語言語句取得資訊。 此資訊的擷取方式使得它可供程式、應用程式或 Chatbot 用來執行動作。 在下列各節中，您將透過 JSON 範例，了解從意圖和實體會傳回哪些資料。

最難解壓縮的資料是機器學習資料，因為它不是完全相符的文字。 機器學習 [實體](luis-concept-entity-types.md) 的資料解壓縮必須是 [撰寫週期](luis-concept-app-iteration.md) 的一部分，直到您確信收到預期的資料為止。

## <a name="data-location-and-key-usage"></a>資料位置和金鑰使用方式
LUIS 會從已發佈 [端點](luis-glossary.md#endpoint)的使用者語句中解壓縮資料。 **HTTPS 要求** (POST 或 GET) 除了包含一些額外的設定 (例如預備或生產環境) 之外，也包含語句。

**V2 預測端點要求**

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

**V3 預測端點要求**

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

當您編輯 LUIS 應用程式時，從該 LUIS 應用程式的 [設定]**** 頁面，以及從您 URL 的一部分 (在 `/apps/` 之後)，都可以取得 `appID`。 `subscription-key` 是用來查詢您應用程式的端點金鑰。 在學習 LUIS 期間，雖然您可以使用免費的撰寫/入門金鑰，但請務必將端點金鑰變更為支援[預期的 LUIS 使用方式](luis-limits.md#key-limits)的金鑰。 `timezoneOffset` 單位為分鐘。

**HTTPS 回應**包含 LUIS 可以根據目前已發佈之預備或生產環境端點模型來判斷的所有意圖和實體資訊。 端點 URL 是在 [LUIS](luis-reference-regions.md) 網站、[管理]**** 區段、[金鑰和端點]**** 頁面上找到的。

## <a name="data-from-intents"></a>來自意圖的資料
主要資料是最高分的**意圖名稱**。 端點回應為：

#### <a name="v2-prediction-endpoint-response"></a>[V2 預測端點回應](#tab/V2)

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 預測端點回應](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

* * *

|資料物件|資料類型|資料位置|值|
|--|--|--|--|
|Intent|String|topScoringIntent.intent|"GetStoreInfo"|

如果您的聊天機器人或 LUIS 呼叫應用程式根據一個以上的意圖分數進行決策，則會傳回所有意圖的分數。


#### <a name="v2-prediction-endpoint-response"></a>[V2 預測端點回應](#tab/V2)

設定 querystring 參數 `verbose=true` 。 端點回應為：

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 預測端點回應](#tab/V3)

設定 querystring 參數 `show-all-intents=true` 。 端點回應為：

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

* * *

意圖會依最高分到最低分排序。

|資料物件|資料類型|資料位置|值|分數|
|--|--|--|--|:--|
|Intent|String|intents[0].intent|"GetStoreInfo"|0.984749258|
|Intent|String|intents[1].intent|"None"|0.0168218873|

如果您新增預先建置的定義域，則意圖名稱除了會指出意圖之外，也會指出該定義域，例如 `Utilties` 或 `Communication`：

#### <a name="v2-prediction-endpoint-response"></a>[V2 預測端點回應](#tab/V2)

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 預測端點回應](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

* * *

|網域|資料物件|資料類型|資料位置|值|
|--|--|--|--|--|
|公用程式|Intent|String|intents[0].intent|"<b>Utilities</b>.ShowNext"|
|通訊|Intent|String|intents[1].intent|<b>Communication</b>.StartOver"|
||Intent|String|intents[2].intent|"None"|


## <a name="data-from-entities"></a>來自實體的資料
大部分聊天機器人和應用程式都需要的意圖名稱不超過意圖名稱。 這個額外的選擇性資料來自在語句中探索到的實體。 每個類型的實體會傳回與比對相關的不同資訊。

語句中的單一單字或片語可能會與多個實體相符。 在該情況下，系統會傳回每個相符的實體及其分數。

從端點之回應的 **實體** 陣列中傳回所有實體

## <a name="tokenized-entity-returned"></a>傳回的 Token 化實體

請參閱 LUIS 中的 [權杖支援](luis-language-support.md#tokenization) 。


## <a name="prebuilt-entity-data"></a>預先建置的實體資料
探索[預先建置](luis-concept-entity-types.md)實體時，會使用開放原始碼 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 專案，根據規則運算式比對進行探索。 預先建置的實體會在實體陣列中傳回，並使用前面加上 `builtin::` 的類別名稱。

## <a name="list-entity-data"></a>清單實體資料

[清單實體](reference-entity-list.md) 代表一組固定且封閉的相關單字及其同義字。 LUIS 並不會探索清單實體的額外值。 使用**建議**功能，以根據目前的清單查看適用於新字組的建議。 如果有多個清單實體具有相同的值，則在端點查詢中會傳回每個實體。

## <a name="regular-expression-entity-data"></a>規則運算式實體資料

[正則運算式實體](reference-entity-regular-expression.md)會根據您提供的正則運算式來解壓縮實體。

## <a name="extracting-names"></a>擷取名稱
從語句中取得名稱相當困難，因為名稱幾乎可以是字母與單字的任何組合。 視所要擷取的名稱類型而定，您會有數個選項。 下列建議不是規定，而是指導方針。

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>新增預建的 PersonName 和 GeographyV2 實體

[PersonName](luis-reference-prebuilt-person.md) 和 [GeographyV2](luis-reference-prebuilt-geographyV2.md) 實體可在某些[語言文化特性](luis-reference-prebuilt-entities.md)中使用。

### <a name="names-of-people"></a>人名

人名可依據語言和文化特性而有些微的格式。 使用預先建立的**[personName](luis-reference-prebuilt-person.md)** 實體或具有 first 和 last 名稱[角色](luis-concept-roles.md)的**[簡單實體](luis-concept-entity-types.md#simple-entity)**。

如果您使用簡單的實體，請務必提供在語句的不同部分中使用名字和姓氏的範例（以不同長度的語句為限），並在所有意圖（包括 None 意圖）之間語句。 請定期[檢閱](luis-how-to-review-endoint-utt.md)端點語句，以標記任何未正確預測的名稱。

### <a name="names-of-places"></a>地名

位置名稱是已設定且已知的，例如城市、縣市、州、省和國家/地區。 使用預先建立的實體 **[geographyV2](luis-reference-prebuilt-geographyv2.md)** 來解壓縮位置資訊。

### <a name="new-and-emerging-names"></a>全新和新興的名稱

有些應用程式需要能夠尋找全新和新興的名稱，例如產品或公司。 這些類型的名稱是最困難的資料解壓縮類型。 從簡單的 **[實體](luis-concept-entity-types.md#simple-entity)** 開始，然後新增 [片語清單](luis-concept-feature.md)。 請定期[檢閱](luis-how-to-review-endoint-utt.md)端點語句，以標記任何未正確預測的名稱。

## <a name="patternany-entity-data"></a>Pattern.any 實體資料

[模式。任何](reference-entity-pattern-any.md) 是僅用於模式範本語句的可變長度預留位置，用來標記實體開始及結束的位置。 您必須在模式中找到使用的實體，才能套用模式。

## <a name="sentiment-analysis"></a>情感分析
如果在 [發行](luis-how-to-publish-app.md#sentiment-analysis)時設定情感分析，LUIS json 回應會包含情感分析。 若要深入了解情感分析，請參閱[文字分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)文件。

## <a name="key-phrase-extraction-entity-data"></a>關鍵片語擷取實體資料
[關鍵字組解壓縮實體](luis-reference-prebuilt-keyphrase.md)會傳回語句中的關鍵字組（由[文字分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)提供）。

## <a name="data-matching-multiple-entities"></a>與多個實體相符的資料

LUIS 會傳回在語句中探索到的所有實體。 因此，您的聊天機器人可能需要根據結果進行決策。

## <a name="data-matching-multiple-list-entities"></a>與多個清單實體相符的資料

如果單字或片語與多個清單實體相符，端點查詢會傳回每個清單實體。

針對查詢 `when is the best time to go to red rock?` ，而且應用程式 `red` 在一個以上的清單中有該單字，LUIS 會辨識所有實體，並傳回實體陣列作為 JSON 端點回應的一部分。

## <a name="next-steps"></a>後續步驟

請參閱[新增實體](luis-how-to-add-entities.md)，以深入了解如何將實體新增至 LUIS 應用程式。
