---
title: 包含檔案
description: 包含檔案
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 04/27/2020
ms.openlocfilehash: ddf16334830b64c57e9d09a75dfc80fc319a9ccd
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133791"
---
[參考文件](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest) |[程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [套件 (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | [範例](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* 最新版的 [Node.js](https://nodejs.org) 和 NPM。

## <a name="using-this-quickstart"></a>使用此快速入門


使用本快速入門有幾個步驟：

* 在 Azure 入口網站中，建立個人化工具資源
* 在 Azure 入口網站中，於個人化工具資源的 [設定] 頁面上，將模型更新頻率變更為非常短的間隔
* 在程式碼編輯器中，建立程式碼檔案並編輯程式碼檔案
* 在命令列或終端機中，從命令列安裝 SDK
* 在命令列或終端機中，執行程式碼檔案

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-nodejs-application"></a>建立新的 Node.js 應用程式

在主控台視窗 (例如 cmd、PowerShell 或 Bash) 中，為您的應用程式建立新的目錄，並瀏覽至該目錄。

```console
mkdir myapp && cd myapp
```

執行 `npm init -y` 命令以建立 `package.json` 檔案。

```console
npm init -y
```

## <a name="install-the-nodejs-library-for-personalizer"></a>安裝 Node.js 個人化工具程式庫

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

* [建立個人化工具用戶端](#create-a-personalizer-client)
* [排名 API](#request-the-best-action)
* [獎勵 API](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>建立新的 Node.js 應用程式

在您慣用的編輯器或名為 `sample.js` 的 IDE 中，建立新的 Node.js 應用程式。

## <a name="add-the-dependencies"></a>新增相依性

在您慣用的編輯器或 IDE 中，開啟 **sample.js** 檔案。 新增下列 `requires` 以新增 NPM 套件：

[!code-javascript[Add module dependencies](~/samples-personalizer/quickstarts/node/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>新增個人化工具資源資訊

針對從環境變數中提取的資源 Azure 金鑰和端點 (名稱為 `PERSONALIZER_KEY` 和 `PERSONALIZER_ENDPOINT`) 建立變數。 如果您在應用程式啟動後才建立環境變數，則執行該應用程式的編輯器、IDE 或殼層必須先關閉再重新載入後，才能存取該變數。 這些方法稍後會在本快速入門中建立。

[!code-javascript[Add Personalizer resource information](~/samples-personalizer/quickstarts/node/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>建立個人化工具用戶端

接下來，建立可傳回個人化工具用戶端的方法。 方法的參數是 `PERSONALIZER_RESOURCE_ENDPOINT`，而 ApiKey 是 `PERSONALIZER_RESOURCE_KEY`。

[!code-javascript[Create a Personalizer client](~/samples-personalizer/quickstarts/node/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>取得以動作表示的內容選擇

動作代表您要個人化工具從中選取最佳內容項目的內容選擇。 將下列方法新增至 Program 類別，以代表動作及其特性的組合。

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>建立學習迴圈

個人化工具學習迴圈是[排名](#request-the-best-action)和[獎勵](#send-a-reward)呼叫的循環。 在本快速入門中，用於個人化內容的每個排名呼叫後面都會接著獎勵呼叫，以告訴個人化工具該服務的成效為何。

下列程式碼會在命令列上進行詢問使用者喜好的循環迴圈，並將該資訊傳送至個人化工具以選取最佳動作，然後向客戶顯示選取項目，讓他們從清單中選擇，接著將獎勵傳送至個人化工具，告知服務的成效為何。

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

請仔細查看下列各節中的排名和報酬呼叫。

新增下列方法，以在執行程式碼檔案之前，[取得內容選項](#get-content-choices-represented-as-actions)：

* getActionsList
* getContextFeaturesList

## <a name="request-the-best-action"></a>要求最佳動作

為了完成排名要求，程式會詢問使用者的喜好來建立內容選擇。 程式可以建立從動作中排除的內容，如 `excludeActions` 所示。 排名要求需要[動作](../concepts-features.md#actions-represent-a-list-of-options)及其特性、currentContext 特性、excludeActions 和唯一排名事件識別碼，才能接收已排名的回應。

本快速入門有一天時間和使用者食物喜好的簡單關係特性。 在生產系統中，判斷和[評估](../concept-feature-evaluation.md)[動作和特性](../concepts-features.md)可能不是簡單的事。

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>傳送獎勵


若要取得獎勵分數以在獎勵要求中傳送，程式會從命令列取得使用者的選取項目，將數值指派給選取項目，然後將唯一的事件識別碼和獎勵分數當作數值傳送至獎勵 API。

本快速入門會指派簡單的數字作為獎勵分數，也就是零或 1。 在生產系統中，視您的特定需求而定，判斷要傳送給[獎勵](../concept-rewards.md)呼叫的時機和內容可能不是簡單的事。

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>執行程式

使用 Node.js 從您的應用程式目錄執行應用程式。

```console
node sample.js
```

![快速入門程式會詢問幾個問題以收集使用者偏好 (稱為特性)，然後提供最佳的動作。](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
