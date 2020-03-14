---
title: 查詢知識庫-QnA Maker
description: 必須發行知識庫。 發佈之後，就會使用 generateAnswer API，在執行時間預測端點上查詢知識庫。
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb777aa16fada50811cce1bbf49f28662c62b49b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79221461"
---
# <a name="query-the-knowledge-base-for-answers"></a>查詢知識庫中的答案

必須發行知識庫。 發佈之後，就會使用 generateAnswer API，在執行時間預測端點上查詢知識庫。 此查詢包含問題文字和其他設定，以協助 QnA Maker 選取最可能的答案相符項。

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>QnA Maker 如何處理使用者查詢以選取最佳答案

定型和[發佈](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base)的 QnA Maker 知識庫會在[GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)接收使用者查詢，從 bot 或其他用戶端應用程式。 下圖說明接收使用者查詢的過程。

![使用者查詢的排名模型進程](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Ranker 流程

下表將說明此程式。

|步驟|目的|
|--|--|
|1|用戶端應用程式會將使用者查詢傳送至[GENERATEANSWER API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)。|
|2|QnA Maker 使用語言偵測、spellers 和斷詞工具來對使用者查詢進行前置處理。|
|3|採取此前置處理來改變使用者查詢的最佳搜尋結果。|
|4|此變更的查詢會傳送至 Azure 認知搜尋索引，以接收 `top` 的結果數目。 如果這些結果中沒有正確的答案，請稍微增加 `top` 的值。 一般來說，`top` 的值為10，可在90% 的查詢中運作。|
|5|QnA Maker 會使用語法和以語義為基礎的特徵化來判斷使用者查詢與提取的 QnA 結果之間的相似性。|
|6|機器學習的 ranker 模型會使用步驟5中的不同功能來判斷信賴分數和新的排名順序。|
|7|新的結果會依排名順序傳回用戶端應用程式。|
|||

使用的功能包括但不限於單字層級的語義、主體中的詞彙層級重要性，以及深度學習的語義模型，以判斷兩個文字字串之間的相似性與相關性。

## <a name="http-request-and-response-with-endpoint"></a>使用端點的 HTTP 要求和回應
當您發佈知識庫時，服務會建立以 REST 為基礎的 HTTP 端點，可以整合到您的應用程式中，通常是聊天機器人。

### <a name="the-user-query-request-to-generate-an-answer"></a>產生解答的使用者查詢要求

使用者查詢是終端使用者要求知識庫的問題，例如 `How do I add a collaborator to my app?`。 查詢通常採用自然語言格式，或幾個代表問題的關鍵字，例如 `help with collaborators`。 查詢會從用戶端應用程式中的 HTTP 要求傳送至您的知識庫。

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

您可以藉由設定屬性（例如[scoreThreshold](./confidence-score.md#choose-a-score-threshold)、 [top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)和[strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags)）來控制回應。

使用具有[多重回合功能](../how-to/multiturn-conversation.md)的[對話內容](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context)，讓交談能夠縮小問題和答案，以尋找正確和最終的答案。

### <a name="the-response-from-a-call-to-generate-an-answer"></a>來自呼叫以產生解答的回應

HTTP 回應是根據指定使用者查詢的最相符項，從知識庫中抓取的答案。 回應包含答案和預測分數。 如果您要求使用 `top` 屬性的一個以上的頂尖答案，您會得到一個以上的頂尖答案，每個都有一個分數。

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
