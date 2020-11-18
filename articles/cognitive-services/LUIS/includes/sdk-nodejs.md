---
title: 包含檔案
description: 包含檔案
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/01/2020
ms.topic: include
ms.custom: include file, devx-track-js, cog-serv-seo-aug-2020
ms.openlocfilehash: f81ec647eefd8b3bd59a60855c0383a32caf8acb
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94573842"
---
使用適用於 Node.js 的 Language Understanding (LUIS) 用戶端程式庫，可以：

* 建立應用程式
* 使用範例語句新增意圖、機器學習實體
* 定型和發佈應用程式
* 查詢預測執行階段

[參考文件](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest) |  [製作](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring)和[預測](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) 程式庫原始程式碼 | [製作](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)和[預測](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) NPM | [範例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/sdk-3x/index.js)

## <a name="prerequisites"></a>Prerequisites

* [Node.js](https://nodejs.org)
* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services)
* 擁有 Azure 訂用帳戶之後，請在 Azure 入口網站中[建立 Language Understanding 撰寫資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)，以取得您的金鑰和端點。 部署完成後，按一下 [移至資源] 按鈕。
    * 您需要您[建立](../luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal)之資源的金鑰和端點，以將應用程式連線至 Language Understanding 撰寫。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。 您可以使用免費定價層 (`F0`) 來試用服務。

## <a name="setting-up"></a>設定

### <a name="create-a-new-javascript-application"></a>建立新的 JavaScript 應用程式

1. 在主控台視窗中，為您的應用程式建立新的目錄，並移至該目錄。

    ```console
    mkdir quickstart-sdk && cd quickstart-sdk
    ```

1. 藉由建立 `package.json` 檔案，將目錄初始化為 JavaScript 應用程式。

    ```console
    npm init -y
    ```

1. 針對您的 JavaScript 程式碼，建立名為 `index.js` 的檔案。

    ```console
    touch index.js
    ```

### <a name="install-the-npm-libraries"></a>安裝 NPM 程式庫

在應用程式目錄中，使用下列命令來安裝相依性，一次執行一行：

```console
npm install @azure/ms-rest-js
npm install @azure/cognitiveservices-luis-authoring
npm install @azure/cognitiveservices-luis-runtime
```

您的 `package.json` 看起來應該類似：

```json
{
  "name": "quickstart-sdk",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/cognitiveservices-luis-authoring": "^4.0.0-preview.3",
    "@azure/cognitiveservices-luis-runtime": "^5.0.0",
    "@azure/ms-rest-js": "^2.0.8"
  }
}
```

## <a name="authoring-object-model"></a>製作物件模型

Language Understanding (LUIS) 撰寫用戶端是向 Azure 進行驗證的 [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest)物件，其中包含您的撰寫金鑰。

## <a name="code-examples-for-authoring"></a>可供撰寫的程式碼範例

建立用戶端之後，請使用此用戶端來存取功能，包括：

