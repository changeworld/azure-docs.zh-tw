---
title: 使用 Web 應用程式 - 個人化工具
description: 使用個人化工具迴圈自訂 C# .Net Web 應用程式，以根據動作 (具有特性) 和內容特性來提供正確內容給使用者。
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 06/10/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: c004887e3883ae711974b544510dff16a98d4ef9
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363913"
---
# <a name="tutorial-add-personalizer-to-a-net-web-app"></a>教學課程：將個人化工具新增至 .NET Web 應用程式

使用個人化工具迴圈自訂 C# .Net Web 應用程式，以根據動作 (具有特性) 和內容特性來提供正確內容給使用者。

**在本教學課程中，您將了解如何：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 設定個人化工具的金鑰和端點
> * 收集特性
> * 呼叫排名 API 和獎勵 API
> * 顯示最佳動作 (指定為 _rewardActionId_ )



## <a name="select-the-best-content-for-a-web-app"></a>選取 Web 應用程式的最佳內容

當網頁上有一份 _動作_ (某些類型的內容) 清單，而這些動作必須個人化為要顯示的單一最佳項目 (rewardActionId) 時，Web 應用程式就應該使用個人化工具。 動作清單的範例包括新聞文章、按鈕放置位置，以及產品名稱的文字選擇。

您需要將動作清單連同內容特性傳送至個人化工具迴圈。 個人化工具會選取單一最佳動作，然後 Web 應用程式會顯示該動作。

在本教學課程中，動作是食物的類型：

* 義大利麵
* 冰淇淋
* 果汁
* 沙拉
* 爆米花
* 咖啡
* 湯

為了協助個人化工具了解您的動作，請在每個排名 API 要求中傳送 _具有特性的動作_ 與 _內容特性_ 。

模型的 **特性** 是可跨 Web 應用程式使用者群體的成員加以彙總 (群組) 的動作或內容相關資訊。 特性 _不會_ 具有個別特定性質 (例如使用者識別碼) 或高度特定性 (例如一天之中的確切時間)。

### <a name="actions-with-features"></a>具有特性的動作

每個動作 (內容項目) 都有可協助區分食物項目的特性。

Azure 入口網站不會將特性設定為迴圈設定的一部分。 相反地，會於每個排名 API 呼叫中以 JSON 物件的形式來傳送這些特性。 這讓動作及其特性能夠彈性地在一段時間內增加、變更和縮減，而讓個人化工具能夠追隨趨勢。

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


## <a name="context-features"></a>內容特性

內容特性可協助個人化工具了解動作的內容。 這個應用程式範例的內容包括：

* 一天之中的時間 - 早上、下午、傍晚、夜間
* 使用者對味道的偏好 - 鹹味、甜味、苦味、酸味或香草味
* 瀏覽器的內容 - 使用者代理程式、地理位置、訪客來源

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

Web 應用程式會使用個人化工具，從食物選擇清單中選取最佳動作。 其方式是在每個排名 API 呼叫中傳送下列資訊：
* **動作** 及其特性，例如 `taste` 和 `spiceLevel`
* **內容** 特性，例如一天之中的 `time`、使用者的 `taste` 偏好、瀏覽器的使用者代理程式資訊，以及內容特性
* **要排除的動作** ，例如果汁
* **eventId** ，此值在每個排名 API 呼叫中都不同。

## <a name="personalizer-model-features-in-a-web-app"></a>Web 應用程式中的個人化工具模型特性

個人化工具需要動作 (內容) 和目前內容 (使用者和環境) 的特性。 特性可用來讓動作符合模型中的目前內容。 模型代表個人化工具過去關於動作、內容及其特性的知識，可讓其做出明智決策。

模型 (包括特性) 會根據您在 Azure 入口網站中的 [模型更新頻率] 設定，依排程進行更新。

> [!CAUTION]
> 此應用程式中的特性旨在說明特性和特性值，但不一定會說明要在 Web 應用程式中使用的最佳特性。

### <a name="plan-for-features-and-their-values"></a>規劃特性及其值

在選取特性時，應使用與您要套用到技術架構中所含任何結構描述或模型相同的規劃與設計。 特性值可以使用商務邏輯或第三方系統來加以設定。 特性值不應該具有高度特定性，以免不能套用到特性的群組或類別中。

### <a name="generalize-feature-values"></a>將特性值一般化

#### <a name="generalize-into-categories"></a>一般化為類別

此應用程式會使用 `time` 作為特性，但會將 time 分組到各個類別，例如 `morning`、`afternoon`、`evening` 和 `night`。 這是使用時間資訊，但不具有高度特定性的範例，例如 `10:05:01 UTC+2`。

