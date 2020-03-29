---
title: 中繼資料與 GenerateAnswer API - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 可讓您將索引鍵/值組形式的中繼資料新增至問答集。 您可以將結果篩選為使用者查詢，並存儲可用於後續對話的其他資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 6a8cbabfd4e47c50d2c2e6f4a23c50a931e645a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221447"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>使用生成應答 API 和中繼資料獲取答案

要獲取使用者問題的預測答案，請使用生成應答 API。 發佈知識庫時，您可以在 **"發佈"** 頁上看到有關如何使用此 API 的資訊。 還可以配置 API 以根據元資料標記篩選答案，以及使用測試查詢字串參數從終結點測試知識庫。

QnA Maker 允許您以鍵和值對的形式將中繼資料添加到一組問題和答案中。 然後，可以使用此資訊將結果篩選到使用者查詢，並存儲可用於後續對話的其他資訊。 如需詳細資訊，請參閱[知識庫](../Concepts/knowledge-base.md)。

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>使用 QnA 實體存儲問題和答案

瞭解 QnA Maker 如何存儲問題和應答資料非常重要。 下圖說明某個 QnA 實體：

![QnA 實體的插圖](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

每個 QnA 實體都有唯一且持續性的識別碼。 您可以使用 ID 對特定的 QnA 實體進行更新。

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>使用生成應答 API 獲取答案預測

在機器人或應用程式中使用[GenerateAnswer API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)使用使用者問題查詢知識庫，以便從問答集中獲得最佳匹配。

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>發佈以獲取生成應答終結點

發佈知識庫後，無論是從[QnA Maker 門戶](https://www.qnamaker.ai)，還是通過使用[API，](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)都可以獲取生成應答終結點的詳細資訊。

若要取得端點詳細資料：
1. 登錄到[https://www.qnamaker.ai](https://www.qnamaker.ai)。
1. **在"我的知識庫"中**，選擇 **"查看知識庫的代碼**"。
    ![我的知識庫截圖](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. 取得 GenerateAnswer 端點詳細資料。

    ![終結點詳細資訊的螢幕截圖](../media/qnamaker-how-to-metadata-usage/view-code.png)

您也可以從知識庫的 [設定]**** 索引標籤取得端點詳細資料。

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>生成應答請求配置

您可以透過 HTTP POST 要求來呼叫 GenerateAnswer。 如需示範如何呼叫 GenerateAnswer 的範例程式碼，請參閱[快速入門](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base)。

POST 請求使用：

* 所需的[URI 參數](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* 安全所需的標頭屬性`Authorization`，
* 必需[的正文屬性](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto)。

生成應答 URL 具有以下格式：

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

請記住，使用具有尾隨空格的字串`Authorization``EndpointKey`的值設置 的 HTTP 標頭屬性，然後在 **"設置"** 頁上找到的終結點鍵。

JSON 正文的示例如下所示：

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

瞭解有關[rank 類型](../concepts/best-practices.md#choosing-ranker-type)的更多。

以前的 JSON 僅請求的答案為 30% 或高於閾值分數。

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>生成應答回應屬性

[回應](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query)是 JSON 物件，包括顯示答案所需的所有資訊，以及對話的下一個回合（如果可用）。

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

上一份JSON的回答是38.5%。

## <a name="use-qna-maker-with-a-bot-in-c"></a>將 QnA 製造商與 C 中的自動程式一起使用#

機器人框架提供訪問QnA製造商的屬性與[getAnswer API](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync?view=botbuilder-dotnet-stable#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__)：

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

以前的 JSON 僅請求的答案為 30% 或高於閾值分數。

支援機器人有[一個](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418)帶有此代碼的示例。

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>在 Node.js 中使用帶有自動程式的 QnA 製造商

機器人框架提供訪問QnA製造商的屬性與[getAnswer API](https://docs.microsoft.com/javascript/api/botbuilder-ai/qnamaker?view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-)：

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

以前的 JSON 僅請求的答案為 30% 或高於閾值分數。

支援機器人有[一個](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36)帶有此代碼的示例。

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>使用中繼資料按自訂元資料標記篩選答案

通過添加中繼資料，您可以通過這些元資料標記篩選答案。 從 **"視圖選項"** 功能表添加中繼資料列。 通過選擇中繼資料**+** 圖示來添加中繼資料對，將中繼資料添加到知識庫中。 此對由一個鍵和一個值組成。

![添加中繼資料的螢幕截圖](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>使用中繼資料標記的 strictFilters 篩選結果

考慮使用者的問題"這家酒店何時關閉？"

由於結果僅對餐廳"天堂"是必需的，因此您可以在中繼資料"餐廳名稱"的"生成應答"調用中設置篩選器。 以下示例顯示了這一點：

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>使用問答結果保持對話上下文

對"生成答案"的回應包含匹配的問題和答案集的相應中繼資料資訊。 您可以在用戶端應用程式中使用此資訊來存儲上一個對話的上下文，以便以後的對話中使用。

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

## <a name="match-questions-only-by-text"></a>僅按文本匹配問題

預設情況下，QnA Maker 會搜索問題和答案。 如果只想搜索問題，要生成答案，請使用`RankerType=QuestionOnly`"生成應答"請求的 POST 正文。

您可以使用 在 已發佈的 kb`isTest=false`中搜索 、使用 或`isTest=true`使用 在 測試 kb 中搜索 。

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="common-http-errors"></a>常見 HTTP 錯誤

|程式碼|說明|
|:--|--|
|2xx|Success|
|400|要求的參數不正確，表示必要參數遺失、格式不正確或太大|
|400|要求的本文不正確，表示 JSON 遺失、格式不正確或太大|
|401|金鑰無效|
|403|禁止 - 您沒有正確的權限|
|404|KB 不存在|
|410|此 API 已被淘汰且不再提供|

## <a name="next-steps"></a>後續步驟

**"發佈"** 頁還提供資訊，以便使用 Postman 或 cURL[生成答案](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md)。

> [!div class="nextstepaction"]
> [建立知識庫 Bot](../tutorials/integrate-qnamaker-luis.md)
