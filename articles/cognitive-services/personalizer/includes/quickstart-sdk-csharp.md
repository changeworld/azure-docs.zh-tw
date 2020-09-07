---
title: 包含檔案
description: 包含檔案
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 08/25/2020
ms.openlocfilehash: f0e7e0909de80ead7b300a4d396bf3eb84515745
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055365"
---
[參考文件](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [套件 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | [範例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services)
* 最新版 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title="建立個人化工具資源"  target="_blank">建立個人化工具資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至個人化工具 API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。

## <a name="setting-up"></a>設定

[!INCLUDE [Change model frequency](change-model-frequency.md)]

### <a name="create-a-new-c-application"></a>建立新的 C# 應用程式

在您慣用的編輯器或 IDE 中，建立新的 .NET Core 應用程式。

在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 dotnet `new` 命令建立名為 `personalizer-quickstart` 的新主控台應用程式。 此命令會建立簡單的 "Hello World" C# 專案，內含單一原始程式檔：`Program.cs`。

```console
dotnet new console -n personalizer-quickstart
```

將目錄變更為新建立的應用程式資料夾。 您可以使用下列命令來建置應用程式：

```console
dotnet build
```

建置輸出應該不會有警告或錯誤。

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>安裝用戶端程式庫

在應用程式目錄中，使用下列命令安裝適用於 .NET 的個人化工具用戶端程式庫：

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

> [!TIP]
> 如果您使用 Visual Studio IDE，則可以取得可下載 NuGet 套件形式的用戶端程式庫。

從專案目錄，在慣用的編輯器或 IDE 中開啟 `Program.cs` 檔案。 加入以下 using 指示詞：

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
using Microsoft.Azure.CognitiveServices.Personalizer.Models;
using System;
using System.Collections.Generic;
using System.Linq;
```

## <a name="object-model"></a>物件模型

個人化工具用戶端是一種 [PersonalizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) 物件，會使用含有金鑰的 Microsoft.Rest.ServiceClientCredentials 向 Azure 進行驗證。

若要要求內容的單一最佳時間，請建立 [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview)，然後將其傳至 [client.Rank](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview) 方法。 Rank 方法會傳回 RankResponse。

若要將獎勵分數傳送至個人化工具，請建立 [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview)，然後將其傳至 [client.Reward](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview) 方法。

在本快速入門中，決定獎勵分數是很簡單的。 在生產系統中，判斷影響[獎勵分數](../concept-rewards.md)的因素及影響程度可能是複雜的程序，您可能會隨著時間做出變更決定。 此設計決策應該是您個人化工具架構中的其中一個主要決策。

## <a name="code-examples"></a>程式碼範例

這些程式碼片段會示範如何使用適用於 .NET 的個人化工具用戶端程式庫來執行下列工作：

* [建立個人化工具用戶端](#authenticate-the-client)
* [排名 API](#request-the-best-action)
* [獎勵 API](#send-a-reward)


## <a name="authenticate-the-client"></a>驗證用戶端

在本節中，您將執行兩個動作：
* 指定您的金鑰和端點
* 建立個人化工具用戶端

首先，將下列幾行新增至您的 Program 類別。 務必從您的個人化工具資源新增金鑰和端點。

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```csharp
private static readonly string ApiKey = "REPLACE-WITH-YOUR-PERSONALIZER-KEY";
private static readonly string ServiceEndpoint = "https://REPLACE-WITH-YOUR-PERSONALIZER-RESOURCE-NAME.cognitiveservices.azure.com";
```

接下來，將方法新增至您的程式，以建立新的個人化工具用戶端。

```csharp
static PersonalizerClient InitializePersonalizerClient(string url)
{
    PersonalizerClient client = new PersonalizerClient(
        new ApiKeyServiceClientCredentials(ApiKey)) { Endpoint = url };

    return client;
}
```

## <a name="get-food-items-as-rankable-actions"></a>取得食物項目作為可排名的動作

動作代表您要個人化工具從中選取最佳內容項目的內容選擇。 將下列方法新增至 Program 類別，以代表動作及其特性的組合。 

```csharp
static IList<RankableAction> GetActions()
{
    IList<RankableAction> actions = new List<RankableAction>
    {
        new RankableAction
        {
            Id = "pasta",
            Features =
            new List<object>() { new { taste = "salty", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
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
            new List<object>() { new { taste = "salty", spiceLevel = "low" }, new { nutritionLevel = 8 } }
        }
    };

    return actions;
}
```

## <a name="get-user-preferences-for-context"></a>取得內容的使用者喜好設定

將下列方法新增至 [程式] 類別，以從命令列取得一天時間和目前食物喜好的使用者輸入。 這些會作為關係特性使用。

```csharp
static string GetUsersTimeOfDay()
{
    string[] timeOfDayFeatures = new string[] { "morning", "afternoon", "evening", "night" };

    Console.WriteLine("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night");
    if (!int.TryParse(GetKey(), out int timeIndex) || timeIndex < 1 || timeIndex > timeOfDayFeatures.Length)
    {
        Console.WriteLine("\nEntered value is invalid. Setting feature value to " + timeOfDayFeatures[0] + ".");
        timeIndex = 1;
    }

    return timeOfDayFeatures[timeIndex - 1];
}
```

```csharp
static string GetUsersTastePreference()
{
    string[] tasteFeatures = new string[] { "salty", "sweet" };

    Console.WriteLine("\nWhat type of food would you prefer (enter number)? 1. salty 2. sweet");
    if (!int.TryParse(GetKey(), out int tasteIndex) || tasteIndex < 1 || tasteIndex > tasteFeatures.Length)
    {
        Console.WriteLine("\nEntered value is invalid. Setting feature value to " + tasteFeatures[0] + ".");
        tasteIndex = 1;
    }

    return tasteFeatures[tasteIndex - 1];
}
```

這兩個方法都會使用 `GetKey` 方法從命令列讀取使用者的選取。

```csharp
private static string GetKey()
{
    return Console.ReadKey().Key.ToString().Last().ToString().ToUpper();
}
```

## <a name="create-the-learning-loop"></a>建立學習迴圈

個人化工具學習迴圈是[排名](#request-the-best-action)和[獎勵](#send-a-reward)呼叫的循環。 在本快速入門中，用於個人化內容的每個排名呼叫後面都會接著獎勵呼叫，以告訴個人化工具該服務的成效為何。

下列程式碼會在命令列上進行詢問使用者喜好的循環迴圈，並將該資訊傳送至個人化工具以選取最佳動作，然後向客戶顯示選取項目，讓他們從清單中選擇，接著將獎勵分數傳送至個人化工具，告知服務的成效為何。

```csharp
static void Main(string[] args)
{
    int iteration = 1;
    bool runLoop = true;

    IList<RankableAction> actions = GetActions();

    PersonalizerClient client = InitializePersonalizerClient(ServiceEndpoint);

    do
    {
        Console.WriteLine("\nIteration: " + iteration++);

        string timeOfDayFeature = GetUsersTimeOfDay();
        string tasteFeature = GetUsersTastePreference();

        IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature }
        };

        IList<string> excludeActions = new List<string> { "juice" };

        string eventId = Guid.NewGuid().ToString();

        var request = new RankRequest(actions, currentContext, excludeActions, eventId);
        RankResponse response = client.Rank(request);

        Console.WriteLine("\nPersonalizer service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

        float reward = 0.0f;
        string answer = GetKey();

        if (answer == "Y")
        {
            reward = 1;
            Console.WriteLine("\nGreat! Enjoy your food.");
        }
        else if (answer == "N")
        {
            reward = 0;
            Console.WriteLine("\nYou didn't like the recommended food choice.");
        }
        else
        {
            Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
        }

        Console.WriteLine("\nPersonalizer service ranked the actions with the probabilities as below:");
        foreach (var rankedResponse in response.Ranking)
        {
            Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
        }

        client.Reward(response.EventId, new RewardRequest(reward));

        Console.WriteLine("\nPress q to break, any other key to continue:");
        runLoop = !(GetKey() == "Q");

    } while (runLoop);
}
```

新增下列方法，以在執行程式碼檔案之前，[取得內容選項](#get-food-items-as-rankable-actions)：

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-the-best-action"></a>要求最佳動作

為了完成排名要求，程式會詢問使用者的喜好，以建立內容選擇的 `currentContext`。 程式可以建立從動作中排除的內容，如 `excludeActions` 所示。 排名要求需要動作及其特性、currentContext 特性、excludeActions 和唯一排名事件識別碼，才能接收回應。

本快速入門有一天時間和使用者食物喜好的簡單關係特性。 在生產系統中，判斷和[評估](../concept-feature-evaluation.md)[動作和特性](../concepts-features.md)可能不是簡單的事。

```csharp
string timeOfDayFeature = GetUsersTimeOfDay();
string tasteFeature = GetUsersTastePreference();

IList<object> currentContext = new List<object>() {
    new { time = timeOfDayFeature },
    new { taste = tasteFeature }
};

IList<string> excludeActions = new List<string> { "juice" };

string eventId = Guid.NewGuid().ToString();

var request = new RankRequest(actions, currentContext, excludeActions, eventId);
RankResponse response = client.Rank(request);
```

## <a name="send-a-reward"></a>傳送獎勵

若要取得獎勵分數以在獎勵要求中傳送，程式會從命令列取得使用者的選取項目，將數值指派給選取項目，然後將唯一的事件識別碼和獎勵分數當作數值傳送至獎勵 API。

本快速入門會指派簡單的數字作為獎勵分數，也就是零或 1。 在生產系統中，視您的特定需求而定，判斷要傳送給[獎勵](../concept-rewards.md)呼叫的時機和內容可能不是簡單的事。

```csharp
float reward = 0.0f;
string answer = GetKey();

if (answer == "Y")
{
    reward = 1;
    Console.WriteLine("\nGreat! Enjoy your food.");
}
else if (answer == "N")
{
    reward = 0;
    Console.WriteLine("\nYou didn't like the recommended food choice.");
}
else
{
    Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
}

Console.WriteLine("\nPersonalizer service ranked the actions with the probabilities as below:");
foreach (var rankedResponse in response.Ranking)
{
    Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
}

// Send the reward for the action based on user response.
client.Reward(response.EventId, new RewardRequest(reward));
```

## <a name="run-the-program"></a>執行程式

從應用程式目錄使用 dotnet `run` 命令來執行應用程式。

```console
dotnet run
```

![快速入門程式會詢問幾個問題以收集使用者偏好 (稱為特性)，然後提供最佳的動作。](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

[這裡可取得適用於此快速入門的原始程式碼](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer)。
