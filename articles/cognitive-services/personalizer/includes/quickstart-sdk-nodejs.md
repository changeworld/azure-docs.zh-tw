---
title: 包含檔案
description: 包含檔案
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 08/27/2020
ms.openlocfilehash: 03680a2a6b4792a2bf522eff1462e29439e0f61b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "89055366"
---
[參考文件](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest) |[程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [套件 (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | [範例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Personalizer)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services)
* 最新版的 [Node.js](https://nodejs.org) 和 NPM。
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title="建立個人化工具資源"  target="_blank">建立個人化工具資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至個人化工具 API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。

## <a name="setting-up"></a>設定

[!INCLUDE [Change model frequency](change-model-frequency.md)]

### <a name="create-a-new-nodejs-application"></a>建立新的 Node.js 應用程式

在主控台視窗 (例如 cmd、PowerShell 或 Bash) 中，為您的應用程式建立新的目錄，並瀏覽至該目錄。

```console
mkdir myapp && cd myapp
```

執行 `npm init -y` 命令以建立 `package.json` 檔案。

```console
npm init -y
```

在您慣用的編輯器或 IDE 中，建立名為 `sample.js` 的新 Node.js 應用程式，並為您的資源端點和訂用帳戶金鑰建立變數。 

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```javascript
const uuidv1 = require('uuid/v1');
const Personalizer = require('@azure/cognitiveservices-personalizer');
const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
const readline = require('readline-sync');

// The key specific to your personalization service instance; e.g. "0123456789abcdef0123456789ABCDEF"
const serviceKey = "<REPLACE-WITH-YOUR-PERSONALIZER-KEY>";

// The endpoint specific to your personalization service instance; 
// e.g. https://<your-resource-name>.cognitiveservices.azure.com
const baseUri = "https://<REPLACE-WITH-YOUR-PERSONALIZER-ENDPOINT>.cognitiveservices.azure.com";
```

### <a name="install-the-nodejs-library-for-personalizer"></a>安裝 Node.js 個人化工具程式庫

使用下列命令安裝適用於 Node.js 的個人化工具用戶端程式庫：

```console
npm install @azure/cognitiveservices-personalizer --save
```

安裝本快速入門的其餘 NPM 套件：

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="object-model"></a>物件模型

個人化工具用戶端是一種 [PersonalizerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest) 物件，會使用含有金鑰的 Microsoft.Rest.ServiceClientCredentials 向 Azure 進行驗證。

若要要求內容的單一最佳時間，請建立 [RankRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rankrequest?view=azure-node-latest)，然後將其傳至 [client.Rank](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest#rank-rankrequest--msrest-requestoptionsbase-) 方法。 Rank 方法會傳回 RankResponse。

若要將獎勵傳送至個人化工具，請建立 [RewardRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rewardrequest?view=azure-node-latest)，然後將其傳遞至 Events 類別上的 [Reward](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/events?view=azure-node-latest#reward-string--rewardrequest--servicecallback-void--) 方法。

在本快速入門中，決定獎勵是很簡單的。 在生產系統中，判斷影響[獎勵分數](../concept-rewards.md)的因素及影響程度可能是複雜的程序，您可能會隨著時間做出變更決定。 這應該是個人化工具架構中的一個主要設計決策。

## <a name="code-examples"></a>程式碼範例

這些程式碼片段會示範如何使用適用於 Node.js 的個人化工具用戶端程式庫來執行下列動作：

* [建立個人化工具用戶端](#authenticate-the-client)
* [排名 API](#request-the-best-action)
* [獎勵 API](#send-a-reward)

## <a name="authenticate-the-client"></a>驗證用戶端

使用您稍早建立的 `serviceKey` 和 `baseUri` 來具現化 `PersonalizerClient`。

```javascript
const credentials = new CognitiveServicesCredentials(serviceKey);

// Initialize Personalization client.
const personalizerClient = new Personalizer.PersonalizerClient(credentials, baseUri);
```

## <a name="get-content-choices-represented-as-actions"></a>取得以動作表示的內容選擇

動作代表您要個人化工具從中選取最佳內容項目的內容選擇。 將下列方法新增至 Program 類別，以代表動作及其特性的組合。

```javascript
function getContextFeaturesList() {
  const timeOfDayFeatures = ['morning', 'afternoon', 'evening', 'night'];
  const tasteFeatures = ['salty', 'sweet'];

  let answer = readline.question("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night\n");
  let selection = parseInt(answer);
  const timeOfDay = selection >= 1 && selection <= 4 ? timeOfDayFeatures[selection - 1] : timeOfDayFeatures[0];

  answer = readline.question("\nWhat type of food would you prefer (enter number)? 1. salty 2. sweet\n");
  selection = parseInt(answer);
  const taste = selection >= 1 && selection <= 2 ? tasteFeatures[selection - 1] : tasteFeatures[0];

  console.log("Selected features:\n");
  console.log("Time of day: " + timeOfDay + "\n");
  console.log("Taste: " + taste + "\n");

  return [
    {
      "time": timeOfDay
    },
    {
      "taste": taste
    }
  ];
}
```

```javascript
function getActionsList() {
  return [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
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
          "taste": "salty",
          "spiceLevel": "low"
        },
        {
          "nutritionLevel": 8
        }
      ]
    }
  ];
}
```

## <a name="create-the-learning-loop"></a>建立學習迴圈

個人化工具學習迴圈是[排名](#request-the-best-action)和[獎勵](#send-a-reward)呼叫的循環。 在本快速入門中，用於個人化內容的每個排名呼叫後面都會接著獎勵呼叫，以告訴個人化工具該服務的成效為何。

下列程式碼會在命令列上進行詢問使用者喜好的循環迴圈，並將該資訊傳送至個人化工具以選取最佳動作，然後向客戶顯示選取項目，讓他們從清單中選擇，接著將獎勵傳送至個人化工具，告知服務的成效為何。

```javascript
let runLoop = true;

do {

  let rankRequest = {}

  // Generate an ID to associate with the request.
  rankRequest.eventId = uuidv1();

  // Get context information from the user.
  rankRequest.contextFeatures = getContextFeaturesList();

  // Get the actions list to choose from personalization with their features.
  rankRequest.actions = getActionsList();

  // Exclude an action for personalization ranking. This action will be held at its current position.
  rankRequest.excludedActions = getExcludedActionsList();

  rankRequest.deferActivation = false;

  // Rank the actions
  const rankResponse = await personalizerClient.rank(rankRequest);

  console.log("\nPersonalization service thinks you would like to have:\n")
  console.log(rankResponse.rewardActionId);

  // Display top choice to user, user agrees or disagrees with top choice
  const reward = getReward();

  console.log("\nPersonalization service ranked the actions with the probabilities as below:\n");
  for (let i = 0; i < rankResponse.ranking.length; i++) {
    console.log(JSON.stringify(rankResponse.ranking[i]) + "\n");
  }

  // Send the reward for the action based on user response.

  const rewardRequest = {
    value: reward
  }

  await personalizerClient.events.reward(rankRequest.eventId, rewardRequest);

  runLoop = continueLoop();

} while (runLoop);
```

請仔細查看下列各節中的排名和報酬呼叫。

新增下列方法，以在執行程式碼檔案之前，[取得內容選項](#get-content-choices-represented-as-actions)：

* getActionsList
* getContextFeaturesList

## <a name="request-the-best-action"></a>要求最佳動作

為了完成排名要求，程式會詢問使用者的喜好來建立內容選擇。 程式可以建立從動作中排除的內容，如 `excludeActions` 所示。 排名要求需要[動作](../concepts-features.md#actions-represent-a-list-of-options)及其特性、currentContext 特性、excludeActions 和唯一排名事件識別碼，才能接收已排名的回應。

本快速入門有一天時間和使用者食物喜好的簡單關係特性。 在生產系統中，判斷和[評估](../concept-feature-evaluation.md)[動作和特性](../concepts-features.md)可能不是簡單的事。

```javascript
let rankRequest = {}

// Generate an ID to associate with the request.
rankRequest.eventId = uuidv1();

// Get context information from the user.
rankRequest.contextFeatures = getContextFeaturesList();

// Get the actions list to choose from personalization with their features.
rankRequest.actions = getActionsList();

// Exclude an action for personalization ranking. This action will be held at its current position.
rankRequest.excludedActions = getExcludedActionsList();

rankRequest.deferActivation = false;

// Rank the actions
const rankResponse = await personalizerClient.rank(rankRequest);
```

## <a name="send-a-reward"></a>傳送獎勵

若要取得獎勵分數以在獎勵要求中傳送，程式會從命令列取得使用者的選取項目，將數值指派給選取項目，然後將唯一的事件識別碼和獎勵分數當作數值傳送至獎勵 API。

本快速入門會指派簡單的數字作為獎勵分數，也就是零或 1。 在生產系統中，視您的特定需求而定，判斷要傳送給[獎勵](../concept-rewards.md)呼叫的時機和內容可能不是簡單的事。

```javascript
const rewardRequest = {
  value: reward
}

await personalizerClient.events.reward(rankRequest.eventId, rewardRequest);
```

## <a name="run-the-program"></a>執行程式

使用 Node.js 從您的應用程式目錄執行應用程式。

```console
node sample.js
```

![快速入門程式會詢問幾個問題以收集使用者偏好 (稱為特性)，然後提供最佳的動作。](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
