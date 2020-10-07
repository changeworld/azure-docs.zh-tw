---
title: 查詢知識庫-QnA Maker
description: 必須發行知識庫。 發佈之後，知識庫會使用 generateAnswer API 在執行時間預測端點上進行查詢。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: e903714aab35de40c1179045505e1520c65b3ebc
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776913"
---
# <a name="query-the-knowledge-base-for-answers"></a>查詢知識庫以取得答案

必須發行知識庫。 發佈之後，知識庫會使用 generateAnswer API 在執行時間預測端點上進行查詢。 此查詢包含問題文字和其他設定，可協助 QnA Maker 選取最可能的答案相符。

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>QnA Maker 如何處理使用者查詢來選取最佳答案

經過定型和 [發佈](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) 的 QnA Maker 知識庫會從 bot 或其他用戶端應用程式的 [GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)接收使用者查詢。 下圖說明收到使用者查詢的進程。

![使用者查詢的排名模型進程](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Ranker 流程

下表將說明此程式。

|步驟|目的|
|--|--|
|1|用戶端應用程式會將使用者查詢傳送至 [GENERATEANSWER API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)。|
|2|QnA Maker 使用語言偵測、spellers 和斷詞工具來對使用者查詢進行前置處理。|
|3|此前置處理是為了改變使用者查詢以取得最佳搜尋結果。|
|4|這項改變的查詢會傳送至 Azure 認知搜尋索引，以接收 `top` 結果數目。 如果這些結果中沒有正確的答案，請將值 `top` 稍微增加。 一般來說，10的值適用于 `top` 90% 的查詢。|
|5|QnA Maker 使用語法和語義型特徵化來判斷使用者查詢與提取 QnA 結果之間的相似性。|
|6|機器學習的 ranker 模型會使用步驟5中的不同功能來判斷信賴分數和新的排名順序。|
|7|新的結果會以排名的順序傳回用戶端應用程式。|
|||

使用的功能包括但不限於文字層級的語義、主體中的詞彙層級重要性，以及深入學習的語義模型，以判斷兩個文字字串之間的相似性和相關性。

## <a name="http-request-and-response-with-endpoint"></a>使用端點的 HTTP 要求和回應
當您發佈知識庫時，此服務會建立可整合到您應用程式中的 REST 型 HTTP 端點，通常是聊天機器人。

### <a name="the-user-query-request-to-generate-an-answer"></a>用來產生答案的使用者查詢要求

使用者查詢是終端使用者要求知識庫的問題，例如 `How do I add a collaborator to my app?` 。 查詢通常會使用自然語言格式或一些代表問題的關鍵字，例如 `help with collaborators` 。 查詢會從用戶端應用程式中的 HTTP 要求傳送至您的知識庫。

```json
{
    "question": "How do I add a collaborator to my app?",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "QuestionType",
        "value": "Support"
    }],
    "userId": "sd53lsY="
}
```

您可以藉由設定屬性（例如 [scoreThreshold](./confidence-score.md#choose-a-score-threshold)、 [top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)和 [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags)）來控制回應。

使用具有[多回合功能](../how-to/multiturn-conversation.md)的[對話內容](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context)，可讓對話持續改進問題和答案，以找出正確和最後的答案。

### <a name="the-response-from-a-call-to-generate-an-answer"></a>呼叫產生答案的回應

HTTP 回應是根據指定使用者查詢的最符合項，從知識庫中取出的答案。 回應會包含答案和預測分數。 如果您向屬性要求一個以上的 top 解答 `top` ，您會得到一個以上的最佳答案，各有一個分數。

```json
{
    "answers": [
        {
            "questions": [
                "How do I add a collaborator to my app?",
                "What access control is provided for the app?",
                "How do I find user management and security?"
            ],
            "answer": "Use the Azure portal to add a collaborator using Access Control (IAM)",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "QuestionType",
                    "value": "Support"
                },
                {
                    "name": "ToolDependency",
                    "value": "Azure Portal"
                }
            ]
        }
    ]
}
```


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [信賴分數](./confidence-score.md)
