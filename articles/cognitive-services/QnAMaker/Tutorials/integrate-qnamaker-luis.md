---
title: LUIS 和 QnAMaker-bot 整合
titleSuffix: Azure Cognitive Services
description: 當您的 QnA Maker 知識庫變大時，就難以將它維護為單一的整合式集合。 將知識庫分割成較小的邏輯區塊。
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81402712"
---
# <a name="use-a-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>使用具有 QnA Maker 和 LUIS 的 bot 來散發您的知識庫
當您的 QnA Maker 知識庫變大時，就難以將它維護為單一的整合式集合。 將知識庫分割成較小的邏輯區塊。

雖然在 QnA Maker 中建立多個知識庫很簡單，但您需要一些邏輯，才能將傳入問題路由至適當的知識庫。 您可以使用 LUIS 來達成此目的。

本文使用 Bot Framework v3 SDK。 如果您對此資訊的 Bot Framework v4 SDK 版本感興趣，請參閱[使用多個 LUIS 和 QnA 模型](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp)。

## <a name="architecture"></a>架構

![顯示具有 Language Understanding 之 QnA Maker 架構的圖形](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

上圖顯示 QnA Maker 先從 LUIS 模型取得傳入問題的意圖。 然後，QnA Maker 會使用該意圖，將問題路由至正確的 QnA Maker 知識庫。

## <a name="create-a-luis-app"></a>建立 LUIS 應用程式

1. 登入[LUIS](https://www.luis.ai/)入口網站。
1. [建立應用程式](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app)。
1. 針對每個 QnA Maker 知識庫[新增意圖](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents)。 範例語句應對應到 QnA Maker 知識庫中的問題。
1. [訓練 LUIS 應用程式](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train)，並[發佈 LUIS 應用程式](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp)。
1. 在 [**管理**] 區段中，記下您的 LUIS 應用程式識別碼、LUIS 端點金鑰和[自訂功能變數名稱](../../cognitive-services-custom-subdomains.md)。 您稍後將需要這些值。

## <a name="create-qna-maker-knowledge-bases"></a>建立 QnA Maker 知識庫

1. 登入[QnA Maker](https://qnamaker.ai)。
1. 針對 LUIS 應用程式中的每個意圖[建立](https://www.qnamaker.ai/Create)知識庫。
1. 測試並發佈知識庫。 當您發佈每一個帳戶時，請記下識別碼、資源名稱（自訂子域 before _. azurewebsites.net/qnamaker_）和授權端點金鑰。 您稍後將需要這些值。

    本文假設所有知識庫都是在相同的 Azure QnA Maker 訂用帳戶中建立。

    ![QnA Maker HTTP 要求的螢幕擷取畫面](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Web 應用程式 Bot

1. [使用 Azure Bot Service 建立「基本」 bot](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0)，這會自動包含 LUIS 應用程式。 選取 c # 程式設計語言。

1. 建立 web 應用程式 bot 之後，請在 [Azure 入口網站中選取 web 應用程式 bot。
1. 在 web 應用程式 bot 服務導覽中，選取 [**應用程式設定**]。 然後向下卷到 [可用設定] 的 [**應用程式設定**] 區段。
1. 將**LuisAppId**變更為上一節中所建立之 LUIS 應用程式的值。 然後選取 [儲存]  。


## <a name="change-the-code-in-the-basicluisdialogcs-file"></a>變更 BasicLuisDialog.cs 檔案中的程式碼
1. 在 Azure 入口網站的 Web 應用程式 Bot 導覽 [Bot 管理]**** 區段中，選取 [建置]****。
2. 選取 [開啟線上程式碼編輯器]****。 隨即會開啟新瀏覽器索引標籤與線上編輯環境。
3. 在 [ **WWWROOT** ] 區段中，選取 [**對話方塊**] 目錄，然後開啟 [ **BasicLuisDialog.cs**]。
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

5. 新增下列類別，以還原序列化 QnA Maker 回應：

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


6. 新增下列類別，以向 QnA Maker 服務發出 HTTP 要求。 請注意，**授權**標頭的值包含單字， `EndpointKey`並在此單字後面加上空格。 JSON 結果會還原序列化為上述類別，並傳回第一個答案。

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


7. 修改`BasicLuisDialog`類別。 每個 LUIS 意圖都應該有使用 **LuisIntent** 裝飾的方法。 裝飾的參數是實際的 LUIS 意圖名稱。 裝飾的方法名稱_應_為可讀性和可維護性的 LUIS 意圖名稱，但在設計或執行時間中不一定要相同。

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
1. 在 [程式碼編輯器] 中，以滑鼠右鍵按一下 [ **build .cmd**]，然後選取 [**從主控台執行**]。

    ![[程式碼編輯器] 中的 [從主控台執行] 選項的螢幕擷取畫面](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. 程式碼視圖會取代為終端機視窗，其中會顯示組建的進度和結果。

    ![主控台組建的螢幕擷取畫面](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>測試 Bot
在 Azure 入口網站中，選取 [Test in Web Chat]\(在網路聊天中測試\)**** 以測試 Bot。 鍵入不同意圖的訊息，以便取得對應的知識庫之中的回應。

![網路聊天測試的螢幕擷取畫面](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [將您的知識庫與 Power Virtual Agent 中的代理程式整合](integrate-with-power-virtual-assistant-fallback-topic.md)