* 應用程式 - [新增](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#add-applicationcreateobject--msrest-requestoptionsbase-)、[刪除](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-)、[發佈](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* 範例語句 - [依批次新增](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-)、[依識別碼刪除](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#deletemethod-string--string--number--msrest-requestoptionsbase-)
* 功能 - 管理[片語清單](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/features?view=azure-node-latest#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-)
* 模型 - 管理[意圖](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-)和實體
* 模式 - 管理[模式](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/pattern?view=azure-node-latest#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-)
* 定型 - [定型](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-)應用程式及輪詢[定型狀態](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-)
* [版本](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/versions?view=azure-node-latest) - 使用複製、匯出和刪除進行管理

## <a name="prediction-object-model"></a>預測物件模型

Language Understanding (LUIS) 撰寫用戶端是向 Azure 進行驗證的 [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest)物件，其中包含您的撰寫金鑰。

## <a name="code-examples-for-prediction-runtime"></a>預測執行階段的程式碼範例

建立用戶端之後，請使用此用戶端來存取功能，包括：

* 依 `staging` 或 `production` 位置進行[預測](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)
* [依版本進行預測](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

[!INCLUDE [Bookmark links to same article](sdk-code-examples.md)]

## <a name="add-the-dependencies"></a>新增相依性

在您慣用的編輯器或命名的 IDE 中開啟 `index.js`，並新增下列相依性。

[!code-javascript[Add NPM libraries to code file](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=Dependencies)]

## <a name="add-boilerplate-code"></a>新增未定案程式碼

1. 新增 `quickstart` 方法和其呼叫。 這個方法會保存剩下的大部分程式碼。 這個方法會在檔案結尾呼叫。

    ```javascript
    const quickstart = async () => {

        // add calls here


    }
    quickstart()
        .then(result => console.log("Done"))
        .catch(err => {
            console.log(`Error: ${err}`)
            })
    ```

1. 除非另有指定，否則請在快速入門方法中新增其餘的程式碼。

## <a name="create-variables-for-the-app"></a>為應用程式建立變數

建立兩組變數：您可變更第一組，第二組則保留為其出現在程式碼範例中的樣子。 

1. 建立變數來保存您的製作金鑰和資源名稱。

    [!code-javascript[Variables you need to change](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=VariablesYouChange)]

1. 建立變數來保存您的端點、應用程式名稱、版本和意圖名稱。

    [!code-javascript[Variables you don't need to change](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=VariablesYouDontNeedToChangeChange)]

## <a name="authenticate-the-client"></a>驗證用戶端

使用您的金鑰建立 [CognitiveServicesCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest) 物件，並使用該物件與您的端點建立 [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) 物件。

[!code-javascript[Authenticate the client](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>建立 LUIS 應用程式

LUIS 應用程式包含納入意圖、實體和範例語句的自然語言處理 (NLP) 模型。

建立 [AppsOperation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) 物件用來建立應用程式的 [add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) 方法。 名稱和語言文化特性是必要屬性。

[!code-javascript[Create a LUIS app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringCreateApplication)]


## <a name="create-intent-for-the-app"></a>建立應用程式的意圖
LUIS 應用程式模型中的主要物件為意圖。 意圖會與使用者語句 _意圖_ 的群組相對應。 使用者可以詢問問題，或發出預期會由 Bot (或其他用戶端應用程式) 提供特定回應的陳述。 意圖的範例包括預約航班、詢問目的地城市的天氣，以及詢問客戶服務的連絡人資訊。

使用具有唯一意圖名稱的 [model.add_intent](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-)，然後傳遞應用程式識別碼、版本識別碼和新的意圖名稱。

`intentName` 值已硬式編碼為 `OrderPizzaIntent`，作為 [為應用程式建立變數](#create-variables-for-the-app)區段中的一部分變數。

[!code-javascript[Create intent for the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AddIntent)]

## <a name="create-entities-for-the-app"></a>建立應用程式的實體

雖然實體並非必要，但大部分的應用程式中都會有實體。 實體會從使用者語句中擷取滿足使用者意圖所需的資訊。 目前有數種類型的[預建](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-)和自訂實體，每一種分別有其本身的資料轉換物件 (DTO) 模型。  會新增至應用程式的常見預建實體包括 [number](../luis-reference-prebuilt-number.md)、[datetimeV2](../luis-reference-prebuilt-datetimev2.md)、[geographyV2](../luis-reference-prebuilt-geographyv2.md)、[ordinal](../luis-reference-prebuilt-ordinal.md)。

請務必了解，實體上不會標示意圖。 它們可以且通常會套用至許多意圖。 只有範例使用者語句會標示特定的單一意圖。

實體的建立方法是[模型](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest)類別的一部分。 每個實體類型都有本身的資料轉換物件 (DTO) 模型。

實體建立程式碼會建立機器學習實體，其中包含套用至 `Quantity` 子實體的子實體和功能。

:::image type="content" source="../media/quickstart-sdk/machine-learned-entity.png" alt-text="入口網站中顯示所建立實體的部分螢幕擷取畫面，這是一個機器學習實體，其中包含套用至 `Quantity` 子實體的子實體和功能。":::

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringAddEntities)]

將下列方法放在 `quickstart` 的方法上方，以尋找數量子實體列的識別碼，並將功能指派給該子實體。

[!code-javascript[Find subentity id](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringSortModelObject)]

## <a name="add-example-utterance-to-intent"></a>將範例語句新增至意圖

為了判斷語句的意圖及擷取實體，應用程式需要語句的範例。 這些範例必須以特定的單一意圖為目標，且應標示所有的自訂實體。 預建實體不需要標示。

藉由建立 [ExampleLabelObject](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject?view=azure-node-latest) 物件的清單來新增範例語句，每個範例語句各有一個物件。 每個範例都應以實體名稱和實體值的名稱/值配對字典來標示所有實體。 實體值應與範例語句的文字中所顯示的完全相同。

:::image type="content" source="../media/quickstart-sdk/labeled-example-machine-learned-entity.png" alt-text="在入口網站中顯示標記範例語句的部分螢幕擷取畫面。":::

使用應用程式識別碼、版本識別碼和範例呼叫 [examples.add](https://docs.microsoft.com//javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#add-string--string--examplelabelobject--models-examplesaddoptionalparams-)。

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringAddLabeledExamples)]

## <a name="train-the-app"></a>進行應用程式定型

建立模型之後，必須針對此版本的模型將 LUIS 應用程式定型。 已定型的模型可用於[容器](../luis-container-howto.md)中，或[發佈](../luis-how-to-publish-app.md)至預備或產品位置。

[train.trainVersion](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) 方法需要應用程式識別碼和版本識別碼。

非常小的模型 (如本快速入門所示) 會非常快速地進行定型。 針對生產層級的應用程式，在為應用程式定型時，必須進行 [get_status](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-) 方法的輪詢呼叫，以確認定型成功的時間或是否成功。 其回應是 [ModelTrainingInfo](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo?view=azure-node-latest) 物件的清單，每個物件各有不同的狀態。 所有物件都必須成功，才會將定型視為完成。

[!code-javascript[Train the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=TrainAppVersion)]

## <a name="publish-app-to-production-slot"></a>將應用程式發佈至生產位置

使用 [app.publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-) 方法發佈 LUIS 應用程式。 這會將目前的定型版本發佈至端點上的指定位置。 您的用戶端應用程式會使用此端點來傳送使用者語句，以進行意圖和實體擷取的預測。

[!code-javascript[Publish app to production slot](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=PublishVersion)]


## <a name="authenticate-the-prediction-runtime-client"></a>驗證預測執行階段用戶端

以您的金鑰使用 msRest.ApiKeyCredentials 物件，並使用該物件與您的端點建立 [LUIS.LUISRuntimeClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) 物件。

[!INCLUDE [Caution about using authoring key](caution-authoring-key.md)]

[!code-javascript [Authenticate the prediction runtime client](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=PredictionCreateClient)]

## <a name="get-prediction-from-runtime"></a>從執行階段取得預測

新增下列程式碼，以建立對預測執行階段的要求。 使用者語句是 [predictionRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest) 物件的一部分。

**[luisRuntimeClient.prediction.getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** 方法需要數個參數，例如應用程式識別碼、位置名稱，以及用來完成要求的預測要求物件。 其他選項 (例如詳細資訊、顯示所有意圖和記錄) 是選擇性的。

[!code-javascript [Get prediction from runtime](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=QueryPredictionEndpoint)]

[!INCLUDE [Prediction JSON response](sdk-json.md)]

## <a name="run-the-application"></a>執行應用程式

使用快速入門檔案上使用 `node index.js` 命令執行應用程式。

```console
node index.js
```
