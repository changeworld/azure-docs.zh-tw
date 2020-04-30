---
title: 包含檔案
description: 包含檔案
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 04/27/2020
ms.author: diberry
ms.openlocfilehash: 97dfe175a609ab336206098948b4e3fcc401d8bc
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203948"
---
這個以 Postman 為基礎的快速入門會逐步引導您從知識庫取得答案。

## <a name="prerequisites"></a>Prerequisites

* 最新的 [**Postman**](https://www.getpostman.com/) \(英文\)。
* 您必須具有
    * [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)
    * 已定型並發佈的[知識庫，其中具有從快速入門建置得來的問題與答案](../Quickstarts/add-question-metadata-portal.md)，並已使用中繼資料和閒聊資料加以設定。

> [!NOTE]
> 當您準備好從知識庫產生問題的答案時，就必須[定型](../Quickstarts/create-publish-knowledge-base.md#save-and-train)並[發佈](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)知識庫。 發佈知識庫時，[發佈]  頁面會顯示用來產生答案的 HTTP 要求設定。 [Postman]  索引標籤會顯示為了產生答案所需的設定。

## <a name="set-up-postman-for-requests"></a>針對要求來設定 Postman

本快速入門會針對 Postman 的 **POST** 要求使用相同設定，然後根據您所嘗試查詢的內容來設定為傳送至服務的 POST 主體 JSON。

使用此程序來設定 Postman，然後閱讀後續各節以設定 POST 主體 JSON。

1. 從知識庫的 [設定]  頁面，選取 [Postman]  索引標籤來查看用來從知識庫產生答案的設定。 複製下列要用於 Postman 的資訊。

    |名稱|設定|目的和值|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|這是 URL 的 HTTP 方法和路由。|
    |`Host`|`https://YOUR-RESOURCE_NAME.azurewebsites.net/qnamaker`|這是 URL 的主機。 串連 Host 和 Post 值，以取得完整的 generateAnswer URL。|
    |`Authorization`|`EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|用來向 Azure 授權要求的標頭值。 |
    |`Content-type`|`application/json`|內容的標頭值。|
    ||`{"question":"<Your question>"}`|POST 要求的主體，會作為 JSON 物件。 根據查詢的目的而定，在下列各節中，此值會有所變更。|

1. 開啟 Postman，並使用所發佈知識庫的設定來建立新的基本 **POST** 要求。 在下列各節中，修改 POST 主體 JSON 以將查詢變更為您的知識庫。

## <a name="use-metadata-to-filter-answer"></a>使用中繼資料來篩選答案

先前的快速入門已將中繼資料新增至兩個 QnA 配對，以區別兩個不同的問題。 將中繼資料新增至查詢，從而將篩選條件限制為只有相關的 QnA 配對。

1. 在 Postman 中，藉由使用 `service:qna_maker` 的名稱/值組來新增 `strictFilters` 屬性，從而只變更查詢 JSON。 主體 JSON 應該是：

    ```json
    {
        'question':'size',
        'strictFilters': [
            {
                'name':'service','value':'qna_maker'
            }
        ]
    }
    ```

    此問題就只有一個字 `size`，其可以傳回兩個問答集的其中一個。 `strictFilters` 陣列會辨別回應，而將回應減少到只剩 `qna_maker` 答案。

1. 回應只會包含符合篩選準則的答案。

    下列回應已經過格式化，以方便您閱讀：

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    如果有不符合搜尋詞彙但符合篩選條件的問答集，則不會傳回。 相反地，會傳回一般性答案 `No good match found in KB.`。

## <a name="use-debug-query-property"></a>使用 debug 查詢屬性

偵錯資訊可協助您了解所傳回答案的判斷方式。 此資訊雖然有用，但並非必要。 若要使用偵錯資訊來產生答案，請新增 `debug` 屬性：

1. 在 Postman 中，藉由新增 `debug` 屬性而僅變更本文 JSON。 JSON 應為：

    ```json
    {
        'question':'size',
        'Debug': {
            'Enable':true
        }

    }
    ```

1. 回應包含答案的相關資訊。 在下列 JSON 輸出中，某些偵錯詳細資料已取代為省略號。

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base) how to collaborate on a knowledge base.",
                "score": 56.07,
                "id": 5,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": {
            "userQuery": {
                "question": "How do I programmatically update my Knowledge Base?",
                "top": 1,
                "userId": null,
                "strictFilters": [],
                "isTest": false,
                "debug": {
                    "enable": true,
                    "recordL1SearchLatency": false,
                    "mockQnaL1Content": null
                },
                "rankerType": 0,
                "context": null,
                "qnaId": 0,
                "scoreThreshold": 0.0
            },
            "rankerInfo": {
                "specialFuzzyQuery": "how do i programmatically~6 update my knowledge base",
                "synonyms": "what s...",
                "rankerLanguage": "English",
                "rankerFileName": "https://qnamakerstore.blob.core.windows.net/qnamakerdata/rankers/ranker-English.ini",
                "rankersDirectory": "D:\\home\\site\\wwwroot\\Data\\QnAMaker\\rd0003ffa60fc45.24.0\\RankerData\\Rankers",
                "allQnAsfeatureValues": {
                    "WordnetSimilarity": {
                        "5": 0.54706300120043716,...
                    },
                    ...
                },
                "rankerVersion": "V2",
                "rankerModelType": "TreeEnsemble",
                "rankerType": 0,
                "indexResultsCount": 25,
                "reRankerResultsCount": 1
            },
            "runtimeVersion": "5.24.0",
            "indexDebugInfo": {
                "indexDefinition": {
                    "name": "064a4112-bd65-42e8-b01d-141c4c9cd09e",
                    "fields": [...
                    ],
                    "scoringProfiles": [],
                    "defaultScoringProfile": null,
                    "corsOptions": null,
                    "suggesters": [],
                    "analyzers": [],
                    "tokenizers": [],
                    "tokenFilters": [],
                    "charFilters": [],
                    "@odata.etag": "\"0x8D7A920EA5EE6FE\""
                },
                "qnaCount": 117,
                "parameters": {},
                "azureSearchResult": {
                    "continuationToken": null,
                    "@odata.count": null,
                    "@search.coverage": null,
                    "@search.facets": null,
                    "@search.nextPageParameters": null,
                    "value": [...],
                    "@odata.nextLink": null
                }
            },
            "l1SearchLatencyInMs": 0,
            "qnaL1Results": {...}
        },
        "activeLearningEnabled": true
    }
    ```

## <a name="use-test-knowledge-base"></a>使用測試知識庫

如果您想要從測試知識庫取得答案，請使用 `isTest` 主體屬性。

在 Postman 中，藉由新增 `isTest` 屬性以便只變更主體 JSON。 JSON 應為：

```json
{
    'question':'size',
    'isTest': true
}
```

JSON 回應會使用與所發佈知識庫查詢相同的結構描述。

> [!NOTE]
> 如果測試知識庫和所發佈的知識庫完全相同，但因為資源的所有知識庫會共用測試索引，所以其中可能還是會有些微變化。

## <a name="query-for-a-chit-chat-answer"></a>查詢閒聊答案

1. 在 Postman 中，只將本文 JSON 變更為以對話結尾的使用者陳述。 JSON 應為：

    ```json
    {
        'question':'thank you'
    }
    ```

1. 回應包含分數和答案。

    ```json
    {
      "answers": [
          {
              "questions": [
                  "I thank you",
                  "Oh, thank you",
                  "My sincere thanks",
                  "My humblest thanks to you",
                  "Marvelous, thanks",
                  "Marvelous, thank you kindly",
                  "Marvelous, thank you",
                  "Many thanks to you",
                  "Many thanks",
                  "Kthx",
                  "I'm grateful, thanks",
                  "Ahh, thanks",
                  "I'm grateful for that, thank you",
                  "Perfecto, thanks",
                  "I appreciate you",
                  "I appreciate that",
                  "I appreciate it",
                  "I am very thankful for that",
                  "How kind, thank you",
                  "Great, thanks",
                  "Great, thank you",
                  "Gracias",
                  "Gotcha, thanks",
                  "Gotcha, thank you",
                  "Awesome thanks!",
                  "I'm grateful for that, thank you kindly",
                  "thank you pal",
                  "Wonderful, thank you!",
                  "Wonderful, thank you very much",
                  "Why thank you",
                  "Thx",
                  "Thnx",
                  "That's very kind",
                  "That's great, thanks",
                  "That is lovely, thanks",
                  "That is awesome, thanks!",
                  "Thanks bot",
                  "Thanks a lot",
                  "Okay, thanks!",
                  "Thank you so much",
                  "Perfect, thanks",
                  "Thank you my friend",
                  "Thank you kindly",
                  "Thank you for that",
                  "Thank you bot",
                  "Thank you",
                  "Right on, thanks very much",
                  "Right on, thanks a lot",
                  "Radical, thanks",
                  "Rad, thanks",
                  "Rad thank you",
                  "Wonderful, thanks!",
                  "Thanks"
              ],
              "answer": "You're welcome.",
              "score": 100.0,
              "id": 75,
              "source": "qna_chitchat_Professional.tsv",
              "metadata": [
                  {
                      "name": "editorial",
                      "value": "chitchat"
                  }
              ],
              "context": {
                  "isContextOnly": false,
                  "prompts": []
              }
          }
      ],
      "debugInfo": null,
      "activeLearningEnabled": true
    }
    ```

    由於 `Thank you` 的問題完全符合閒聊問題，因此QnA Maker 完全信賴 100 這個分數。 QnA Maker 也會傳回所有相關問題，以及包含閒聊中繼資料標記的中繼資料屬性。

## <a name="use-threshold-and-default-answer"></a>使用閾值和預設答案

您可以針對答案要求最小的閾值。 如果不符合閾值，便傳回預設的答案。

1. 在 Postman 中，只將本文 JSON 變更為以對話結尾的使用者陳述。 JSON 應為：

    ```json
    {
        'question':'size',
        'scoreThreshold':80.00
    }
    ```

    因為問題的分數是 71%，所以知識庫應該不會找到該答案，而是會傳回您在建立知識庫時所提供的預設答案。

    傳回的 JSON 回應 (包括分數和答案) 是：

    ```json
    {
        "answers": [
            {
                "questions": [],
                "answer": "No good match found in KB.",
                "score": 0.0,
                "id": -1,
                "source": null,
                "metadata": []
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```

    QnA Maker 會傳回代表沒有信心的 `0` 分。 它也會傳回預設答案。

1. 將閾值變更為 60%，然後再次要求查詢：

    ```json
    {
        'question':'size',
        'scoreThreshold':60.00
    }
    ```

    傳回的 JSON 找到了答案。

    ```json
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 71.1,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "server",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```