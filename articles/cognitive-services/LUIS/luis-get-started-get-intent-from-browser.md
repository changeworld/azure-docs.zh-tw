---
title: 快速入門：使用瀏覽器取得意圖 - LUIS
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，使用可用的公用 LUIS 應用程式，從瀏覽器中的交談文字判斷使用者的意圖。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 10/17/2019
ms.author: diberry
ms.openlocfilehash: 2ca0f9faf1623df9212072abbc960cba41a6414b
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2020
ms.locfileid: "76165751"
---
# <a name="quickstart-get-intent-with-a-browser"></a>快速入門：使用瀏覽器取得意圖

若要了解 LUIS 預測端點所傳回的內容，請在網頁瀏覽器中檢視預測結果。 

## <a name="prerequisites"></a>Prerequisites

若要查詢公用應用程式，您需要：

* 您自己的 Language Understanding (LUIS) 撰寫或預測金鑰，其可從 [LUIS 入口網站 (預覽)](https://preview.luis.ai/) 取得。 若您還沒有訂用帳戶可建立金鑰，您可以註冊[免費帳戶](https://azure.microsoft.com/free/)。 
* 公用應用程式的識別碼：`df67dcdb-c37d-46af-88e1-8b97951ca1c2`。 

## <a name="use-the-browser-to-see-predictions"></a>使用瀏覽器查看預測

1. 請開啟網頁瀏覽器。 
1. 使用下列完整的 URL，將 `YOUR-KEY` 取代為您自己的 LUIS 撰寫或預測金鑰。 要求是 GET 要求，包含以您的 LUIS 撰寫或預測金鑰作為查詢字串參數的授權。

    #### <a name="v3-prediction-requesttabv3-1-1"></a>[V3 預測要求](#tab/V3-1-1)
    
    
    **GET** 端點 (依位置) 要求的 V3 URL 格式為：
    
    `
    https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY
    `

    #### <a name="v2-prediction-requesttabv2-1-2"></a>[V2 預測要求](#tab/V2-1-2)
    
    **GET** 端點要求的 V2 URL 格式為：
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-KEY&q=turn on all lights
    `

1. 將 URL 貼入瀏覽器視窗，然後按 Enter。 瀏覽器會顯示 JSON 結果，指出 LUIS 偵測到 `HomeAutomation.TurnOn` 意圖 (最高意圖) 和 `HomeAutomation.Operation` 實體 (值為 `on`)。

    #### <a name="v3-prediction-responsetabv3-2-1"></a>[V3 預測回應](#tab/V3-2-1)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-responsetabv2-2-2"></a>[V2 預測回應](#tab/V2-2-2)

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    * * *

1. 若要查看所有意圖，請新增適當的查詢字串參數。 

    #### <a name="v3-prediction-endpointtabv3-3-1"></a>[V3 預測端點](#tab/V3-3-1)

    將 `show-all-intents=true` 新增至查詢字串結尾處，以**顯示所有意圖**：

    `
    https://westus.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-endpointtabv2"></a>[V2 預測端點](#tab/V2)

    將 `verbose=true` 新增至查詢字串結尾處，以**顯示所有意圖**：

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key={your-key}&verbose=true
    `

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "intents": [
            {
                "intent": "HomeAutomation.TurnOn",
                "score": 0.5375382
            },
            {
                "intent": "None",
                "score": 0.08687421
            },
            {
                "intent": "HomeAutomation.TurnOff",
                "score": 0.0207554
            }
        ],
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```


<!-- FIX - is the public app getting updated for the new prebuilt domain with entities? -->   

## <a name="next-steps"></a>後續步驟

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

> [!div class="nextstepaction"]
> [在 LUIS 入口網站中建立應用程式](get-started-portal-build-app.md)
