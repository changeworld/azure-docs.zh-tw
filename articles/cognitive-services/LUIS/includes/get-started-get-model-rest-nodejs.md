---
title: 在 C# 中使用 REST 呼叫取得模型
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/18/2020
ms.author: diberry
ms.openlocfilehash: 19f72dbb62fc2084bf0c9609fb3782e083c911af
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655551"
---
## <a name="prerequisites"></a>Prerequisites

* Azure Language Understanding - 撰寫資源的 32 字元金鑰和撰寫端點 URL。 使用 [Azure 入口網站](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)或 [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli) 建立。
* 從 `Azure-Samples/cognitive-services-sample-data-files` GitHub 存放庫匯入[披薩](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json)應用程式。
* 已匯入披薩應用程式的 LUIS 應用程式識別碼。 應用程式識別碼會顯示在應用程式儀表板中。
* 應用程式中用來接收表達的版本識別碼。
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
    npm install --save request-promise
    ```

## <a name="change-model-programmatically"></a>以程式設計方式變更模型

1. 建立名為 `model.js` 的新檔案。 新增下列程式碼：

    ```javascript
    var request = require('request-promise');

    //////////
    // Values to modify.

    // YOUR-APP-ID: The App ID GUID found on the www.luis.ai Application Settings page.
    const LUIS_appId = "YOUR-APP-ID";

    // YOUR-AUTHORING-KEY: Your LUIS authoring key, 32 character value.
    const LUIS_authoringKey = "YOUR-AUTHORING-KEY";

    // YOUR-AUTHORING-ENDPOINT: Replace this with your authoring key endpoint.
    // For example, "https://your-resource-name.api.cognitive.microsoft.com/"
    const LUIS_endpoint = "YOUR-AUTHORING-ENDPOINT";

    // NOTE: Replace this your version number. The Pizza app uses a version number of "0.1".
    const LUIS_versionId = "0.1";
    //////////

    const addUtterancesURI = `${LUIS_endpoint}luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/examples`;
    const addTrainURI = `${LUIS_endpoint}luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/train`;

    const utterances = [
        {
            'text': 'order a pizza',
            'intentName': 'ModifyOrder',
            'entityLabels': [
                {
                    'entityName': 'Order',
                    'startCharIndex': 6,
                    'endCharIndex': 12
                }
            ]
        },
        {
            'text': 'order a large pepperoni pizza',
            'intentName': 'ModifyOrder',
            'entityLabels': [
                {
                    'entityName': 'Order',
                    'startCharIndex': 6,
                    'endCharIndex': 28
                },
                {
                    'entityName': 'FullPizzaWithModifiers',
                    'startCharIndex': 6,
                    'endCharIndex': 28
                },
                {
                    'entityName': 'PizzaType',
                    'startCharIndex': 14,
                    'endCharIndex': 28
                },
                {
                    'entityName': 'Size',
                    'startCharIndex': 8,
                    'endCharIndex': 12
                }
            ]
        },
        {
            'text': 'I want two large pepperoni pizzas on thin crust',
            'intentName': 'ModifyOrder',
            'entityLabels': [
                {
                    'entityName': 'Order',
                    'startCharIndex': 7,
                    'endCharIndex': 46
                },
                {
                    'entityName': 'FullPizzaWithModifiers',
                    'startCharIndex': 7,
                    'endCharIndex': 46
                },
                {
                    'entityName': 'PizzaType',
                    'startCharIndex': 17,
                    'endCharIndex': 32
                },
                {
                    'entityName': 'Size',
                    'startCharIndex': 11,
                    'endCharIndex': 15
                },
                {
                    'entityName': 'Quantity',
                    'startCharIndex': 7,
                    'endCharIndex': 9
                },
                {
                    'entityName': 'Crust',
                    'startCharIndex': 37,
                    'endCharIndex': 46
                }
            ]
        }
    ];

    // Main function.
    const main = async() =>{

        await addUtterances(utterances);
        await train("POST");
        await train("GET");

    }

    // Adds the utterances to the model.
    const addUtterances = async (utterances) => {

        const options = {
            uri: addUtterancesURI,
            method: 'POST',
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: utterances
        };

        const response = await request(options)
        console.log("addUtterance:\n" + JSON.stringify(response, null, 2));
    }

    // With verb === "POST", sends a training request.
    // With verb === "GET", obtains the training status.
    const train = async (verb) => {

        const options = {
            uri: addTrainURI,
            method: verb,
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: null // The body can be empty for a training request
        };

        const response = await request(options)
        console.log("train " + verb + ":\n" + JSON.stringify(response, null, 2));
    }

    // MAIN
    main().then(() => console.log("done")).catch((err)=> console.log(err));
    ```

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

## <a name="clean-up-resources"></a>清除資源

完成本快速入門時，請從檔案系統中刪除專案資料夾。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式的最佳做法](../luis-concept-best-practices.md)