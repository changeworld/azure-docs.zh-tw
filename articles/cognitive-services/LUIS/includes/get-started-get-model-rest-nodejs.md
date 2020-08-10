---
title: 在 Node.js 中使用 REST 呼叫取得模型
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/03/2020
ms.author: diberry
ms.custom: devx-track-javascript
ms.openlocfilehash: d2dd43366068c8b0d5f280a4c5c22ac774824f59
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407248"
---
[參考文件](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45) | [範例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/node-model-with-rest/model.js)

## <a name="prerequisites"></a>必要條件

* [Node.js](https://nodejs.org/) (英文) 程式設計語言
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>範例語句 JSON 檔案

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="create-the-nodejs-project"></a>建立 Node.js 專案

1. 建立新資料夾來存放您的 Node.js 專案，例如 `node-model-with-rest`。

1. 開啟新的命令提示字元，瀏覽至您所建立的資料夾，然後執行下列命令：

    ```console
    npm init
    ```

    在每個提示字元下，按 ENTER 接受預設值。

1. 輸入下列命令來安裝 request-promise 模組：

    ```console
    npm install --save request
    npm install --save request-promise
    npm install --save querystring
    ```

## <a name="change-model-programmatically"></a>以程式設計方式變更模型

1. 建立名為 `model.js` 的新檔案。 新增下列程式碼：

    [!code-javascript[Code snippet](~/cognitive-services-quickstart-code/javascript/LUIS/node-model-with-rest/model.js)]

1. 使用您自己的值取代以 `YOUR-` 開頭的值。

    |資訊|目的|
    |--|--|
    |`YOUR-APP-ID`| 您的 LUIS 應用程式識別碼。 |
    |`YOUR-AUTHORING-KEY`|您的 32 字元撰寫金鑰。|
    |`YOUR-AUTHORING-ENDPOINT`| 您的撰寫 URL 端點。 例如： `https://replace-with-your-resource-name.api.cognitive.microsoft.com/` 。 您已在建立資源時設定資源名稱。|

    在 LUIS 入口網站中，您可以在 [Azure 資源] 頁面上的 [管理] 區段中看到指派的金鑰和端點。 在相同的 [管理] 區段中，您可以在 [應用程式設定] 頁面上取得應用程式識別碼。

1. 在命令提示字元中輸入下列命令，以執行專案：

    ```console
    node model.js
    ```

1. 檢閱撰寫回應：

    ```json
    addUtterance:
    [
      {
        "value": {
          "ExampleId": 1137150691,
          "UtteranceText": "order a pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1137150692,
          "UtteranceText": "order a large pepperoni pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1137150693,
          "UtteranceText": "i want two large pepperoni pizzas on thin crust"
        },
        "hasError": false
      }
    ]
    train POST:
    {
      "statusId": 9,
      "status": "Queued"
    }
    train GET:
    [
      {
        "modelId": "edb46abf-0000-41ab-beb2-a41a0fe1630f",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "a5030be2-616c-4648-bf2f-380fa9417d37",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "3f2b1f31-a3c3-4fbd-8182-e9d9dbc120b9",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "e4b6704b-1636-474c-9459-fe9ccbeba51c",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "031d3777-2a00-4a7a-9323-9a3280a30000",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "9250e7a1-06eb-4413-9432-ae132ed32583",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      }
    ]
    done
    ```

## <a name="clean-up-resources"></a>清除資源

完成本快速入門時，請從檔案系統中刪除專案資料夾。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式的最佳做法](../luis-concept-best-practices.md)