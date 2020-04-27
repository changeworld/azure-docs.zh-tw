---
title: 在 Python 中使用 REST 呼叫取得意圖
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/20/2020
ms.author: diberry
ms.openlocfilehash: 2307ef6ea8940a3b3ddfb8c7539f4f809dc4c52c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733337"
---
## <a name="prerequisites"></a>Prerequisites

* [Python 3.6](https://www.python.org/downloads/) 或更新版本。
* [Visual Studio Code](https://code.visualstudio.com/)
* LUIS 應用程式識別碼 - 使用 `df67dcdb-c37d-46af-88e1-8b97951ca1c2` 的公用 IoT 應用程式識別碼。 快速入門程式碼中使用的使用者查詢是該應用程式所專屬。

## <a name="create-luis-runtime-key-for-predictions"></a>建立預測的 LUIS 執行階段金鑰

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 按一下[建立 **Language Understanding**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. 輸入**執行階段**金鑰的所有必要設定：

    |設定|值|
    |--|--|
    |名稱|所需的名稱 (2-64 個字元)|
    |訂用帳戶|選取適當的訂用帳戶|
    |Location|選取任何附近和可用的位置|
    |定價層|`F0` - 最低定價層|
    |資源群組|選取可用的資源群組|

1. 按一下 [建立]  ，並等待系統建立資源。 資源建立後，請瀏覽至 [資源] 頁面。
1. 收集已設定的 `endpoint` 和 `key`。

## <a name="get-intent-from-the-prediction-endpoint"></a>從預測端點取得意圖

使用 Python 查詢[預測端點](https://aka.ms/luis-apim-v3-prediction)並取得預測結果。

1. 將以下程式碼片段複製到名為 `predict.py` 的檔案中：

    ```python
    ########### Python 3.6 #############
    import requests

    try:

        key = 'YOUR-KEY' # your Runtime key
        endpoint = 'YOUR-ENDPOINT' # such as 'your-resource-name.api.cognitive.microsoft.com'
        appId = 'df67dcdb-c37d-46af-88e1-8b97951ca1c2'
        utterance = 'turn on all lights'

        headers = {
        }

        params ={
            'query': utterance,
            'timezoneOffset': '0',
            'verbose': 'true',
            'show-all-intents': 'true',
            'spellCheck': 'false',
            'staging': 'false',
            'subscription-key': key
        }

        r = requests.get(f'https://{endpoint}/luis/prediction/v3.0/apps/{appId}/slots/production/predict',headers=headers, params=params)
        print(r.json())

    except Exception as e:
        print(f'{e}')
    ```

1. 將 `YOUR-KEY` 和 `YOUR-ENDPOINT` 值取代為您自己的預測**執行階段**金鑰和端點。

    |資訊|目的|
    |--|--|
    |`YOUR-KEY`|您的 32 字元預測**執行階段**金鑰。|
    |`YOUR-ENDPOINT`| 您的預測 URL 端點。 例如： `replace-with-your-resource-name.api.cognitive.microsoft.com` 。|

1. 安裝 `requests` 相依性。 這用來提出 HTTP 要求：

    ```console
    pip install requests
    ```

1. 使用以下主控台命令執行您的指令碼：

    ```console
    python predict.py
    ```

1. 檢閱預測回應，其以 JSON 格式傳回：

    ```console
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    以下是針對可讀性格式化的 JSON 回應：

    ```JSON
    {
        "query": "turn on all lights",
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
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
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

## <a name="clean-up-resources"></a>清除資源

您完成本快速入門時，請從檔案系統中刪除該檔案。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [新增表達並定型](../get-started-get-model-rest-apis.md)