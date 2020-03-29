---
title: 改善知識庫 - QnA Maker
description: 通過積極學習提高知識庫的品質。 審核、接受或拒絕，添加而不刪除或更改現有問題。
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 8b2176731ce456b70521cc9208435d0b19943ab8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053076"
---
# <a name="accept-active-learning-suggested-questions-in-the-knowledge-base"></a>在知識庫中接受主動學習建議的問題


<a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>

"活動學習"在批准建議後更改知識庫或搜索服務，然後保存並訓練。 如果您批准該建議，它將作為備用問題添加。

## <a name="turn-on-active-learning"></a>開啟主動式學習

要查看建議的問題，您必須為 QnA Maker 資源[打開主動學習](use-active-learning.md)。

## <a name="view-suggested-questions"></a>查看建議的問題

1. 要查看建議的問題，在 **"編輯**知識庫"頁上，選擇 **"查看選項**"，然後選擇 **"顯示活動學習建議**"。

    [![在門戶的"編輯"部分，選擇"顯示建議"以查看活動學習的新問題備選方案。](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. 使用問答對篩選知識庫，通過選擇 **"按建議篩選"** 來僅顯示建議。

    [![使用"按建議篩選"切換僅查看活動學習建議的問題備選方案。](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. 每個 QnA 對都建議使用核取記號的新問題備選方案`✔`，以接受問題或拒絕`x`建議。 選取核取記號可新增問題。

    [![通過選擇綠色核取記號或紅色刪除標記，選擇或拒絕活動學習建議的問題備選方案。](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    您可以通過選擇 **"添加所有**"或 **"拒絕**上下文工具列中的所有建議"來添加或刪除_所有建議_。

1. 選取 [儲存並訓練]****，以儲存對知識庫所做的變更。

1. 選擇 **"發佈"** 以允許從[生成應答 API](metadata-generateanswer-usage.md#generateanswer-request-configuration)中提供更改。

    當 5 個或更多類似的查詢被群集時，每 30 分鐘，QnA Maker 會建議您接受或拒絕其他問題。


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>用於使用來自自動程式的生成應答和訓練 API 的體系結構流

自動程式或其他用戶端應用程式應使用以下體系結構流來使用活動學習：

* Bot 使用生成應答 API[從知識庫中獲取答案](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)，使用`top`屬性獲取多個答案。
* 機器人確定顯式回饋：
    * 使用您自己的[自訂業務邏輯](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)，過濾掉低分數。
    * 在自動程式或用戶端應用程式中，顯示使用者可能的答案清單，並獲取使用者選擇的答案。
* 機器人使用[訓練 API](#train-api)[將選定的答案發送回 QnA 製造商](#bot-framework-sample-code)。


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>使用"生成應答"請求中的頂部屬性獲取多個匹配的答案

向 QnA Maker 提交問題以進行回答時，JSON`top`正文的屬性將設置要返回的答案數。

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>使用分數屬性以及業務邏輯獲取答案清單以顯示使用者

當用戶端應用程式（如聊天機器人）收到回應時，將返回前 3 個問題。 使用`score`屬性分析分數之間的鄰近程度。 此鄰近範圍由您自己的業務邏輯決定。

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ *+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>用戶端應用程式在問題具有類似分數時的後續動作

用戶端應用程式顯示問題，並允許使用者選擇最代表其意圖_的單一問題_。

使用者選擇現有問題之一後，用戶端應用程式將使用 QnA Maker 的火車 API 將使用者的選擇作為回饋發送。 此回饋完成活動學習回饋迴圈。

## <a name="train-api"></a>將 API 定型

主動學習回饋通過培訓 API POST 要求傳送給 QnA 製造商。 API 簽名是：

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|HTTP 要求屬性|名稱|類型|目的|
|--|--|--|--|
|URL 路由參數|知識庫識別碼|字串|測試您知識庫的 GUID。|
|自訂子域|QnAMaker 資源名稱|字串|資源名稱用作 QnA Maker 的自訂子域。 在發佈知識庫後，"設置"頁上提供了此功能。 它被列為 。 `host`|
|頁首|Content-Type|字串|傳送至 API 的本文媒體類型。 預設值為：`application/json`|
|頁首|授權|字串|您的端點金鑰 (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)。|
|張貼本文|JSON 物件|JSON|培訓回饋|

JSON 正文具有多個設置：

|JSON 正文屬性|類型|目的|
|--|--|--|--|
|`feedbackRecords`|array|回饋清單。|
|`userId`|字串|接受建議問題的人員的使用者 ID。 使用者 ID 格式由您決定。 例如，電子郵件地址可以是體系結構中的有效使用者 ID。 選擇性。|
|`userQuestion`|字串|使用者查詢的確切文本。 必要。|
|`qnaID`|number|問題的 ID，在[生成答案回應](metadata-generateanswer-usage.md#generateanswer-response-properties)中找到。 |

JSON 正文的示例如下所示：

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

成功回應返回狀態為 204，沒有 JSON 回應機構。

### <a name="batch-many-feedback-records-into-a-single-call"></a>將許多回饋記錄批次處理為單個呼叫

在用戶端應用程式中（如自動程式），可以存儲資料，然後在`feedbackRecords`陣列中的單個 JSON 正文中發送許多記錄。

JSON 正文的示例如下所示：

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "How do I ...",
            "qnaId": 1
        },
        {
            "userId": "2",
            "userQuestion": "Where is ...",
            "qnaId": 40
        },
        {
            "userId": "3",
            "userQuestion": "When do I ...",
            "qnaId": 33
        }
    ]
}
```



<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>機器人框架示例代碼

如果使用者的查詢應用於活動學習，則自動程式框架代碼需要調用訓練 API。 有兩段代碼要編寫：

* 確定是否應將查詢用於活動學習
* 將查詢發送回 QnA 製造商的列車 API 以進行主動學習

在[Azure 機器人示例中](https://aka.ms/activelearningsamplebot)，這兩個活動都已程式設計。

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>使用機器人框架 4.x 訓練 API 的示例 C# 代碼

以下代碼說明了如何使用訓練 API 將資訊發送回 QnA 製造商。 此[完整的代碼示例](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-activelearning/csharp_dotnetcore)可在 GitHub 上使用。

```csharp
public class FeedbackRecords
{
    // <summary>
    /// List of feedback records
    /// </summary>
    [JsonProperty("feedbackRecords")]
    public FeedbackRecord[] Records { get; set; }
}

/// <summary>
/// Active learning feedback record
/// </summary>
public class FeedbackRecord
{
    /// <summary>
    /// User id
    /// </summary>
    public string UserId { get; set; }

    /// <summary>
    /// User question
    /// </summary>
    public string UserQuestion { get; set; }

    /// <summary>
    /// QnA Id
    /// </summary>
    public int QnaId { get; set; }
}

/// <summary>
/// Method to call REST-based QnAMaker Train API for Active Learning
/// </summary>
/// <param name="endpoint">Endpoint URI of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string endpoint, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = endpoint + "/knowledgebases/" + kbId + "/train/";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(uri);
            request.Content = new StringContent(JsonConvert.SerializeObject(feedbackRecords), Encoding.UTF8, "application/json");
            request.Headers.Add("Authorization", "EndpointKey " + key);

            var response = await client.SendAsync(request, cancellationToken);
            await response.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>帶有 Bot 框架 4.x 的火車 API 的示例 Node.js 代碼

以下代碼說明了如何使用訓練 API 將資訊發送回 QnA 製造商。 此[完整的代碼示例](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs)可在 GitHub 上使用。

```javascript
async callTrain(stepContext){

    var trainResponses = stepContext.values[this.qnaData];
    var currentQuery = stepContext.values[this.currentQuery];

    if(trainResponses.length > 1){
        var reply = stepContext.context.activity.text;
        var qnaResults = trainResponses.filter(r => r.questions[0] == reply);

        if(qnaResults.length > 0){

            stepContext.values[this.qnaData] = qnaResults;

            var feedbackRecords = {
                FeedbackRecords:[
                    {
                        UserId:stepContext.context.activity.id,
                        UserQuestion: currentQuery,
                        QnaId: qnaResults[0].id
                    }
                ]
            };

            // Call Active Learning Train API
            this.activeLearningHelper.callTrain(this.qnaMaker.endpoint.host, feedbackRecords, this.qnaMaker.endpoint.knowledgeBaseId, this.qnaMaker.endpoint.endpointKey);

            return await stepContext.next(qnaResults);
        }
        else{

            return await stepContext.endDialog();
        }
    }

    return await stepContext.next(stepContext.result);
}
```

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>主動學習保存在匯出的知識庫中

當應用啟用了活動學習並匯出應用時，tsv`SuggestedQuestions`檔中的列將保留活動學習資料。

該`SuggestedQuestions`列是隱式、`autosuggested`顯式回饋和顯式`usersuggested`回饋資訊的 JSON 物件。 對於單個使用者提交的問題，此 JSON 物件的示例`help`是：

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

您還可以使用下載更改 API 使用 REST 或任何基於語言的 SDK 來查看這些更改：
* [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fc)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.alterationsextensions.getasync?view=azure-dotnet)


當您重新導入此應用程式時，活動學習將繼續收集資訊並為您的知識庫提供建議。



## <a name="best-practices"></a>最佳作法

如需使用主動式學習時的最佳做法，請參閱[最佳做法](../Concepts/best-practices.md#active-learning)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [透過 GenerateAnswer API 使用中繼資料](metadata-generateanswer-usage.md)
