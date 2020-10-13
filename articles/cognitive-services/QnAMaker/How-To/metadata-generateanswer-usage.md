---
title: 中繼資料與 GenerateAnswer API - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 可讓您將中繼資料（以索引鍵/值組的形式）新增至您的問題/答案配對。 您可以將結果篩選至使用者查詢，並儲存可在後續交談中使用的其他資訊。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/16/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 3a67f16b53c2754e2ac5ae1df467aac7726f358e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320994"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>使用 GenerateAnswer API 和中繼資料取得答案

若要取得使用者問題的預測答案，請使用 GenerateAnswer API。 當您發佈知識庫時，您可以在 [ **發行** ] 頁面上看到有關如何使用此 API 的資訊。 您也可以設定 API 來根據元資料標記篩選答案，並使用測試查詢字串參數從端點測試知識庫。

QnA Maker 可讓您將中繼資料（以索引鍵和值組的形式）新增至您的問題和答案配對。 然後，您可以使用這項資訊來篩選使用者查詢的結果，以及儲存可在後續交談中使用的其他資訊。 如需詳細資訊，請參閱[知識庫](../Concepts/knowledge-base.md)。

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>使用 QnA 實體來儲存問題和解答

請務必瞭解 QnA Maker 儲存問題和解答資料的方式。 下圖說明某個 QnA 實體：

