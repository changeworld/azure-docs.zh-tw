---
title: 在 Java 中使用 REST 呼叫取得意圖
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 1bd7a2bb6d3393aca397686a2817f1dcd5f89a38
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987753"
---
## <a name="prerequisites"></a>Prerequisites

* [JDK SE](https://aka.ms/azure-jdks) (英文) (Java 開發套件，標準版)
* [Visual Studio Code](https://code.visualstudio.com/) 或您最愛的 IDE
* 公用應用程式識別碼：`df67dcdb-c37d-46af-88e1-8b97951ca1c2`

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

## <a name="get-intent-programmatically"></a>以程式設計方式取得意圖

使用 Java 查詢[預測端點](https://aka.ms/luis-apim-v3-prediction)並取得預測結果。

1. 建立名為 `lib` 的子目錄，並在下列的 java 程式庫中複製：

    * [commons-logging-1.2.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/commons-logging-1.2.jar)
    * [httpclient-4.5.3.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpclient-4.5.3.jar)
    * [httpcore-4.4.6.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpcore-4.4.6.jar)

1. 複製下列程式碼，以在名為 `Predict.java` 的檔案中建立類別：

    ```java
    import java.io.*;
    import java.net.URI;
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.HttpClient;
    import org.apache.http.client.methods.HttpGet;
    import org.apache.http.client.utils.URIBuilder;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;

    public class Predict {

        public static void main(String[] args)
        {
            HttpClient httpclient = HttpClients.createDefault();

            try
            {

                // The ID of a public sample LUIS app that recognizes intents for turning on and off lights
                String AppId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

                // Add your prediction Runtime key
                String Key = "YOUR-KEY";

                // Add your endpoint, example is your-resource-name.api.cognitive.microsoft.com
                String Endpoint = "YOUR-ENDPOINT";

                String Utterance = "turn on all lights";

                // Begin endpoint URL string building
                URIBuilder endpointURLbuilder = new URIBuilder("https://" + Endpoint + "/luis/prediction/v3.0/apps/" + AppId + "/slots/production/predict?");

                // query string params
                endpointURLbuilder.setParameter("query", Utterance);
                endpointURLbuilder.setParameter("subscription-key", Key);
                endpointURLbuilder.setParameter("show-all-intents", "true");
                endpointURLbuilder.setParameter("verbose", "true");

                // create URL from string
                URI endpointURL = endpointURLbuilder.build();

                // create HTTP object from URL
                HttpGet request = new HttpGet(endpointURL);

                // access LUIS endpoint - analyze text
                HttpResponse response = httpclient.execute(request);

                // get response
                HttpEntity entity = response.getEntity();


                if (entity != null)
                {
                    System.out.println(EntityUtils.toString(entity));
                }
            }

            catch (Exception e)
            {
                System.out.println(e.getMessage());
            }
        }
    }
    ```

1. 將 `YOUR-KEY` 和 `YOUR-ENDPOINT` 值取代為您自己的預測**執行階段**金鑰和端點。

    |資訊|目的|
    |--|--|
    |`YOUR-KEY`|您的 32 字元預測**執行階段**金鑰。|
    |`YOUR-ENDPOINT`| 您的預測 URL 端點。 例如： `replace-with-your-resource-name.api.cognitive.microsoft.com` 。|


1. 從命令列編譯 java 程式：

    ```console
    javac -cp ":lib/*" Predict.java
    ```

1. 從命令列執行 java 程式：

    ```console
    java -cp ":lib/*" Predict
    ```

1. 檢閱預測回應，其以 JSON 格式傳回：

    ```console
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
> [使用 Java 新增表達並定型](../get-started-get-model-rest-apis.md)