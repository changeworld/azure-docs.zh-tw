---
title: 使用 web 應用程式-個人化工具
description: '使用個人化工具迴圈自訂 c # .NET web 應用程式，以根據動作（含功能）和內容功能提供正確的內容給使用者。'
ms.topic: troubleshooting
ms.date: 06/10/2020
ms.author: diberry
ms.openlocfilehash: 9def69a1540e81b99723c16ad34ba522d1737c7f
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2020
ms.locfileid: "84713967"
---
# <a name="add-personalizer-to-a-net-web-app"></a>將個人化工具新增至 .NET web 應用程式

使用個人化工具迴圈自訂 c # .NET web 應用程式，以根據動作（含功能）和內容功能提供正確的內容給使用者。

**在本教學課程中，您將了解如何：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 設定個人化工具金鑰和端點
> * 收集功能
> * 呼叫排名和獎勵 Api
> * 顯示最上方的動作，指定為_rewardActionId_



## <a name="select-the-best-content-for-a-web-app"></a>選取 web 應用程式的最佳內容

當網頁上有一份_動作_（某些類型的內容）需要個人化以顯示的單一頂端專案（rewardActionId）時，web 應用程式應該使用個人化工具。 動作清單的範例包括新聞文章、按鈕位置位置，以及產品名稱的文字選項。

您會將動作清單連同內容功能傳送至個人化工具迴圈。 個人化工具會選取單一最佳動作，然後您的 web 應用程式會顯示該動作。

在本教學課程中，動作是食物的類型：

* pasta
* 霜淇淋
* juice
* 沙拉
* 爆米花
* 廳
* 湯

若要協助個人化工具瞭解您的動作，請使用每個排名 API 要求的功能和_內容功能_來傳送 __動作_。

模型的**功能**是可在 web 應用程式使用者群成員之間匯總（群組）之動作或內容的相關資訊。 功能_不_會個別指定（例如使用者識別碼）或高度特定（例如一天的確切時間）。

### <a name="actions-with-features"></a>具有功能的動作

每個動作（內容專案）都有可協助區分食物專案的功能。

在 Azure 入口網站中，不會將功能設定為迴圈設定的一部分。 相反地，它們會以 JSON 物件的形式，使用每個排名 API 呼叫來傳送。 這可讓動作及其功能的彈性在一段時間內成長、變更和縮減，讓個人化工具能夠遵循趨勢。

```csharp
 /// <summary>
  /// Creates personalizer actions feature list.
  /// </summary>
  /// <returns>List of actions for personalizer.</returns>
  private IList<RankableAction> GetActions()
  {
      IList<RankableAction> actions = new List<RankableAction>
      {
          new RankableAction
          {
              Id = "pasta",
              Features =
              new List<object>() { new { taste = "savory", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
          },

          new RankableAction
          {
              Id = "ice cream",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionalLevel = 2 } }
          },

          new RankableAction
          {
              Id = "juice",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionLevel = 5 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "salad",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "low" }, new { nutritionLevel = 8 } }
          },

          new RankableAction
          {
              Id = "popcorn",
              Features =
              new List<object>() { new { taste = "salty", spiceLevel = "none" }, new { nutritionLevel = 3 } }
          },

          new RankableAction
          {
              Id = "coffee",
              Features =
              new List<object>() { new { taste = "bitter", spiceLevel = "none" }, new { nutritionLevel = 3 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "soup",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "high" }, new { nutritionLevel =  7} }
          }
      };

      return actions;
  }
```


## <a name="context-features"></a>關係特性

內容功能有助於個人化工具瞭解動作的內容。 這個範例應用程式的內容包括：

* 當日時間-早上、下午、晚上、晚上
* 使用者對 salty、bitter、來源或 savory 等的喜好設定
* 瀏覽器的內容-使用者代理程式、地理位置、訪客