![QnA 實體的圖例](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

每個 QnA 實體都有唯一且持續性的識別碼。 您可以使用此識別碼來對特定的 QnA 實體進行更新。

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>使用 GenerateAnswer API 取得答案預測

您可以在 bot 或應用程式中使用 [GENERATEANSWER API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) ，以使用者問題查詢您的知識庫，以充分符合問題和答案配對。

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>發佈以取得 GenerateAnswer 端點

從 [QnA Maker 入口網站](https://www.qnamaker.ai)或使用 [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)發佈知識庫之後，您可以取得 GenerateAnswer 端點的詳細資料。

若要取得端點詳細資料：
1. 登入 [https://www.qnamaker.ai](https://www.qnamaker.ai)。
1. 在 [ **我的知識庫**] 中，選取您知識庫的 [視圖程式 **代碼** ]。
    ![我的知識庫螢幕擷取畫面](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. 取得 GenerateAnswer 端點詳細資料。

    ![端點詳細資料的螢幕擷取畫面](../media/qnamaker-how-to-metadata-usage/view-code.png)

您也可以從知識庫的 [設定]**** 索引標籤取得端點詳細資料。

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer 要求設定

您可以透過 HTTP POST 要求來呼叫 GenerateAnswer。 如需示範如何呼叫 GenerateAnswer 的範例程式碼，請參閱[快速入門](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base)。

POST 要求會使用：

* 必要的 [URI 參數](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* 必要的標頭屬性， `Authorization` 用於安全性
* 必要的 [主體屬性](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto)。

GenerateAnswer URL 的格式如下：

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

請記得使用具有尾端空格的字串值設定 HTTP 標頭屬性， `Authorization` `EndpointKey` 然後在 [ **設定** ] 頁面上找到端點金鑰。

範例 JSON 主體看起來像這樣：

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 30,
    "rankerType": "" // values: QuestionOnly
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

深入瞭解 [rankerType](../concepts/best-practices.md#choosing-ranker-type)。

先前的 JSON 要求的答案僅為30% 或高於臨界值分數。

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>GenerateAnswer 回應屬性

[回應](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query)是一個 JSON 物件，其中包括顯示答案所需的所有資訊，以及對話中的下一個回合（如果有的話）。

```json
{
    "answers": [
        {
            "score": 38.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

先前的 JSON 回應分數為38.5% 的答案。

## <a name="use-qna-maker-with-a-bot-in-c"></a>搭配使用 QnA Maker 與 C 中的 bot#

Bot framework 可讓您使用 [GETANSWER API](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync?view=botbuilder-dotnet-stable#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__)存取 QnA Maker 的屬性：

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

metadata.Name = Constants.MetadataName.Intent;
metadata.Value = topIntent;
qnaOptions.StrictFilters = new Microsoft.Bot.Builder.AI.QnA.Metadata[] { metadata };
qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

先前的 JSON 要求的答案僅為30% 或高於臨界值分數。

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>使用 QnA Maker 與 Node.js 中的 bot

Bot framework 可讓您使用 [GETANSWER API](https://docs.microsoft.com/javascript/api/botbuilder-ai/qnamaker?view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-)存取 QnA Maker 的屬性：

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.30,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

先前的 JSON 要求的答案僅為30% 或高於臨界值分數。

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>使用中繼資料依自訂元資料標記篩選答案

加入中繼資料可讓您篩選這些元資料標記的答案。 從 [ **視圖選項** ] 功能表新增 [中繼資料] 資料行。 選取中繼資料 **+** 圖示來新增中繼資料組，以將中繼資料加入至知識庫。 這組組合是由一個索引鍵和一個值所組成。

![新增中繼資料的螢幕擷取畫面](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>使用中繼資料標記的 strictFilters 篩選結果

請考慮使用者的問題：「這個旅館何時關閉？」，這是針對餐廳「天堂」暗示的意圖。

由於只有餐廳 "天堂" 才需要結果，因此您可以在 GenerateAnswer 呼叫的中繼資料「餐廳名稱」上設定篩選。 下列範例顯示：

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [ { "name": "restaurant", "value": "paradise"}]
}
```

### <a name="logical-and-by-default"></a>邏輯 AND 預設

若要在查詢中結合數個中繼資料篩選準則，請將其他中繼資料篩選加入至屬性的陣列 `strictFilters` 。 依預設，這些值會以邏輯方式結合 (和) 。 邏輯組合需要所有篩選準則符合 QnA 組，才能在答案中傳回配對。

這相當於使用 `strictFiltersCompoundOperationType` 具有值的屬性 `AND` 。

### <a name="logical-or-using-strictfilterscompoundoperationtype-property"></a>邏輯或使用 strictFiltersCompoundOperationType 屬性

合併數個中繼資料篩選準則時，如果您只在意其中一個或一些篩選準則，請使用 `strictFiltersCompoundOperationType` 具有值的屬性 `OR` 。

這可讓您的知識庫在任何篩選準則相符時傳回答案，但不會傳回沒有中繼資料的答案。

```json
{
    "question": "When do facilities in this hotel close?",
    "top": 1,
    "strictFilters": [
      { "name": "type","value": "restaurant"},
      { "name": "type", "value": "bar"},
      { "name": "type", "value": "poolbar"}
    ],
    "strictFiltersCompoundOperationType": "OR"
}
```

### <a name="metadata-examples-in-quickstarts"></a>快速入門中的中繼資料範例

在 QnA Maker 入口網站快速入門中，深入瞭解中繼資料的中繼資料：
* [製作 - 將中繼資料新增至 QnA 配對](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [查詢預測 - 依中繼資料篩選答案](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>使用問與答結果來保存交談內容

對 GenerateAnswer 的回應包含相符問題和答案配對的對應中繼資料資訊。 您可以在用戶端應用程式中使用這項資訊來儲存先前交談的內容，以便在稍後的對話中使用。

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="match-questions-only-by-text"></a>依文字比對問題

依預設，QnA Maker 會搜尋問題和答案。 如果您只想要搜尋問題，若要產生解答，請 `RankerType=QuestionOnly` 在 GenerateAnswer 要求的 POST 主體中使用。

您可以使用在測試 kb 中搜尋已發佈的 kb、使用 `isTest=false` 或 `isTest=true` 。

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="common-http-errors"></a>常見的 HTTP 錯誤

|程式碼|說明|
|:--|--|
|2xx|成功|
|400|要求的參數不正確，表示必要參數遺失、格式不正確或太大|
|400|要求的本文不正確，表示 JSON 遺失、格式不正確或太大|
|401|金鑰無效|
|403|禁止 - 您沒有正確的權限|
|404|KB 不存在|
|410|此 API 已被淘汰且不再提供|

## <a name="next-steps"></a>後續步驟

[ **發佈** ] 頁面也會提供資訊，以使用 Postman 或捲曲 [產生答案](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) 。

> [!div class="nextstepaction"]
> [取得知識庫的分析](../how-to/get-analytics-knowledge-base.md)
