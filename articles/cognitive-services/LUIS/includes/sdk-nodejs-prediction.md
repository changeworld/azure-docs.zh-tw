---
title: 包含檔案
description: 包含檔案
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 9c15e4217c5331346c5a95329bae7e2a4f0e0841
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732036"
---
使用適用於 Node.js 的 Language Understanding (LUIS) 執行階段用戶端程式庫，可以：

* 依位置進行預測
* 依版本進行預測

[參考文件](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) | [執行階段套件 (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [範例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_prediction.js)

## <a name="prerequisites"></a>Prerequisites

* Language Understanding 執行階段資源：[在 Azure 入口網站中建立一個](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)
* LUIS 應用程式識別碼 - 使用 `df67dcdb-c37d-46af-88e1-8b97951ca1c2` 的公用 IoT 應用程式識別碼。 快速入門程式碼中使用的使用者查詢是該應用程式所專屬。

## <a name="setting-up"></a>設定

### <a name="get-your-language-understanding-luis-runtime-key"></a>取得您的 Language Understanding (LUIS) 執行階段金鑰

藉由建立 LUIS 執行階段資源，取得您的[執行階段金鑰](../luis-how-to-azure-subscription.md)。 請保留您的金鑰和金鑰端點，以進行下一個步驟。

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-javascript-nodejs-file"></a>建立新的 JavaScript (Node.js) 檔案

在您慣用的編輯器或 IDE 中，建立名為 `luis_prediction.js` 的新 JavaScript 檔案。

### <a name="install-the-npm-library-for-the-luis-runtime"></a>安裝用於 LUIS 執行階段的 NPM 程式庫

在應用程式目錄中，使用下列命令來安裝相依性：

```console
npm install @azure/cognitiveservices-luis-runtime @azure/ms-rest-js
```

## <a name="object-model"></a>物件模型

Language Understanding (LUIS) 撰寫用戶端是向 Azure 進行驗證的 [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest)物件，其中包含您的撰寫金鑰。

建立用戶端之後，請使用此用戶端來存取功能，包括：

* 依 `staging` 或 `production` 位置進行[預測](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)
* [依版本進行預測](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用 Language Understanding (LUIS) 預測執行階段用戶端程式庫來執行下列動作：

* [依位置進行預測](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>新增相依性

從專案目錄，在慣用的編輯器或 IDE 中開啟 `luis_prediction.js` 檔案。 新增下列相依性：

[!code-javascript [Dependencies](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Dependencies)]

## <a name="authenticate-the-client"></a>驗證用戶端

1. 為您自己的必要 LUIS 資訊建立變數：

    新增變數以管理從名為 `LUIS_RUNTIME_KEY` 的環境變數中提取的預測金鑰。 如果您在應用程式啟動後才建立環境變數，則執行該應用程式的編輯器、IDE 或殼層必須先關閉再重新載入後，才能存取該變數。 方法會於稍後建立。

    建立用來保存資源名稱的變數 `LUIS_RUNTIME_ENDPOINT`。

    [!code-javascript [Azure resource variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Variables)]

1. 建立應用程式識別碼的變數，作為名為 `LUIS_APP_ID` 的環境變數。 將環境變數設定為公用 IoT 應用程式 **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** 。 建立變數以設定 `production` 發佈的位置。

    [!code-javascript [LUIS app variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=OtherVariables)]


1. 使用您的金鑰建立 msRest.ApiKeyCredentials 物件，並使用該物件與您的端點建立 [LUIS.LUISRuntimeClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) 物件。

    [!code-javascript [LUIS Runtime client is required to access predictions for LUIS apps](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=AuthoringCreateClient)]

## <a name="get-prediction-from-runtime"></a>從執行階段取得預測

新增下列方法，以建立對預測執行階段的要求。

使用者語句是 [predictionRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest) 物件的一部分。

**[luisRuntimeClient.prediction.getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** 方法需要數個參數，例如應用程式識別碼、位置名稱，以及用來完成要求的預測要求物件。 其他選項 (例如詳細資訊、顯示所有意圖和記錄) 是選擇性的。

[!code-javascript [LUIS prediction request and response in Node.js NPM SDK](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=predict)]

## <a name="main-code-for-the-prediction"></a>預測的主要程式碼

使用下列主要方法將變數和方法繫結在一起，以取得預測。

[!code-javascript [Main method and main call](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Main)]

## <a name="run-the-application"></a>執行應用程式

使用 `node luis_prediction.js` 命令，從您的應用程式目錄執行應用程式。

```console
node luis_prediction.js
```

預測結果會傳回 JSON 物件：

```console
{
   "query":"turn on all lights",
   "prediction":{
      "topIntent":"HomeAutomation.TurnOn",
      "intents":{
         "HomeAutomation.TurnOn":{
            "score":0.5375382
         },
         "None":{
            "score":0.08687421
         },
         "HomeAutomation.TurnOff":{
            "score":0.0207554
         }
      },
      "entities":{
         "HomeAutomation.Operation":[
            "on"
         ],
         "$instance":{
            "HomeAutomation.Operation":[
               {
                  "type":"HomeAutomation.Operation",
                  "text":"on",
                  "startIndex":5,
                  "length":2,
                  "score":0.724984169,
                  "modelTypeId":-1,
                  "modelType":"Unknown",
                  "recognitionSources":[
                     "model"
                  ]
               }
            ]
         }
      }
   }
}
```


## <a name="clean-up-resources"></a>清除資源

在完成預測後，請刪除檔案及其子目錄，以清除本快速入門中的工作。