```csharp
/// <summary>
/// Get users time of the day context.
/// </summary>
/// <returns>Time of day feature selected by the user.</returns>
private string GetUsersTimeOfDay()
{
    Random rnd = new Random();
    string[] timeOfDayFeatures = new string[] { "morning", "noon", "afternoon", "evening", "night", "midnight" };
    int timeIndex = rnd.Next(timeOfDayFeatures.Length);
    return timeOfDayFeatures[timeIndex];
}

/// <summary>
/// Gets user food preference.
/// </summary>
/// <returns>Food taste feature selected by the user.</returns>
private string GetUsersTastePreference()
{
    Random rnd = new Random();
    string[] tasteFeatures = new string[] { "salty", "bitter", "sour", "savory", "sweet" };
    int tasteIndex = rnd.Next(tasteFeatures.Length);
    return tasteFeatures[tasteIndex];
}
```

## <a name="how-does-the-web-app-use-personalizer"></a>Web 應用程式如何使用個人化工具？

Web 應用程式會使用個人化工具，從食物挑選清單中選取最佳的動作。 其運作方式是使用每個排名 API 呼叫來傳送下列資訊：
* 具有其功能的**動作** `taste` ，例如和`spiceLevel`
* **內容**功能，例如 `time` 日、使用者的 `taste` 喜好設定，以及瀏覽器的使用者代理程式資訊，以及內容功能
* **要排除的動作，** 例如 juice
* **eventid**，對排名 API 的每個呼叫都不同。

## <a name="personalizer-model-features-in-a-web-app"></a>Web 應用程式中的個人化工具模型功能

個人化工具需要動作（內容）和目前內容（使用者和環境）的功能。 功能可用來將動作對齊模型中的目前內容。 模型是個人化工具過去關於動作、內容及其功能的知識，可讓 it 做出明智的決策。

模型（包括功能）會根據您在 Azure 入口網站中的 [**模型更新頻率**] 設定，依排程更新。

> [!CAUTION]
> 此應用程式中的功能旨在說明功能和功能的值，但不一定要在 web 應用程式中使用最佳功能。

### <a name="plan-for-features-and-their-values"></a>規劃功能及其值

選取功能時，應使用與您在技術架構中的任何架構或模型相同的規劃與設計。 功能值可以使用商務邏輯或協力廠商系統來設定。 功能值應該不會因為不會在一組或多項功能的類別中套用，因此不會有太大的限制。

### <a name="generalize-feature-values"></a>一般化功能值

#### <a name="generalize-into-categories"></a>一般化為類別

此應用程式會使用 `time` 做為功能，但會將時間分組為類別 `morning` ，例如、 `afternoon` 、 `evening` 和 `night` 。 這是使用時間資訊，但不以高度特定方式（例如）的範例 `10:05:01 UTC+2` 。

#### <a name="generalize-into-parts"></a>將元件一般化

此應用程式會使用瀏覽器中的 HTTP 要求功能。 這會從非常特定的字串開始，其中包含所有資料，例如：

```http
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/530.99 (KHTML, like Gecko) Chrome/80.0.3900.140 Safari/537.36
```

**HttpRequestFeatures**類別庫會將此字串一般化至具有個別值的**userAgentInfo**物件。 任何太特定的值都會設定為空字串。 傳送要求的內容功能時，其具有下列 JSON 格式：

```JSON
{
  "httpRequestFeatures": {
    "_synthetic": false,
    "OUserAgent": {
      "_ua": "",
      "_DeviceBrand": "",
      "_DeviceFamily": "Other",
      "_DeviceIsSpider": false,
      "_DeviceModel": "",
      "_OSFamily": "Windows",
      "_OSMajor": "10",
      "DeviceType": "Desktop"
    }
  }
}
```


## <a name="using-sample-web-app"></a>使用範例 web 應用程式

以瀏覽器為基礎的 web 應用程式範例（提供所有程式碼）需要安裝下列應用程式才能執行應用程式。

安裝下列軟體：

* [.Net core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1) -範例後端伺服器使用 .net core
* [Node.js](https://nodejs.org/) -用戶端/前端相依于此應用程式
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)或[.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/) -使用 Visual Studio 2019 的開發人員環境或 .NET Core CLI 來建立及執行應用程式

