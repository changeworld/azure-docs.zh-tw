---
title: 包含檔案
description: 包含檔案
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/28/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 6e240a0c5d5d77489c92862238c2e5041bdeabe3
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2020
ms.locfileid: "84171327"
---
使用適用於 Node.js 的 Language Understanding (LUIS) 撰寫用戶端程式庫可以：

* 建立應用程式。
* 新增意圖、實體和範例語句。
* 新增功能，例如片語清單。
* 定型和發佈應用程式。
* 刪除應用程式

[參考文件](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring) | [撰寫套件 (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)、[執行階段套件 (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [範例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_authoring_quickstart.js)

## <a name="prerequisites"></a>Prerequisites

* Language Understanding 撰寫資源：[在 Azure 入口網站中建立一個](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)

## <a name="setting-up"></a>設定

### <a name="get-your-language-understanding-luis-starter-key"></a>取得您的 Language Understanding (LUIS) 入門金鑰

藉由建立 LUIS 撰寫資源，取得您的[入門金鑰](../luis-how-to-azure-subscription.md#starter-key)。 請保留您的金鑰和金鑰端點，以進行下一個步驟。

### <a name="create-an-environment-variable"></a>建立環境變數

使用金鑰和金鑰區域，建立兩個環境變數以進行驗證：

* `LUIS_AUTHORING_KEY` - 用於驗證您要求的資源金鑰。
* `LUIS_AUTHORING_ENDPOINT` - 與金鑰相關聯的端點。

請使用適合您作業系統的指示。

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_AUTHORING_KEY <replace-with-your-luis-authoring-key
setx LUIS_AUTHORING_ENDPOINT <replace-with-your-luis-authoring-endpoint>
```

新增環境變數之後，請重新啟動主控台視窗。

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

新增環境變數之後，從主控台視窗執行 `source ~/.bashrc`，讓變更生效。

#### <a name="macos"></a>[macOS](#tab/unix)

編輯 `.bash_profile`，然後新增環境變數：

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

新增環境變數之後，從主控台視窗執行 `source .bash_profile`，讓變更生效。
***

### <a name="install-the-npm-library-for-luis-authoring"></a>安裝可用來撰寫 LUIS 的 NPM 程式庫

在應用程式目錄中，使用下列命令來安裝相依性：

```console
npm install @azure/cognitiveservices-luis-authoring @azure/ms-rest-js
```

## <a name="object-model"></a>物件模型

Language Understanding (LUIS) 撰寫用戶端是向 Azure 進行驗證的 [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest)物件，其中包含您的撰寫金鑰。

建立用戶端之後，請使用此用戶端來存取功能，包括：

* 應用程式 - [新增](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#add-applicationcreateobject--msrest-requestoptionsbase-)、[刪除](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-)、[發佈](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* 範例語句 - [依批次新增](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-)、[依識別碼刪除](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#deletemethod-string--string--number--msrest-requestoptionsbase-)
* 功能 - 管理[片語清單](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/features?view=azure-node-latest#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-)
* 模型 - 管理[意圖](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-)和實體
* 模式 - 管理[模式](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/pattern?view=azure-node-latest#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-)
* 定型 - [定型](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-)應用程式及輪詢[定型狀態](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-)
* [版本](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/versions?view=azure-node-latest) - 使用複製、匯出和刪除進行管理


## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 Node.js 的 Language Understanding (LUIS) 撰寫用戶端程式庫來執行下列動作：

* [建立應用程式](#create-a-luis-app)
* [新增實體](#create-entities-for-the-app)
* [新增對應方式](#create-intent-for-the-app)
* [新增範例語句](#add-example-utterance-to-intent)
* [定型應用程式](#train-the-app)
* [發佈應用程式](#publish-a-language-understanding-app)
* [刪除應用程式](#delete-a-language-understanding-app)
* [列出應用程式](#list-language-understanding-apps)

## <a name="create-a-new-nodejs-application"></a>建立新的 Node.js 應用程式

在您慣用的編輯器或 IDE 中，建立名為 `luis_authoring_quickstart.js` 的新文字檔。 然後新增下列相依性。

[!code-javascript[Create a new application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=Dependencies)]

為資源的 Azure 端點和金鑰建立變數。 如果您在啟動應用程式後才建立環境變數，則必須先關閉執行該應用程式的編輯器、IDE 或殼層，再重新加以開啟，才能存取該變數。

[!code-javascript[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=Variables)]

## <a name="authenticate-the-client"></a>驗證用戶端

使用您的金鑰建立 [CognitiveServicesCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest) 物件，並使用該物件與您的端點建立 [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) 物件。

[!code-javascript[Create LUIS client object](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>建立 LUIS 應用程式

1. 建立 LUIS 應用程式，並使其包含用來保存意圖、實體和範例語句的自然語言處理 (NLP) 模型。

1. 建立 [AppsOperation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) 物件用來建立應用程式的 [add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) 方法。 名稱和語言文化特性是必要屬性。

    [!code-javascript[Create LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringCreateApplication&highlight=9-11)]


## <a name="create-intent-for-the-app"></a>建立應用程式的意圖
LUIS 應用程式模型中的主要物件為意圖。 意圖會與使用者語句_意圖_的群組相對應。 使用者可以詢問問題，或發出預期會由 Bot (或其他用戶端應用程式) 提供特定回應的陳述。  意圖的範例包括預約航班、詢問目的地城市的天氣，以及詢問客戶服務的連絡人資訊。

使用具有唯一意圖名稱的 [model.add_intent](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-)，然後傳遞應用程式識別碼、版本識別碼和新的意圖名稱。

[!code-javascript[Create intent](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringAddIntents&highlight=2-6)]

## <a name="create-entities-for-the-app"></a>建立應用程式的實體

雖然實體並非必要，但大部分的應用程式中都會有實體。 實體會從使用者語句中擷取滿足使用者意圖所需的資訊。 目前有數種類型的[預建](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-)和自訂實體，每一種分別有其本身的資料轉換物件 (DTO) 模型。  會新增至應用程式的常見預建實體包括 [number](../luis-reference-prebuilt-number.md)、[datetimeV2](../luis-reference-prebuilt-datetimev2.md)、[geographyV2](../luis-reference-prebuilt-geographyv2.md)、[ordinal](../luis-reference-prebuilt-ordinal.md)。

這個 **add_entities** 方法會建立一個具有兩個角色的 `Location` 簡單實體、一個 `Class` 簡單實體、一個 `Flight` 複合實體，並新增數個預建實體。

請務必了解，實體上不會標示意圖。 它們可以且通常會套用至許多意圖。 只有範例使用者語句會標示特定的單一意圖。

實體的建立方法是[模型](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest)類別的一部分。 每個實體類型都有本身的資料轉換物件 (DTO) 模型。

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringAddEntities&highlight=2-6)]

## <a name="add-example-utterance-to-intent"></a>將範例語句新增至意圖

為了判斷語句的意圖及擷取實體，應用程式需要語句的範例。 這些範例必須以特定的單一意圖為目標，且應標示所有的自訂實體。 預建實體不需要標示。

藉由建立 [ExampleLabelObject](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject?view=azure-node-latest) 物件的清單來新增範例語句，每個範例語句各有一個物件。 每個範例都應以實體名稱和實體值的名稱/值配對字典來標示所有實體。 實體值應與範例語句的文字中所顯示的完全相同。

使用應用程式識別碼、版本識別碼和範例清單來呼叫 [examples.batch](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-)。 此呼叫會以結果清單來回應。 您必須檢查每個範例的結果，以確定範例已成功新增至模型。

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringBatchAddUtterancesForIntent&highlight=52-56)]

## <a name="train-the-app"></a>進行應用程式定型

建立模型之後，必須針對此版本的模型將 LUIS 應用程式定型。 已定型的模型可用於[容器](../luis-container-howto.md)中，或[發佈](../luis-how-to-publish-app.md)至預備或產品位置。

[train.trainVersion](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) 方法需要應用程式識別碼和版本識別碼。

非常小的模型 (如本快速入門所示) 會非常快速地進行定型。 針對生產層級的應用程式，在為應用程式定型時，必須進行 [get_status](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-) 方法的輪詢呼叫，以確認定型成功的時間或是否成功。 其回應是 [ModelTrainingInfo](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo?view=azure-node-latest) 物件的清單，每個物件各有不同的狀態。 所有物件都必須成功，才會將定型視為完成。

[!code-javascript[Train LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringTrainVersion&highlight=2-5)]

定型所有模型需要一些時間。 請使用 operationResult 來檢查定型狀態。

[!code-javascript[Get training status](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringWaitForOperation&highlight=11-14)]

## <a name="publish-a-language-understanding-app"></a>發佈 Language Understanding 應用程式

使用 [app.publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-) 方法發佈 LUIS 應用程式。 這會將目前的定型版本發佈至端點上的指定位置。 您的用戶端應用程式會使用此端點來傳送使用者語句，以進行意圖和實體擷取的預測。

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringPublishVersion&highlight=2-5)]

## <a name="delete-a-language-understanding-app"></a>刪除 Language Understanding 應用程式

使用 [app.deleteMethod](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-) 方法來刪除 LUIS 應用程式。 這會刪除目前的應用程式。

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringDeleteApp&highlight=2)]

## <a name="list-language-understanding-apps"></a>列出 Language Understanding 應用程式

取得與 Language Understanding 金鑰相關聯的應用程式清單

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringListApps)]

## <a name="run-the-application"></a>執行應用程式

使用快速入門檔案上使用 `node luis_authoring_quickstart.js` 命令執行應用程式。

```console
node luis_authoring_quickstart.js
```

應用程式的命令列輸出如下：

```console
Created LUIS app with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7
Entity Destination created.
Entity Class created.
Entity Flight created.
Intent FindFlights added.
Created 3 entity labels.
Created 3 entity labels.
Created 3 entity labels.
Example utterances added.
Waiting for train operation to finish...
Current model status: ["Queued"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["Success"]
Application published. Endpoint URL: https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/e137a439-b3e0-4e16-a7a8-a9746e0715f7
Application with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7 deleted. Operation result: Operation Successful
```