#### <a name="generalize-into-parts"></a>將組件一般化

此應用程式會使用瀏覽器中的 HTTP 要求特性。 這會從具有所有資料、非常特定的字串來開始，例如：

```http
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/530.99 (KHTML, like Gecko) Chrome/80.0.3900.140 Safari/537.36
```

**HttpRequestFeatures** 類別庫會將此字串一般化為具有個別值的 **userAgentInfo** 物件。 任何太特定的值都會設定為空字串。 要求的內容特性在傳送時會有下列 JSON 格式：

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


## <a name="using-sample-web-app"></a>使用範例 Web 應用程式

瀏覽器型的範例 Web 應用程式 (提供所有程式碼) 需要安裝下列應用程式才能執行 app。

請安裝下列軟體：

* [.NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1) - 範例後端伺服器會使用 .NET Core
* [Node.js](https://nodejs.org/) - 用戶端/前端取決於此應用程式
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) 或 [.NET Core CLI](/dotnet/core/tools/) - 請使用 Visual Studio 2019 的開發人員環境或 .NET Core CLI 來建置和執行 app

### <a name="set-up-the-sample"></a>設定範例
1. 複製 Azure 個人化工具範例存放庫。

    ```bash
    git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
    ```

1. 瀏覽至 _samples/HttpRequestFeatures_ 以開啟解決方案 `HttpRequestFeaturesExample.sln`。

    如有要求，請允許 Visual Studio 更新個人化工具的 .NET 套件。

### <a name="set-up-azure-personalizer-service"></a>設定 Azure 個人化工具服務

1. 在 Azure 入口網站中[建立個人化工具資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)。

1. 在 Azure 入口網站中，於 [金鑰和端點] 索引標籤中尋找 `Endpoint` 和 `Key1` 或 `Key2` (任何一個都行)。這些是您的 `PersonalizerServiceEndpoint` 和 `PersonalizerApiKey`。
1. 在 **appsettings.json** 中填寫 `PersonalizerServiceEndpoint`。
1. 以下列其中一種方式，將 `PersonalizerApiKey` 設定為[應用程式祕密](/aspnet/core/security/app-secrets)：

    * 如果您使用 .NET Core CLI，則可以使用 `dotnet user-secrets set "PersonalizerApiKey" "<API Key>"` 命令。
    * 如果您使用 Visual Studio，則可以在專案上按一下滑鼠右鍵，然後選取 [管理使用者祕密] 功能表選項來設定個人化工具金鑰。 如此一來，Visual Studio 將會開啟 `secrets.json` 檔案以供您在其中新增金鑰，如下所示：

    ```JSON
    {
      "PersonalizerApiKey": "<your personalizer key here>",
    }
    ```

## <a name="run-the-sample"></a>執行範例

使用下列其中一種方法來建置和執行 HttpRequestFeaturesExample：

* Visual Studio 2019：按 **F5**
* .NET Core CLI：`dotnet build` 然後 `dotnet run`

透過網頁瀏覽器，您可以傳送排名要求和獎勵要求並查看其回應，以及從環境中擷取的 HTTP 要求特性。

> [!div class="mx-imgBorder"]
> ![此螢幕擷取畫面顯示網頁瀏覽器中的 Http 要求功能範例。](./media/tutorial-web-app/web-app-single-page.png)

## <a name="demonstrate-the-personalizer-loop"></a>示範個人化工具迴圈

1. 選取 [產生新的排名要求] 按鈕，為排名 API 呼叫建立新的 JSON 物件。 這會建立動作 (具有特性) 和內容特性並顯示值，讓您可以查看 JSON 的樣子。

    您自己未來的應用程式可能會在用戶端上、伺服器上或同時在兩者上產生動作和特性，也可能會透過呼叫其他服務來產生。

1. 選取 [傳送排名要求] 以將 JSON 物件傳送至伺服器。 伺服器會呼叫個人化工具排名 API。 伺服器會接收回應，並將排名最高的動作傳回給用戶端顯示。

1. 設定獎勵值，然後選取 [傳送獎勵要求] 按鈕。 如果您未變更獎勵值，則用戶端應用程式一律會將 `1` 值傳送給個人化工具。

    > [!div class="mx-imgBorder"]
    > ![此螢幕擷取畫面顯示 [獎勵要求] 區段。](./media/tutorial-web-app/reward-score-api-call.png)

    您自己未來的應用程式可能會在從使用者的用戶端行為以及伺服器上的商務邏輯收集資訊後產生獎勵分數。