### <a name="set-up-the-sample"></a>設定範例
1. 複製 Azure 個人化工具範例存放庫。

    ```bash
    git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
    ```

1. 流覽至_samples/HttpRequestFeatures_以開啟方案 `HttpRequestFeaturesExample.sln` 。

    如有要求，允許 Visual Studio 更新個人化工具的 .NET 封裝。

### <a name="set-up-azure-personalizer-service"></a>設定 Azure 個人化工具 Service

1. 在 Azure 入口網站中[建立個人化工具資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)。

1. 在 [Azure 入口網站中，于 `Endpoint` `Key1` `Key2` [**金鑰和端點**] 索引標籤中尋找和（或）（可正常執行）。這些是您的 `PersonalizerServiceEndpoint` 和您的 `PersonalizerApiKey` 。
1. 在 `PersonalizerServiceEndpoint` **appsettings.js**中填入。
1. `PersonalizerApiKey`以下列其中一種方式將設定為[應用程式密碼](https://docs.microsoft.com/aspnet/core/security/app-secrets)：

    * 如果您使用 .NET Core CLI，則可以使用 `dotnet user-secrets set "PersonalizerApiKey" "<API Key>"` 命令。
    * 如果您使用 Visual Studio，您可以在專案上按一下滑鼠右鍵，然後選取 [**管理使用者密碼**] 功能表選項來設定個人化工具金鑰。 如此一來，Visual Studio 將會開啟檔案， `secrets.json` 您可以在其中新增金鑰，如下所示：

    ```JSON
    {
      "PersonalizerApiKey": "<your personalizer key here>",
    }
    ```

## <a name="run-the-sample"></a>執行範例

使用下列其中一種方法來建立並執行 HttpRequestFeaturesExample：

* Visual Studio 2019：按**F5**
* .NET Core CLI： `dotnet build` then`dotnet run`

透過網頁瀏覽器，您可以傳送排名要求和報酬要求，並查看其回應，以及從您的環境中解壓縮的 HTTP 要求功能。

> [!div class="mx-imgBorder"]
> ![建立並執行 HTTPRequestFeaturesExample 專案。 瀏覽器視窗隨即開啟，以顯示單一頁面應用程式。](./media/tutorial-web-app/web-app-single-page.png)

## <a name="demonstrate-the-personalizer-loop"></a>示範個人化工具迴圈

1. 選取 [**產生新的排名要求**] 按鈕，為「排名 API」呼叫建立新的 JSON 物件。 這會建立動作（含功能）和內容功能，並顯示值，讓您可以查看 JSON 的樣子。

    對於您自己的未來應用程式，可能會在用戶端、伺服器上、兩者的混合，或呼叫其他服務時，產生動作和功能。

1. 選取 [**傳送排名要求**]，將 JSON 物件傳送至伺服器。 伺服器會呼叫個人化工具排名 API。 伺服器會接收回應，並將排名最高的動作傳回給用戶端以顯示。

1. 設定 [獎勵] 值，然後選取 [**傳送獎勵要求**] 按鈕。 如果您未變更獎勵值，用戶端應用程式一律會將的值傳送 `1` 至個人化工具。

    > [!div class="mx-imgBorder"]
    > ![建立並執行 HTTPRequestFeaturesExample 專案。 瀏覽器視窗隨即開啟，以顯示單一頁面應用程式。](./media/tutorial-web-app/reward-score-api-call.png)

    在您自己的未來應用程式中，從使用者的用戶端行為收集資訊，以及伺服器上的商務邏輯，可能會產生報酬分數。

## <a name="understand-the-sample-web-app"></a>瞭解範例 web 應用程式

範例 web 應用程式具有**c # .net** server，可管理功能的集合，以及傳送和接收個人化工具端點的 HTTP 呼叫。

範例 web 應用程式會使用**挖定的前端用戶端應用程式**來捕捉功能，並處理使用者介面動作，例如按一下按鈕，以及將資料傳送到 .net 伺服器。

下列各節說明開發人員在使用個人化工具時所需瞭解的伺服器和用戶端部分。

## <a name="rank-api-client-application-sends-context-to-server"></a>排名 API：用戶端應用程式將內容傳送至伺服器

用戶端應用程式會收集使用者的瀏覽器_使用者代理_程式。

> [!div class="mx-imgBorder"]
> ![建立並執行 HTTPRequestFeaturesExample 專案。 瀏覽器視窗隨即開啟，以顯示單一頁面應用程式。](./media/tutorial-web-app/user-agent.png)

## <a name="rank-api-server-application-calls-personalizer"></a>排名 API：伺服器應用程式呼叫個人化工具

這是使用用戶端應用程式的典型 .NET web 應用程式，大部分的定案盤子程式碼都是為您提供。 不是個人化工具特定的任何程式碼都會從下列程式碼片段中移除，因此您可以將焦點放在個人化工具特定的程式碼上。

### <a name="create-personalizer-client"></a>建立個人化工具用戶端

在伺服器的**Startup.cs**中，個人化工具端點和金鑰是用來建立個人化工具用戶端。 用戶端應用程式不需要與此應用程式中的個人化工具通訊，而是依賴伺服器來進行這些 SDK 呼叫。

網頁伺服器的 .NET 啟動程式碼為：

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
// ... other using statements removed for brevity

namespace HttpRequestFeaturesExample
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            string personalizerApiKey = Configuration.GetSection("PersonalizerApiKey").Value;
            string personalizerEndpoint = Configuration.GetSection("PersonalizerConfiguration:ServiceEndpoint").Value;
            if (string.IsNullOrEmpty(personalizerEndpoint) || string.IsNullOrEmpty(personalizerApiKey))
            {
                throw new ArgumentException("Missing Azure Personalizer endpoint and/or api key.");
            }
            services.AddSingleton(client =>
            {
                return new PersonalizerClient(new ApiKeyServiceClientCredentials(personalizerApiKey))
                {
                    Endpoint = personalizerEndpoint
                };
            });

            services.AddMvc();
        }

        // ... code removed for brevity
    }
}
```

### <a name="select-best-action"></a>選取最佳動作

在伺服器的**PersonalizerController.cs**中， **GenerateRank**伺服器 API 會摘要說明呼叫排名 API 的準備工作

* `eventId`為 Rank 呼叫建立新的
* 取得動作清單
* 從使用者取得功能的清單，並建立內容功能
* （選擇性）設定任何排除的動作
* 呼叫排名 API，將結果傳回到用戶端

```csharp
/// <summary>
/// Creates a RankRequest with user time of day, HTTP request features,
/// and taste as the context and several different foods as the actions
/// </summary>
/// <returns>RankRequest with user info</returns>
[HttpGet("GenerateRank")]
public RankRequest GenerateRank()
{
    string eventId = Guid.NewGuid().ToString();

    // Get the actions list to choose from personalizer with their features.
    IList<RankableAction> actions = GetActions();

    // Get context information from the user.
    HttpRequestFeatures httpRequestFeatures = GetHttpRequestFeaturesFromRequest(Request);
    string timeOfDayFeature = GetUsersTimeOfDay();
    string tasteFeature = GetUsersTastePreference();

    // Create current context from user specified data.
    IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature },
            new { httpRequestFeatures }
    };

    // Exclude an action for personalizer ranking. This action will be held at its current position.
    IList<string> excludeActions = new List<string> { "juice" };

    // Rank the actions
    return new RankRequest(actions, currentContext, excludeActions, eventId);
}
```

傳送至個人化工具的 JSON，其中包含動作（含功能）和目前的內容功能，如下所示：

```json
{
    "contextFeatures": [
        {
            "time": "morning"
        },
        {
            "taste": "savory"
        },
        {
            "httpRequestFeatures": {
                "_synthetic": false,
                "MRefer": {
                    "referer": "http://localhost:51840/"
                },
                "OUserAgent": {
                    "_ua": "",
                    "_DeviceBrand": "",
                    "_DeviceFamily": "Other",
                    "_DeviceIsSpider": false,
                    "_DeviceModel": "",
                    "_OSFamily": "Windows",
                    "_OSMajor": "10",
                    "DeviceType": "Desktop"
                }
            }
        }
    ],
    "actions": [
        {
            "id": "pasta",
            "features": [
                {
                    "taste": "savory",
                    "spiceLevel": "medium"
                },
                {
                    "nutritionLevel": 5,
                    "cuisine": "italian"
                }
            ]
        },
        {
            "id": "ice cream",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionalLevel": 2
                }
            ]
        },
        {
            "id": "juice",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 5
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "salad",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "low"
                },
                {
                    "nutritionLevel": 8
                }
            ]
        },
        {
            "id": "popcorn",
            "features": [
                {
                    "taste": "salty",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                }
            ]
        },
        {
            "id": "coffee",
            "features": [
                {
                    "taste": "bitter",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "soup",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "high"
                },
                {
                    "nutritionLevel": 7
                }
            ]
        }
    ],
    "excludedActions": [
        "juice"
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "deferActivation": null
}
```

### <a name="return-personalizer-rewardactionid-to-client"></a>將個人化工具 rewardActionId 回用戶端

次序 API 會將選取的最佳動作**rewardActionId**傳回伺服器。

顯示**rewardActionId**中傳回的動作。

```json
{
    "ranking": [
        {
            "id": "popcorn",
            "probability": 0.833333254
        },
        {
            "id": "salad",
            "probability": 0.03333333
        },
        {
            "id": "juice",
            "probability": 0
        },
        {
            "id": "soup",
            "probability": 0.03333333
        },
        {
            "id": "coffee",
            "probability": 0.03333333
        },
        {
            "id": "pasta",
            "probability": 0.03333333
        },
        {
            "id": "ice cream",
            "probability": 0.03333333
        }
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "rewardActionId": "popcorn"
}
```

### <a name="client-displays-the-rewardactionid-action"></a>用戶端顯示 rewardActionId 動作

在本教學課程中， `rewardActionId` 會顯示值。

在您自己的未來應用程式中，可能會有部分精確的文字、按鈕或網頁的區段反白顯示。 此清單會針對分數的任何後續分析傳回，而不是內容的順序。 只 `rewardActionId` 應顯示內容。

## <a name="reward-api-collect-information-for-reward"></a>獎勵 API：收集報酬的資訊

應謹慎規劃報酬[分數](concept-rewards.md)，就像規劃功能一樣。 報酬分數通常應該是介於0到1之間的值。 根據商務邏輯和目標，此值_可以_部分在用戶端應用程式中，根據使用者行為和部分在伺服器上計算。

如果伺服器未在個人化工具資源的 Azure 入口網站中設定的報酬**等待時間**內呼叫獎勵 API，則該事件會使用**預設**報酬（也在 Azure 入口網站中設定）。

在此範例應用程式中，您可以選取一個值，以查看報酬如何影響選取專案。

## <a name="additional-ways-to-learn-from-this-sample"></a>從這個範例學習的其他方法

此範例會使用個人化工具資源的 Azure 入口網站中設定的數個以時間為基礎的事件。 使用這些值來播放，然後返回此範例 web 應用程式，以查看變更對排名和報酬呼叫的影響：

* 報酬等候時間
* 模型更新頻率

要用來播放的其他設定包括：
* 預設報酬
* 探索百分比


## <a name="clean-up-resources"></a>清除資源

當您完成本教學課程時，請清除下列資源：

* 刪除您的範例專案目錄。
* 刪除您的個人化工具資源-將個人化工具資源視為動作的專用，如果您仍使用「食物」做為動作主體網域，則僅限內容重複使用該資源。


## <a name="next-steps"></a>後續步驟
* [個人化工具的運作方式](how-personalizer-works.md)
* [功能](concepts-features.md)：使用 with 動作和內容來瞭解功能的概念
* [獎勵](concept-rewards.md)：瞭解如何計算報酬