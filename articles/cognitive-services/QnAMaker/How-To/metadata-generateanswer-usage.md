---
title: 中繼資料與 GenerateAnswer API - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 可讓您將中繼資料（以索引鍵/值組的形式）新增至您的問題/答案配對。 您可以將結果篩選為使用者查詢，並儲存可在後續交談中使用的其他資訊。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 863143cb2ec1085bf03b070c225f2be5e8e4393d
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87126171"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>取得 GenerateAnswer API 和中繼資料的解答

若要取得使用者問題的預測答案，請使用 GenerateAnswer API。 當您發佈知識庫時，您可以在 [**發行**] 頁面上看到如何使用此 API 的相關資訊。 您也可以設定 API，根據元資料標記篩選答案，並使用測試查詢字串參數從端點測試知識庫。

QnA Maker 可讓您將中繼資料（以索引鍵和值組的形式）新增至您的問題和答案配對。 接著，您可以使用這項資訊來篩選使用者查詢的結果，以及儲存可在後續交談中使用的其他資訊。 如需詳細資訊，請參閱[知識庫](../Concepts/knowledge-base.md)。

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>使用 QnA 實體來儲存問題和答案

請務必瞭解 QnA Maker 如何儲存問題和解答資料。 下圖說明某個 QnA 實體：

![QnA 實體的圖例](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

每個 QnA 實體都有唯一且持續性的識別碼。 您可以使用此識別碼來對特定 QnA 實體進行更新。

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>使用 GenerateAnswer API 取得答案預測

您可以在 bot 或應用程式中使用[GENERATEANSWER API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) ，以使用者問題查詢您的知識庫，以取得最符合的問題和答案配對。

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>發行以取得 GenerateAnswer 端點

從[QnA Maker 入口網站](https://www.qnamaker.ai)或使用[API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)發佈知識庫之後，您可以取得 GenerateAnswer 端點的詳細資料。

若要取得端點詳細資料：
1. 登入 [https://www.qnamaker.ai](https://www.qnamaker.ai)。
1. 在 [**我的知識庫**] 中，選取您知識庫的 [查看程式**代碼**]。
    ![我知識庫的螢幕擷取畫面](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. 取得 GenerateAnswer 端點詳細資料。

    ![端點詳細資料的螢幕擷取畫面](../media/qnamaker-how-to-metadata-usage/view-code.png)

您也可以從知識庫的 [設定]**** 索引標籤取得端點詳細資料。

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer 要求設定

您可以透過 HTTP POST 要求來呼叫 GenerateAnswer。 如需示範如何呼叫 GenerateAnswer 的範例程式碼，請參閱[快速入門](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base)。

POST 要求會使用：

* 必要的[URI 參數](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* 必要的標頭屬性， `Authorization` 用於安全性
* 必要的[主體屬性](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto)。

GenerateAnswer URL 的格式如下：

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

請記得將的 HTTP 標頭屬性設定為，並將 `Authorization` 字串的值 `EndpointKey` 加上尾端空格，然後在 [**設定**] 頁面上找到端點金鑰。

範例 JSON 主體如下所示：

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

深入瞭解[rankerType](../concepts/best-practices.md#choosing-ranker-type)。

先前的 JSON 只要求30% 或高於閾值分數的答案。

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>GenerateAnswer 回應屬性

[回應](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query)是 JSON 物件，其中包含在交談中顯示答案和下一個回合時所需的所有資訊（如果有的話）。

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

Bot framework 可讓您使用[GETANSWER API](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync?view=botbuilder-dotnet-stable#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__)來存取 QnA Maker 的屬性：

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

先前的 JSON 只要求30% 或高於閾值分數的答案。

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>在 Node.js 中使用 bot 的 QnA Maker

Bot framework 可讓您使用[GETANSWER API](https://docs.microsoft.com/javascript/api/botbuilder-ai/qnamaker?view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-)來存取 QnA Maker 的屬性：

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

先前的 JSON 只要求30% 或高於閾值分數的答案。

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>使用中繼資料來篩選自訂元資料標記的答案

新增中繼資料可讓您依這些元資料標記篩選答案。 從 [**視圖選項**] 功能表新增 [中繼資料] 資料行。 藉由選取中繼資料 **+** 圖示來新增中繼資料組，以將中繼資料新增至您的知識庫。 此配對包含一個索引鍵和一個值。

![新增中繼資料的螢幕擷取畫面](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>使用中繼資料標記的 strictFilters 篩選結果

請考慮使用者問題「此飯店何時關閉？」，其中的意圖是針對餐廳「天堂」所隱含。

由於只有餐廳 "天堂" 才需要結果，因此您可以在 GenerateAnswer 呼叫中設定中繼資料「餐廳名稱」的篩選準則。 下列範例顯示這種情況：

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [ { "name": "restaurant", "value": "paradise"}]
}
```

### <a name="logical-and-by-default"></a>邏輯 AND （依預設）

若要在查詢中結合數個中繼資料篩選準則，請將其他中繼資料篩選準則新增至屬性的陣列 `strictFilters` 。 根據預設，這些值會以邏輯方式結合（和）。 邏輯組合要求所有篩選準則必須符合 QnA 組，才能在答案中傳回配對。

這相當於使用 `strictFiltersCompoundOperationType` 具有值的屬性 `AND` 。

### <a name="logical-or-using-strictfilterscompoundoperationtype-property"></a>Logical 或 using strictFiltersCompoundOperationType 屬性

結合數個中繼資料篩選器時，如果您只在意其中一個或一些篩選準則相符，請使用 `strictFiltersCompoundOperationType` 具有值的屬性 `OR` 。

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

在 QnA Maker 入口網站的中繼資料快速入門中深入瞭解中繼資料：
* [撰寫-將中繼資料新增至 QnA 配對](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [查詢預測-依中繼資料篩選答案](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>使用問題和答案結果來保持交談內容

GenerateAnswer 的回應包含相符的問題和答案配對的對應中繼資料資訊。 您可以在用戶端應用程式中使用這項資訊來儲存上一個交談的內容，以便在稍後的交談中使用。

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

## <a name="match-questions-only-by-text"></a>只比對問題（依文字）

根據預設，QnA Maker 會搜尋問題和答案。 如果您只想要搜尋問題，若要產生解答，請 `RankerType=QuestionOnly` 在 GenerateAnswer 要求的 POST 主體中使用。

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

[**發佈**] 頁面也會提供資訊，以使用 Postman 或捲曲[產生答案](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md)。

> [!div class="nextstepaction"]
> [取得知識庫的分析](../how-to/get-analytics-knowledge-base.md)
