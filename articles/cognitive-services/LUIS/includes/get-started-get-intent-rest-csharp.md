---
title: 在 C# 中使用 REST 呼叫取得預測
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/20/2020
ms.author: diberry
ms.openlocfilehash: 20916ff80ae52ee9fc215d87c0987900d89e590a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733294"
---
## <a name="prerequisites"></a>Prerequisites

* [.NET Core V2.2+](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)
* LUIS 應用程式識別碼 - 使用 `df67dcdb-c37d-46af-88e1-8b97951ca1c2` 的公用 IoT 應用程式識別碼。 快速入門程式碼中使用的使用者查詢是該應用程式所專屬。

## <a name="create-luis-runtime-key-for-predictions"></a>建立預測的 LUIS 執行階段金鑰

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 按一下[建立 **Language Understanding**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. 輸入執行階段金鑰的所有必要設定：

    |設定|值|
    |--|--|
    |名稱|所需的名稱 (2-64 個字元)|
    |訂用帳戶|選取適當的訂用帳戶|
    |Location|選取任何附近和可用的位置|
    |定價層|`F0` - 最低定價層|
    |資源群組|選取可用的資源群組|

1. 按一下 [建立]  ，並等待系統建立資源。 資源建立後，請瀏覽至 [資源] 頁面。
1. 收集已設定的 `endpoint` 和 `key`。

## <a name="get-intent-programmatically"></a>以程式設計方式取得意圖

使用 C# (.NET Core) 查詢[預測端點](https://aka.ms/luis-apim-v3-prediction)並取得預測結果。

1. 建立以 C# 語言為目標的新主控台應用程式，其專案和資料夾名稱為 `predict-with-rest`。

    ```console
    dotnet new console -lang C# -n predict-with-rest
    ```

1. 變更為您剛才建立的 `predict-with-rest` 目錄，並使用下列命令安裝必要的相依性：

    ```console
    cd predict-with-rest
    dotnet add package System.Net.Http
    ```

1. 在您慣用的 IDE 或編輯器中開啟 `Program.cs`。 然後使用下列程式碼覆寫 `Program.cs`：

   ```csharp
    using System;
    using System.Net.Http;
    using System.Web;

    namespace predict_with_rest
    {
        class Program
        {
            static void Main(string[] args)
            {
                // YOUR-KEY: 32 character key
                var key = "YOUR-KEY";

                // YOUR-ENDPOINT: example is your-resource-name.api.cognitive.microsoft.com
                var endpoint = "YOUR-ENDPOINT";

                // //public sample app
                var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

                var utterance = "turn on all lights";

                MakeRequest(key, endpoint, appId, utterance);

                Console.WriteLine("Hit ENTER to exit...");
                Console.ReadLine();
            }
            static async void MakeRequest(string key, string endpoint, string appId, string utterance)
            {
                var client = new HttpClient();
                var queryString = HttpUtility.ParseQueryString(string.Empty);

                // The request header contains your subscription key
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

                // The "q" parameter contains the utterance to send to LUIS
                queryString["query"] = utterance;

                // These optional request parameters are set to their default values
                queryString["verbose"] = "true";
                queryString["show-all-intents"] = "true";
                queryString["staging"] = "false";
                queryString["timezoneOffset"] = "0";

                var endpointUri = String.Format("https://{0}/luis/prediction/v3.0/apps/{1}/slots/production/predict?query={2}", endpoint, appId, queryString);

                var response = await client.GetAsync(endpointUri);

                var strResponseContent = await response.Content.ReadAsStringAsync();

                // Display the JSON result from LUIS
                Console.WriteLine(strResponseContent.ToString());
            }
        }
    }

   ```

1. 將 `YOUR-KEY` 和 `YOUR-ENDPOINT` 值取代為您自己的預測金鑰和端點。

    |資訊|目的|
    |--|--|
    |`YOUR-KEY`|您的 32 字元預測金鑰。|
    |`YOUR-ENDPOINT`| 您的預測 URL 端點。 例如： `replace-with-your-resource-name.api.cognitive.microsoft.com` 。|

1. 使用以下命令建置主控台應用程式：

    ```console
    dotnet build
    ```

1. 執行主控台應用程式。 主控台輸出會顯示您稍早在瀏覽器視窗中看到的相同 JSON。

    ```console
    dotnet run
    ```

1. 檢閱預測回應，其以 JSON 格式傳回：

    ```console
    Hit ENTER to exit...
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    針對可讀性格式化的 JSON 回應：

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