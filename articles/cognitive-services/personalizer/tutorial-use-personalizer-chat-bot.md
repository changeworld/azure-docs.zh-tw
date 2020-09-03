---
title: 在聊天機器人中使用個人化工具 - 個人化工具
description: 使用個人化工具迴圈自訂 C# .Net 聊天機器人，以根據動作 (具有特性) 和內容特性來提供正確內容給使用者。
ms.topic: tutorial
ms.date: 07/17/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ee037632b068b0d8d2f3ed3b6ea7a83d157c5083
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935648"
---
# <a name="tutorial-use-personalizer-in-net-chat-bot"></a>教學課程：在 .NET 聊天機器人中使用個人化工具

搭配使用 C# .NET 聊天機器人與個人化工具迴圈，以提供正確內容給使用者。 此聊天機器人會向使用者建議特定的咖啡或茶。 使用者可以接受或拒絕該建議。 這會讓個人化工具獲得資訊，而有助於提供更適當的下一個建議。

**在本教學課程中，您將了解如何：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 設定 Azure 資源
> * 設定及執行聊天機器人
> * 使用聊天機器人模擬器與聊天機器人互動
> * 了解聊天機器人會在哪裡以什麼方式使用個人化工具


## <a name="how-does-the-chat-bot-work"></a>聊天機器人的運作方式為何？

聊天機器人一般會與使用者來回對話。 這個特定的聊天機器人會使用個人化工具來選取要向使用者提供的最佳動作 (咖啡或茶)。 個人化工具會使用增強式學習來做出該選擇。