## <a name="understand-the-sample-web-app"></a>了解範例 Web 應用程式

範例 Web 應用程式具有 **C# .NET** 伺服器，以負責管理特性的集合，以及傳送和接收個人化工具端點的 HTTP 呼叫。

範例 Web 應用程式會使用 **油墨廓清前端用戶端應用程式** 來擷取特性和處理使用者介面動作，例如按一下按鈕，以及將資料傳送給 .NET 伺服器。

下列各節會說明開發人員在使用個人化工具時所必須了解的伺服器和用戶端部分。

## <a name="rank-api-client-application-sends-context-to-server"></a>排名 API：用戶端應用程式將內容傳送給伺服器

用戶端應用程式會收集使用者的瀏覽器 _使用者代理程式_ 。

> [!div class="mx-imgBorder"]
> ![建置和執行 HTTPRequestFeaturesExample 專案。 瀏覽器視窗隨即開啟，以顯示單頁應用程式。](./media/tutorial-web-app/user-agent.png)

## <a name="rank-api-server-application-calls-personalizer"></a>排名 API：伺服器應用程式呼叫個人化工具

這是與用戶端應用程式搭配的典型 .NET Web 應用程式，會為您提供大部分的樣板程式碼。 不是個人化工具特定的程式碼都會從下列程式碼片段中移除，以便您可以專注於個人化工具特定的程式碼上。

### <a name="create-personalizer-client"></a>建立個人化工具用戶端

在伺服器的 **Startup.cs** 中，個人化工具端點和金鑰可用來建立個人化工具用戶端。 用戶端應用程式不需要與此應用程式中的個人化工具通訊，而是依賴伺服器來進行這些 SDK 呼叫。

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

在伺服器的 **PersonalizerController.cs** 中， **GenerateRank** 伺服器 API 會摘要說明呼叫排名 API 的準備工作

* 為排名呼叫建立新的 `eventId`
* 取得動作清單
* 從使用者取得特性清單，並建立內容特性
* (選擇性) 設定任何排除的動作
* 呼叫排名 API，將結果傳回給用戶端

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

傳送至個人化工具、且包含動作 (具有特性) 和目前內容特性的 JSON 如下所示：

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

### <a name="return-personalizer-rewardactionid-to-client"></a>將個人化工具 rewardActionId 傳回給用戶端

排名 API 會將所選取的最佳動作 **rewardActionId** 傳回給伺服器。

顯示 **rewardActionId** 中傳回的動作。

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

本教學課程會顯示 `rewardActionId` 值。

在您自己未來的應用程式中，則可能是某個確切文字、按鈕或醒目提示的網頁區段。 分數的任何後續分析會傳回此清單，而不是內容的排序。 應顯示的只有 `rewardActionId` 內容。

## <a name="reward-api-collect-information-for-reward"></a>獎勵 API：收集獎勵的資訊

[獎勵分數](concept-rewards.md)應謹慎規劃，就如同規劃特性一樣。 獎勵分數一般應該是介於 0 到 1 之間的值。 該值 _可以_ 在用戶端應用程式中進行部分計算 (根據使用者行為) 以及在伺服器上進行部分計算 (根據商務邏輯和目標)。

如果在 Azure 入口網站中針對個人化工具資源所設定的 [獎勵等候時間] 內，伺服器未呼叫獎勵 API，則會針對該事件使用 [預設獎勵] (一樣是在 Azure 入口網站中進行設定)。

在此範例應用程式中，您可以選取某個值來查看獎勵如何影響選取項目。

## <a name="additional-ways-to-learn-from-this-sample"></a>要從這個範例學習的其他方法

此範例會使用在 Azure 入口網站中針對個人化工具資源所設定的數個時間型事件。 請使用這些值，然後返回此範例 Web 應用程式，以查看變更對排名呼叫和獎勵呼叫的影響：

* 報酬等候時間
* 模型更新頻率

要使用的其他設定包括：
* 預設報酬
* 探索百分比


## <a name="clean-up-resources"></a>清除資源

當您完成本教學課程時，請清除下列資源：

* 刪除您的範例專案目錄。
* 刪除您的個人化工具資源 - 將個人化工具資源視為動作和內容的專用資源，只有在仍使用食物作為動作主題領域時才重複使用該資源。


## <a name="next-steps"></a>後續步驟
* [個人化工具的運作方式](how-personalizer-works.md)
* [特性](concepts-features.md)：了解動作和內容所使用的特性相關概念
* [獎勵](concept-rewards.md)：了解如何計算獎勵