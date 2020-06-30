---
title: 快速入門：使用 REST API 取得意圖 - LUIS
titleSuffix: Azure Cognitive Services
description: 在本 REST API 快速入門中，使用可用的公用 LUIS 應用程式，從交談文字判斷使用者的用意。
services: cognitive-services
author: roy-har
manager: diberry
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 06/19/2020
ms.author: v-royha
ms.openlocfilehash: 8248d441add1af39ab9d2d08a6dba85055f254b6
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2020
ms.locfileid: "85119571"
---
# <a name="quickstart-use-curl-and-rest-to-get-intent"></a>快速入門：使用 cURL 和 REST 來取得意圖

本快速入門會示範如何使用 LUIS 應用程式，從交談文字中判斷使用者的意圖。 您將使用 cURL 以文字形式將使用者的意圖傳送至披薩應用程式的 HTTP 預測端點。 LUIS 會在端點套用披薩應用程式的模型來分析自然語言文字的意義、判斷整體意圖，並擷取與應用程式主體網域相關的資料。

[參考文件](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08)

## <a name="prerequisites"></a>必要條件

* 免費的 [LUIS](https://www.luis.ai) 帳戶。
* 文字編輯器，例如 [Visual Studio Code](https://code.visualstudio.com/)。
* 命令列程式 cURL。 cURL 程式已安裝在 macOS、大部分的 Linux 散發套件，以及 Windows 10 組建 1803 和更新版本上。 如果您需要安裝 cURL，您可以從 [cURL 下載頁面](https://curl.haxx.se/download.html)下載 cURL。

## <a name="create-pizza-app"></a>建立披薩應用程式

[!INCLUDE [Create pizza app](includes/get-started-get-model-create-pizza-app.md)]

## <a name="get-intent-from-the-prediction-endpoint"></a>從預測端點取得意圖

使用 cURL 查詢[預測端點](https://aka.ms/luis-apim-v3-prediction)並取得預測結果。

1. 將此命令複製到文字編輯器中：

    #### <a name="windows"></a>[Windows](#tab/windows)
    ```cURL
    curl "https://***YOUR-PREDICTION-ENDPOINT***/luis/prediction/v3.0/apps/***YOUR-APP-ID***/slots/production/predict" ^
          --request GET ^
          --get ^
          --data "subscription-key=***YOUR-PREDICTION-KEY***" ^
          --data "verbose=true" ^
          --data "show-all-intents=true" ^
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "https://***YOUR-PREDICTION-ENDPOINT***/luis/prediction/v3.0/apps/***YOUR-APP-ID***/slots/production/predict" \
          --request GET \
          --get \
          --data "subscription-key=***YOUR-PREDICTION-KEY***" \
          --data "verbose=true" \
          --data "show-all-intents=true" \
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "https://***YOUR-PREDICTION-ENDPOINT***/luis/prediction/v3.0/apps/***YOUR-APP-ID***/slots/production/predict" \
          --request GET \
          --get \
          --data "subscription-key=***YOUR-PREDICTION-KEY***" \
          --data "verbose=true" \
          --data "show-all-intents=true" \
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    ***

1. 使用您自己的值取代以 `***YOUR-` 開頭的值。

    |資訊|目的|
    |--|--|
    |`***YOUR-PREDICTION-ENDPOINT***`| 您的預測 URL 端點。 位於您應用程式的 LUIS 入口網站 [Azure 資源] 頁面上。<br>例如： `https://westus.api.cognitive.microsoft.com/` 。|
    |`***YOUR-APP-ID***`|您的應用程式識別碼。 位於您應用程式的 LUIS 入口網站 [應用程式設定] 頁面上。
    |`***YOUR-PREDICTION-KEY***`|您的 32 字元預測金鑰。 位於您應用程式的 LUIS 入口網站 [Azure 資源] 頁面上。

1. 將文字複製到主控台視窗，然後按 Enter 鍵來執行命令：

1. 使用以下主控台命令執行您的指令碼：

    ```console
    python predict.py
    ```

1. 檢閱預測回應，其以 JSON 格式傳回：

    ```console
    {"query":"I want two large pepperoni pizzas on thin crust please","prediction":{"topIntent":"ModifyOrder","intents":{"ModifyOrder":{"score":1.0},"None":{"score":8.55E-09},"Greetings":{"score":1.82222226E-09},"CancelOrder":{"score":1.47272727E-09},"Confirmation":{"score":9.8125E-10}},"entities":{"Order":[{"FullPizzaWithModifiers":[{"PizzaType":["pepperoni pizzas"],"Size":[["Large"]],"Quantity":[2],"Crust":[["Thin"]],"$instance":{"PizzaType":[{"type":"PizzaType","text":"pepperoni pizzas","startIndex":17,"length":16,"score":0.9978157,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Size":[{"type":"SizeList","text":"large","startIndex":11,"length":5,"score":0.9984481,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Quantity":[{"type":"builtin.number","text":"two","startIndex":7,"length":3,"score":0.999770939,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Crust":[{"type":"CrustList","text":"thin crust","startIndex":37,"length":10,"score":0.933985531,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}]}}],"$instance":{"FullPizzaWithModifiers":[{"type":"FullPizzaWithModifiers","text":"two large pepperoni pizzas on thin crust","startIndex":7,"length":40,"score":0.90681237,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}]}}],"ToppingList":[["Pepperoni"]],"$instance":{"Order":[{"type":"Order","text":"two large pepperoni pizzas on thin crust","startIndex":7,"length":40,"score":0.9047088,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"ToppingList":[{"type":"ToppingList","text":"pepperoni","startIndex":17,"length":9,"modelTypeId":5,"modelType":"List Entity Extractor","recognitionSources":["model"]}]}}}}
    ```

    針對可讀性格式化的 JSON 回應：

    ```JSON
    {
      "query": "I want two large pepperoni pizzas on thin crust please",
      "prediction": {
        "topIntent": "ModifyOrder",
        "intents": {
          "ModifyOrder": {
            "score": 1.0
          },
          "None": {
            "score": 8.55e-9
          },
          "Greetings": {
            "score": 1.82222226e-9
          },
          "CancelOrder": {
            "score": 1.47272727e-9
          },
          "Confirmation": {
            "score": 9.8125e-10
          }
        },
        "entities": {
          "Order": [
            {
              "FullPizzaWithModifiers": [
                {
                  "PizzaType": [
                    "pepperoni pizzas"
                  ],
                  "Size": [
                    [
                      "Large"
                    ]
                  ],
                  "Quantity": [
                    2
                  ],
                  "Crust": [
                    [
                      "Thin"
                    ]
                  ],
                  "$instance": {
                    "PizzaType": [
                      {
                        "type": "PizzaType",
                        "text": "pepperoni pizzas",
                        "startIndex": 17,
                        "length": 16,
                        "score": 0.9978157,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Size": [
                      {
                        "type": "SizeList",
                        "text": "large",
                        "startIndex": 11,
                        "length": 5,
                        "score": 0.9984481,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Quantity": [
                      {
                        "type": "builtin.number",
                        "text": "two",
                        "startIndex": 7,
                        "length": 3,
                        "score": 0.999770939,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Crust": [
                      {
                        "type": "CrustList",
                        "text": "thin crust",
                        "startIndex": 37,
                        "length": 10,
                        "score": 0.933985531,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ]
                  }
                }
              ],
              "$instance": {
                "FullPizzaWithModifiers": [
                  {
                    "type": "FullPizzaWithModifiers",
                    "text": "two large pepperoni pizzas on thin crust",
                    "startIndex": 7,
                    "length": 40,
                    "score": 0.90681237,
                    "modelTypeId": 1,
                    "modelType": "Entity Extractor",
                    "recognitionSources": [
                      "model"
                    ]
                  }
                ]
              }
            }
          ],
          "ToppingList": [
            [
              "Pepperoni"
            ]
          ],
          "$instance": {
            "Order": [
              {
                "type": "Order",
                "text": "two large pepperoni pizzas on thin crust",
                "startIndex": 7,
                "length": 40,
                "score": 0.9047088,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                  "model"
                ]
              }
            ],
            "ToppingList": [
              {
                "type": "ToppingList",
                "text": "pepperoni",
                "startIndex": 17,
                "length": 9,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                  "model"
                ]
              }
            ]
          }
        }
      }
    }
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [新增表達並定型](luis-get-started-rest-get-model.md)
