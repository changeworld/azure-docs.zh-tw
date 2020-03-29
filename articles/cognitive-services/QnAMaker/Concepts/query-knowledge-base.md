---
title: 查詢知識庫 - QnA 製造商
description: 必須發佈知識庫。 發佈後，使用生成應答 API 在運行時預測終結點查詢知識庫。
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb777aa16fada50811cce1bbf49f28662c62b49b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221461"
---
# <a name="query-the-knowledge-base-for-answers"></a>查詢知識庫以查找答案

必須發佈知識庫。 發佈後，使用生成應答 API 在運行時預測終結點查詢知識庫。 該查詢包括問題文本和其他設置，以説明 QnA Maker 選擇與答案的最佳匹配。

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>QnA Maker 如何處理使用者查詢以選擇最佳答案

經過訓練並[發佈的](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base)QnA Maker 知識庫在[生成應答 API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)接收來自自動程式或其他用戶端應用程式的使用者查詢。 下圖說明瞭接收使用者查詢時的過程。

![使用者查詢的排名模型流程](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Ranker 過程

下表說明了該過程。

|步驟|目的|
|--|--|
|1|用戶端應用程式將使用者查詢發送到[生成應答 API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)。|
|2|QnA Maker 使用語言檢測、拼寫器和斷字元預先處理使用者查詢。|
|3|此預處理用於更改使用者查詢以獲得最佳搜尋結果。|
|4|此更改的查詢將發送到 Azure 認知搜索索引，該索引接收`top`結果數。 如果正確答案不在這些結果中，請稍微增加值`top`。 通常，90% 的查詢`top`中工作的值為 10。|
|5|QnA Maker 使用句法和基於語義的壯舉來確定使用者查詢與提取的 QnA 結果之間的相似性。|
|6|機器學習的記號模型使用步驟 5 中的不同功能來確定置信度分數和新的排名順序。|
|7|新結果按排名順序返回給用戶端應用程式。|
|||

使用的功能包括但不限於單詞級語義、語料庫中的術語級重要性以及深學語義模型，以確定兩個文本字串之間的相似性和相關性。

## <a name="http-request-and-response-with-endpoint"></a>使用終結點進行 HTTP 要求和回應
發佈知識庫時，該服務將創建一個基於 REST 的 HTTP 終結點，該終結點可以集成到應用程式中，通常是聊天機器人。

### <a name="the-user-query-request-to-generate-an-answer"></a>生成答案的使用者查詢請求

使用者查詢是最終使用者詢問的知識庫的問題，例如`How do I add a collaborator to my app?`。 查詢通常採用自然語言格式或代表問題的幾個關鍵字，如`help with collaborators`。 查詢將從用戶端應用程式中的 HTTP 要求發送到知識庫。

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

您可以通過設置諸如[分數閾值](./confidence-score.md#choose-a-score-threshold)、[頂部](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)和[嚴格篩選器](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags)等屬性來控制回應。

使用具有[多轉功能](../how-to/multiturn-conversation.md)[的對話上下文](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context)來保持對話的優化問題和答案，以找到正確和最終的答案。

### <a name="the-response-from-a-call-to-generate-an-answer"></a>來自呼叫的回應以生成應答

HTTP 回應是根據給定使用者查詢的最佳匹配從知識庫中檢索的答案。 回應包括答案和預測分數。 如果您要求多個頂部答案的屬性`top`，你會得到多個頂部答案，每個帶有分數。

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
> [置信度得分](./confidence-score.md)