聊天機器人必須管理對話中的回合。 聊天機器人會使用 [Bot Framework](https://github.com/microsoft/botframework-sdk) 來管理聊天機器人的架構和對話，並使用認知服務 [Language Understanding](../LUIS/index.yml) (LUIS) 來了解使用者所說的自然語言所代表的意圖。

聊天機器人是一個網站，具有可用來回答要求的特定路由：`http://localhost:3978/api/messages`。 當您在本機開發聊天機器人時，您可以使用聊天機器人模擬器，以視覺化方式與執行中的聊天機器人互動。

### <a name="user-interactions-with-the-bot"></a>使用者與聊天機器人的互動

這是簡單的聊天機器人，可讓您輸入文字查詢。

|使用者輸入文字|聊天機器人以文字回應|聊天機器人為了判斷回應文字所採用動作的說明|
|--|--|--|
|未輸入文字 - 聊天機器人開始對話。|`This is a simple chatbot example that illustrates how to use Personalizer. The bot learns what coffee or tea order is preferred by customers given some context information (such as weather, temperature, and day of the week) and information about the user.`<br>`To use the bot, just follow the prompts. To try out a new imaginary context, type “Reset” and a new one will be randomly generated.`<br>`Welcome to the coffee bot, please tell me if you want to see the menu or get a coffee or tea suggestion for today. Once I’ve given you a suggestion, you can reply with ‘like’ or ‘don’t like’. It’s Tuesday today and the weather is Snowy.`|聊天機器人會用說明文字來開始對話，並讓您知道所處的內容為何：`Tuesday`、`Snowy`。|
|`Show menu`|`Here is our menu: Coffee: Cappuccino Espresso Latte Macchiato Mocha Tea: GreenTea Rooibos`|使用 LUIS 判斷查詢的意圖，然後顯示咖啡和茶品項的菜單選項。 動作的特性包括 |
|`What do you suggest`|`How about Latte?`|使用 LUIS 判斷查詢的意圖，然後呼叫**排名 API**，並顯示排名最高的選項作為問題 `How about {response.RewardActionId}?`。 也會顯示 JSON 呼叫和回應，以供說明之用。|
|`I like it`|`That’s great! I’ll keep learning your preferences over time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|使用 LUIS 判斷查詢的意圖，然後以獎勵 `1` 呼叫**獎勵 API**，顯示 JSON 呼叫和回應以供說明之用。|
|`I don't like it`|`Oh well, maybe I’ll guess better next time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|使用 LUIS 判斷查詢的意圖，然後以獎勵 `0` 呼叫**獎勵 API**，顯示 JSON 呼叫和回應以供說明之用。|
|`Reset`|傳回說明文字。|使用 LUIS 判斷查詢的意圖，然後顯示說明文字並重設內容。|


### <a name="personalizer-in-this-bot"></a>此聊天機器人中的個人化工具

此聊天機器人會使用個人化工具，根據_動作_清單 (某些內容類型) 和內容特性來選取排名最高的動作 (特定咖啡或茶)。

聊天機器人會將動作清單連同內容特性傳送至個人化工具迴圈。 個人化工具會向聊天機器人傳回單一的最佳動作，聊天機器人則將其顯示出來。

在本教學課程中，**動作**是咖啡和茶的類型：

|咖啡|茶|
|--|--|
|卡布奇諾<br>濃縮咖啡<br>拿鐵<br>摩卡|綠茶<br>南非國寶茶|

**排名 API：** 為了協助個人化工具了解您的動作，聊天機器人會在每個排名 API 要求中傳送下列資訊：

* _具有特性_的動作
* 內容特性

模型的**特性**是可跨聊天機器人使用者群體的成員加以彙總 (群組) 的動作或內容相關資訊。 特性_不會_具有個別特定性質 (例如使用者識別碼) 或高度特定性 (例如一天之中的確切時間)。

特性可用來讓動作符合模型中的目前內容。 模型代表個人化工具過去關於動作、內容及其特性的知識，可讓其做出明智決策。

模型 (包括特性) 會根據您在 Azure 入口網站中的 [模型更新頻率] 設定，依排程進行更新。

在選取特性時，應使用與您要套用到技術架構中所含任何結構描述或模型相同的規劃與設計。 特性值可以使用商務邏輯或第三方系統來加以設定。

> [!CAUTION]
> 此應用程式中的特性是為了示範，不一定是最適合您使用案例中的 Web 應用程式使用的特性。

#### <a name="action-features"></a>動作特性

每個動作 (內容項目) 都有可協助區分咖啡或茶品項的特性。

Azure 入口網站不會將特性設定為迴圈設定的一部分。 相反地，會於每個排名 API 呼叫中以 JSON 物件的形式來傳送這些特性。 這讓動作及其特性能夠彈性地在一段時間內增加、變更和縮減，而讓個人化工具能夠追隨趨勢。

咖啡和茶的特性包括：

* 咖啡豆產地，例如肯雅和巴西
* 咖啡或茶是否為有機的？
* 淺烘焙或深烘焙的咖啡

雖然咖啡有上述清單中的三個特性，但茶的特性只有一個。 請只將對動作有意義的特性傳遞給個人化工具。 如果某個特性不適用於動作，則請勿針對該特性傳入空白值。

#### <a name="context-features"></a>內容特性

內容特性有助於個人化工具了解環境的內容，例如顯示裝置、使用者、所在位置，以及與您的使用案例相關的其他特性。

此聊天機器人的內容包括：

* 天氣類型 (下雪、下雨、晴朗)
* 星期幾

此聊天機器人會隨機選取特性。 在實際的聊天機器人中，則請針對內容特性使用實際的資料。

### <a name="design-considerations-for-this-bot"></a>此聊天機器人的設計考量

關於此對話，有幾點必須注意：
* **聊天機器人的互動**：對話非常簡單，因為其目的是在簡單的使用案例中示範排名和獎勵。 其不會示範 Bot Framework SDK 或模擬器的完整功能。
* **個人化工具**：會隨機選取特性來模擬使用方式。 在生產環境的個人化工具案例中，則請勿隨機選取特性。
* **Language Understanding (LUIS)** ：LUIS 模型只有少數範例語句的情況僅適用於此範例。 請勿在生產環境的 LUIS 應用程式中使用少數範例語句。


## <a name="install-required-software"></a>安裝必要軟體
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。 如果您想要使用 .NET Core CLI，則可下載的範例存放庫中會有指示。
- [Microsoft Bot Framework 模擬器](https://aka.ms/botframeworkemulator)是一款桌面應用程式，可讓聊天機器人的開發人員在本機或透過通道從遠端對其聊天機器人進行測試和偵錯。

## <a name="download-the-sample-code-of-the-chat-bot"></a>下載聊天機器人的範例程式碼

聊天機器人可在個人化工具範例存放庫中取得。 複製或[下載](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/archive/master.zip)存放庫，然後使用 Visual Studio 2019 開啟 `/samples/ChatbotExample` 目錄中的範例。

若要複製存放庫，請在 Bash 殼層 (終端機) 中使用下列 Git 命令。

```bash
git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
```

## <a name="create-and-configure-personalizer-and-luis-resources"></a>建立和設定個人化工具與 LUIS 資源

### <a name="create-azure-resources"></a>建立 Azure 資源

若要使用此聊天機器人，您必須建立適用於個人化工具和 Language Understanding (LUIS) 的 Azure 資源。

* [建立 LUIS 資源](../luis/luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal)。 在建立步驟中選取**兩者**，因為您同時需要撰寫和預測資源。
* [建立個人化工具資源](how-to-create-resource.md)，然後從 Azure 入口網站複製金鑰和端點。 您將需要在 .NET 專案的 `appsettings.json` 檔案中設定這些值。

### <a name="create-luis-app"></a>建立 LUIS 應用程式

如果您首次使用 LUIS，則需要[登入](https://www.luis.ai)並立即遷移帳戶。 您不需要建立新的資源，而是要選取您在本教學課程的上一節中建立的資源。

1. 若要建立新的 LUIS 應用程式，請在 [LUIS 入口網站](https://www.luis.ai)中，選取您的訂用帳戶和撰寫資源。
1. 然後，仍在相同的頁面上，選取 [+ 新增對話應用程式]，然後選取 [匯入為 JSON]。
1. 在快顯對話方塊中，選取 [選擇檔案]，然後選取 `/samples/ChatbotExample/CognitiveModels/coffeebot.json` 檔案。 輸入名稱 `Personalizer Coffee bot`。
1. 在 LUIS 入口網站的右上方導覽中，選取 [定型] 按鈕。
1. 選取 [發佈] 按鈕，將應用程式發佈至預測執行階段的 [生產位置]。
1. 選取 [管理]，然後選取 [設定]。 複製 [應用程式識別碼] 的值。 您將需要在 .NET 專案的 `appsettings.json` 檔案中設定此值。
1. 仍在 [管理] 區段中，選取 [Azure 資源]。 這會顯示應用程式上的相關聯資源。
1. 選取 [新增預測資源]。 在快顯對話方塊中，選取您的訂用帳戶，以及您在本教學課程的上一節建立的預測資源，然後選取 [完成]。
1. 複製 [主要金鑰] 和 [端點 URL] 的值。 您將需要在 .NET 專案的 `appsettings.json` 檔案中設定這些值。

### <a name="configure-bot-with-appsettingsjson-file"></a>使用 appsettings.json 檔案來設定聊天機器人

1. 使用 Visual Studio 2019 開啟聊天機器人的解決方案檔案 `ChatbotSamples.sln`。
1. 開啟位於專案根目錄中的 `appsettings.json`。
1. 設定在本教學課程上一節複製的五個設定。

    ```json
    {
      "PersonalizerChatbot": {
        "LuisAppId": "",
        "LuisAPIKey": "",
        "LuisServiceEndpoint": "",
        "PersonalizerServiceEndpoint": "",
        "PersonalizerAPIKey": ""
      }
    }
    ```

### <a name="build-and-run-the-bot"></a>建置和執行聊天機器人

設定 `appsettings.json` 之後，您就可以開始建置和執行聊天機器人。 當您這麼做時，瀏覽器會開啟至執行中的網站 `http://localhost:3978`。

:::image type="content" source="media/tutorial-chat-bot/chat-bot-web-site.png" alt-text="顯示聊天機器人網站的瀏覽器螢幕擷取畫面。":::

本教學課程會說明聊天機器人在做什麼，因此請讓網站保持執行，以便您可以與聊天機器人互動。


## <a name="set-up-the-bot-emulator"></a>設定聊天機器人模擬器

1. 開啟聊天機器人模擬器，然後選取 [開啟聊天機器人]。

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-startup.png" alt-text="聊天機器人模擬器啟動畫面的螢幕擷取畫面。":::


1. 使用下列**聊天機器人 URL** 來設定聊天機器人，然後選取 [連線]：

    `http://localhost:3978/api/messages`

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-open-bot-settings.png" alt-text="聊天機器人模擬器開啟聊天機器人設定的螢幕擷取畫面。":::

    模擬器會連線至聊天機器人，並顯示說明文字以及有助於進行本機開發的記錄和偵錯資訊。

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-bot-conversation-first-turn.png" alt-text="第一回對話中聊天機器人模擬器的螢幕擷取畫面。":::

## <a name="use-the-bot-in-the-bot-emulator"></a>在聊天機器人模擬器中使用聊天機器人

1. 輸入 `I would like to see the menu` 來要求查看菜單。 聊天機器人會顯示品項。
1. 輸入 `Please suggest a drink for me.` 讓聊天機器人建議品項。模擬器會在聊天視窗中顯示排名的要求和回應，因此您可以看到完整的 JSON。 聊天機器人會提出建議，例如 `How about Latte?`
1. 回答您接受其建議即表示您接受個人化工具的最高排名選項 `I like it.`。模擬器會在聊天視窗中顯示獎勵分數為 1 的獎勵要求和回應，因此您可以看到完整的 JSON。 而聊天機器人會回以 `That’s great! I’ll keep learning your preferences over time.` 和 `Would you like to get a new suggestion or reset the simulated context to a new day?`

    如果您針對選項的回應是 `no`，則會將獎勵分數 0 傳送至個人化工具。


## <a name="understand-the-net-code-using-personalizer"></a>了解使用個人化工具的 .NET 程式碼

.NET 解決方案是簡單的 Bot Framework 聊天機器人。 與個人化工具相關的程式碼位於下列資料夾中：
* `/samples/ChatbotExample/Bots`
    * `PersonalizerChatbot.cs` 檔案用於聊天機器人與個人化工具之間的互動
* `/samples/ChatbotExample/ReinforcementLearning` - 管理個人化工具模型的動作和特性
* `/samples/ChatbotExample/Model` - 這些檔案用於個人化工具的動作和特性以及 LUIS 意圖

### <a name="personalizerchatbotcs---working-with-personalizer"></a>PersonalizerChatbot.cs - 與個人化工具搭配運作

`PersonalizerChatbot` 類別衍生自 `Microsoft.Bot.Builder.ActivityHandler`。 其有三個屬性和方法可管理對話流程。

> [!CAUTION]
> 請勿複製本教學課程中的程式碼。 請使用[個人化工具範例存放庫](https://github.com/Azure-Samples/cognitive-services-personalizer-samples)中的範例程式碼。

```csharp
public class PersonalizerChatbot : ActivityHandler
{

    private readonly LuisRecognizer _luisRecognizer;
    private readonly PersonalizerClient _personalizerClient;

    private readonly RLContextManager _rlFeaturesManager;

    public PersonalizerChatbot(LuisRecognizer luisRecognizer, RLContextManager rlContextManager, PersonalizerClient personalizerClient)
            {
                _luisRecognizer = luisRecognizer;
                _rlFeaturesManager = rlContextManager;
                _personalizerClient = personalizerClient;
            }
    }

    public override async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
    {
        await base.OnTurnAsync(turnContext, cancellationToken);

        if (turnContext.Activity.Type == ActivityTypes.Message)
        {
            // Check LUIS model
            var recognizerResult = await _luisRecognizer.RecognizeAsync(turnContext, cancellationToken);
            var topIntent = recognizerResult?.GetTopScoringIntent();
            if (topIntent != null && topIntent.HasValue && topIntent.Value.intent != "None")
            {
                Intents intent = (Intents)Enum.Parse(typeof(Intents), topIntent.Value.intent);
                switch (intent)
                {
                    case Intents.ShowMenu:
                        await turnContext.SendActivityAsync($"Here is our menu: \n Coffee: {CoffeesMethods.DisplayCoffees()}\n Tea: {TeaMethods.DisplayTeas()}", cancellationToken: cancellationToken);
                        break;
                    case Intents.ChooseRank:
                        // Here we generate the event ID for this Rank.
                        var response = await ChooseRankAsync(turnContext, _rlFeaturesManager.GenerateEventId(), cancellationToken);
                        _rlFeaturesManager.CurrentPreference = response.Ranking;
                        await turnContext.SendActivityAsync($"How about {response.RewardActionId}?", cancellationToken: cancellationToken);
                        break;
                    case Intents.RewardLike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 1, cancellationToken);
                            await turnContext.SendActivityAsync($"That's great! I'll keep learning your preferences over time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you like. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.RewardDislike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 0, cancellationToken);
                            await turnContext.SendActivityAsync($"Oh well, maybe I'll guess better next time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you dislike. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.Reset:
                        _rlFeaturesManager.GenerateRLFeatures();
                        await SendResetMessageAsync(turnContext, cancellationToken);
                        break;
                    default:
                        break;
                }
            }
            else
            {
                var msg = @"Could not match your message with any of the following LUIS intents:
                        'ShowMenu'
                        'ChooseRank'
                        'RewardLike'
                        'RewardDislike'.
                        Try typing 'Show me the menu','What do you suggest','I like it','I don't like it'.";
                await turnContext.SendActivityAsync(msg);
            }
        }
        else if (turnContext.Activity.Type == ActivityTypes.ConversationUpdate)
        {
            // Generate a new weekday and weather condition
            // These will act as the context features when we call rank with Personalizer
            _rlFeaturesManager.GenerateRLFeatures();

            // Send a welcome message to the user and tell them what actions they may perform to use this bot
            await SendWelcomeMessageAsync(turnContext, cancellationToken);
        }
        else
        {
            await turnContext.SendActivityAsync($"{turnContext.Activity.Type} event detected", cancellationToken: cancellationToken);
        }
    }

    // code removed for brevity, full sample code available for download
    private async Task SendWelcomeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendResetMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendByebyeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
    private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
}
```

前面加上 `Send` 的方法會管理與聊天機器人和 LUIS 的對話。 `ChooseRankAsync` 和 `RewardAsync` 方法會與個人化工具互動。

#### <a name="calling-rank-api-and-display-results"></a>呼叫排名 API 並顯示結果

`ChooseRankAsync` 方法會收集具有特性的動作和內容特性，來建置要傳送給個人化工具排名 API 的 JSON 資料。

```csharp
private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
{
    IList<object> contextFeature = new List<object>
    {
        new { weather = _rlFeaturesManager.RLFeatures.Weather.ToString() },
        new { dayofweek = _rlFeaturesManager.RLFeatures.DayOfWeek.ToString() },
    };

    Random rand = new Random(DateTime.UtcNow.Millisecond);
    IList<RankableAction> actions = new List<RankableAction>();
    var coffees = Enum.GetValues(typeof(Coffees));
    var beansOrigin = Enum.GetValues(typeof(CoffeeBeansOrigin));
    var organic = Enum.GetValues(typeof(Organic));
    var roast = Enum.GetValues(typeof(CoffeeRoast));
    var teas = Enum.GetValues(typeof(Teas));

    foreach (var coffee in coffees)
    {
        actions.Add(new RankableAction
        {
            Id = coffee.ToString(),
            Features =
            new List<object>()
            {
                new { BeansOrigin = beansOrigin.GetValue(rand.Next(0, beansOrigin.Length)).ToString() },
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
                new { Roast = roast.GetValue(rand.Next(0, roast.Length)).ToString() },
            },
        });
    }

    foreach (var tea in teas)
    {
        actions.Add(new RankableAction
        {
            Id = tea.ToString(),
            Features =
            new List<object>()
            {
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
            },
        });
    }

    // Sending a rank request to Personalizer
    // Here we are asking Personalizer to decide which drink the user is most likely to want
    // based on the current context features (weather, day of the week generated in RLContextManager)
    // and the features of the drinks themselves
    var request = new RankRequest(actions, contextFeature, null, eventId);
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL TO RANK =====\n" +
        "This is what is getting sent to Rank:\n" +
        $"{JsonConvert.SerializeObject(request, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    var response = await _personalizerClient.RankAsync(request, cancellationToken);
    await turnContext.SendActivityAsync(
        $"===== DEBUG MESSAGE RETURN FROM RANK =====\n" +
        "This is what Rank returned:\n" +
        $"{JsonConvert.SerializeObject(response, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    return response;
}
```

#### <a name="calling-reward-api-and-display-results"></a>呼叫獎勵 API 並顯示結果

`RewardAsync` 方法會判斷分數來建置要傳送給個人化工具獎勵 API 的 JSON 資料。 分數會從使用者文字中所識別出的 LUIS 意圖來決定，並從 `OnTurnAsync` 方法傳送出來。

```csharp
private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
{
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL REWARD =====\n" +
        "Calling Reward:\n" +
        $"eventId = {eventId}, reward = {reward}\n",
        cancellationToken: cancellationToken);

    // Sending a reward request to Personalizer
    // Here we are responding to the drink ranking Personalizer provided us
    // If the user liked the highest ranked drink, we give a high reward (1)
    // If they did not, we give a low reward (0)
    await _personalizerClient.RewardAsync(eventId, new RewardRequest(reward), cancellationToken);
}
```

## <a name="design-considerations-for-a-bot"></a>聊天機器人的設計考量

此範例旨在示範聊天機器人中個人化工具的簡單端對端解決方案。 您的使用案例可能會更加複雜。

如果您打算在生產環境的聊天機器人中使用個人化工具，請做以下規劃：
* _每次_需要經過排名的選項時，就即時存取個人化工具。 排名 API 無法批次處理或快取。  獎勵呼叫可延遲或卸載至不同程序，而且如果您未在一段時間內傳回獎勵，則會為該事件設定預設獎勵值。
* 以使用案例為基礎來計算獎勵：此範例顯示了兩個值為零的獎勵，一個沒有範圍區間，且分數沒有負數值。 您的系統需要更細微的評分。
* 聊天機器人通道：這個範例會使用單一通道，但如果您想要使用多個通道，或在單一通道上使用聊天機器人的變種，則可能需要將其視為個人化工具模型內容特性的一部分。

## <a name="clean-up-resources"></a>清除資源

當您完成本教學課程時，請清除下列資源：

* 刪除您的範例專案目錄。
* 在 Azure 入口網站中刪除您的個人化工具和 LUIS 資源。

## <a name="next-steps"></a>後續步驟
* [個人化工具的運作方式](how-personalizer-works.md)
* [特性](concepts-features.md)：了解動作和內容所使用的特性相關概念
* [獎勵](concept-rewards.md)：了解如何計算獎勵
