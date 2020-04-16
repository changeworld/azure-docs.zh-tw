---
title: LUIS 與 QnAMaker - 機器人整合
titleSuffix: Azure Cognitive Services
description: 隨著 QnA Maker 知識庫的擴大,很難將其作為單一集進行維護。 將知識庫拆分為更小的邏輯區塊。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b0d28c77966668f919cdf1265f8cc63b4931d5fd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402712"
---
# <a name="use-a-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>使用帶有 QnA 製造商和 LUIS 的機器人來分發您的知識庫
隨著 QnA Maker 知識庫的擴大,很難將其作為單一集進行維護。 將知識庫拆分為更小的邏輯區塊。

儘管在 QnA Maker 中創建多個知識庫非常簡單,但您需要一些邏輯才能將傳入的問題路由到相應的知識庫。 您可以使用 LUIS 來達成此目的。

本文使用機器人框架 v3 SDK。 如果您對此資訊的 Bot Framework v4 SDK 版本感興趣,請參閱[使用多個 LUIS 和 QnA 模型](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp)。

## <a name="architecture"></a>架構

![顯示具有語言理解的 QnA 製造商架構的圖像圖形](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

上圖顯示QnA Maker首先從LUIS模型獲取傳入問題的意圖。 然後,QnA Maker 使用該意圖將問題路由到正確的 QnA Maker 知識庫。

## <a name="create-a-luis-app"></a>建立 LUIS 應用程式

1. 登錄到[LUIS](https://www.luis.ai/)門戶。
1. [建立套用](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app)。
1. 針對每個 QnA Maker 知識庫[新增意圖](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents)。 範例語句應對應到 QnA Maker 知識庫中的問題。
1. [訓練 LUIS 應用程式](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train)並[發佈 LUIS 應用程式](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp)。
1. 在 **'管理'** 部份中,關閉點金鑰與[自訂網域名稱](../../cognitive-services-custom-subdomains.md)。 您稍後將需要這些值。

## <a name="create-qna-maker-knowledge-bases"></a>建立 QnA Maker 知識庫

1. 登入[QnA 製造商](https://qnamaker.ai)。
1. 為 LUIS 應用中的每個意圖[創建](https://www.qnamaker.ai/Create)知識庫。
1. 測試並發佈知識庫。 發佈每個子域時,請注意 ID、資源名稱 _(.azurewebsites.net/qnamaker_之前的自定義子域)和授權終結點密鑰。 您稍後將需要這些值。

    本文假定知識庫都在同一 Azure QnA Maker 訂閱中創建。

    ![QnA 製造商 HTTP 要求的螢幕截圖](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Web 應用程式 Bot

1. [使用 Azure 自動程式服務創建「基本」機器人](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0),該自動包含 LUIS 應用。 選擇 C# 程式設計語言。

1. 創建 Web 應用程式自動程式後,在 Azure 入口中選擇 Web 應用自動程式。
1. 在 Web 應用程式服務導覽中,選擇 **「應用程式設定**」 。。 然後向下滾動到可用**設置的應用程式設置**部分。
1. 將**LuisAppId**更改為上一節中創建的 LUIS 應用的值。 然後選擇 **「保存**」。


## <a name="change-the-code-in-the-basicluisdialogcs-file"></a>變更BasicLuisDialog.cs檔案中的代碼
1. 在 Azure 入口網站的 Web 應用程式 Bot 導覽 [Bot 管理]**** 區段中，選取 [建置]****。
2. 選取 [開啟線上程式碼編輯器]****。 隨即會開啟新瀏覽器索引標籤與線上編輯環境。
3. 在**WWWROOT**部份中,選擇 **「對話框」** 目錄,然後開啟**BasicLuisDialog.cs**。
4. 將相依性加入至 **BasicLuisDialog.cs** 檔案的頂端：

    ```csharp
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Luis;
    using Microsoft.Bot.Builder.Luis.Models;
    using Newtonsoft.Json;
    using System.Text;
    ```

5. 新增以下類別以取消序列化 QnA Maker 回應:

    ```csharp
    public class Metadata
    {
        public string name { get; set; }
        public string value { get; set; }
    }

    public class Answer
    {
        public IList<string> questions { get; set; }
        public string answer { get; set; }
        public double score { get; set; }
        public int id { get; set; }
        public string source { get; set; }
        public IList<object> keywords { get; set; }
        public IList<Metadata> metadata { get; set; }
    }

    public class QnAAnswer
    {
        public IList<Answer> answers { get; set; }
    }
    ```


6. 新增下列類別，以向 QnA Maker 服務發出 HTTP 要求。 請注意,**授權**標頭的值包括單`EndpointKey`詞 , 單詞後有一個空格。 JSON 結果被反序列化到前面的類中,並返回第一個答案。

    ```csharp
    [Serializable]
    public class QnAMakerService
    {
        private string qnaServiceResourceName;
        private string knowledgeBaseId;
        private string endpointKey;

        public QnAMakerService(string resourceName, string kbId, string endpointkey)
        {
            qnaServiceResourceName = resourceName;
            knowledgeBaseId = kbId;
            endpointKey = endpointkey;

        }
        async Task<string> Post(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Authorization", "EndpointKey " + endpointKey);

                var response = await client.SendAsync(request);
                return  await response.Content.ReadAsStringAsync();
            }
        }
        public async Task<string> GetAnswer(string question)
        {
            string uri = qnaServiceResourceName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer";
            string questionJSON = "{\"question\": \"" + question.Replace("\"","'") +  "\"}";

            var response = await Post(uri, questionJSON);

            var answers = JsonConvert.DeserializeObject<QnAAnswer>(response);
            if (answers.answers.Count > 0)
            {
                return answers.answers[0].answer;
            }
            else
            {
                return "No good match found.";
            }
        }
    }
    ```


7. 變更類別`BasicLuisDialog`。 每個 LUIS 意圖都應該有使用 **LuisIntent** 裝飾的方法。 裝飾的參數是實際的 LUIS 意圖名稱。 修飾的方法名稱_應_為可讀性和可維護性的 LUIS 意圖名稱,但在設計或運行時不必相同。

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all knowledge bases are created with same Azure service
        static string qnamaker_endpointKey = "<QnA Maker endpoint KEY>";
        static string qnamaker_resourceName = "my-qnamaker-s0-s";

        // QnA Maker Human Resources Knowledge base
        static string HR_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // QnA Maker Finance Knowledge base
        static string Finance_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // Instantiate the knowledge bases
        public QnAMakerService hrQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", HR_kbID, qnamaker_endpointKey);
        public QnAMakerService financeQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", Finance_kbID, qnamaker_endpointKey);

        public BasicLuisDialog() : base(new LuisService(new LuisModelAttribute(
            LUIS_appId,
            LUIS_apiKey,
            domain: LUIS_hostRegion)))
        {
        }

        [LuisIntent("None")]
        public async Task NoneIntent(IDialogContext context, LuisResult result)
        {
            HttpClient client = new HttpClient();
            await this.ShowLuisResult(context, result);
        }

        // HR Intent
        [LuisIntent("HR")]
        public async Task HumanResourcesIntent(IDialogContext context, LuisResult result)
        {
            // Ask the HR knowledge base
            var qnaMakerAnswer = await hrQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }

        // Finance intent
        [LuisIntent("Finance")]
        public async Task FinanceIntent(IDialogContext context, LuisResult result)
        {
            // Ask the finance knowledge base
            var qnaMakerAnswer = await financeQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }
        private async Task ShowLuisResult(IDialogContext context, LuisResult result)
        {
            await context.PostAsync($"You have reached {result.Intents[0].Intent}. You said: {result.Query}");
            context.Wait(MessageReceived);
        }
    }
    ```


## <a name="build-the-bot"></a>建置 Bot
1. 在代碼編輯器中,右鍵按一下**build.cmd,** 然後選擇「**從控制台執行**」。

    ![代碼編輯器中「從主控台執行」選項的螢幕截圖](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. 代碼檢視替換為顯示生成進度和結果的終端視窗。

    ![主控台產生螢幕擷取](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>測試 Bot
在 Azure 入口網站中，選取 [Test in Web Chat]\(在網路聊天中測試\)**** 以測試 Bot。 鍵入不同意圖的訊息，以便取得對應的知識庫之中的回應。

![網路聊天測試的螢幕擷取](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [將您的知識庫與 Power 虛擬代理中的代理整合](integrate-with-power-virtual-assistant-fallback-topic.md)
