---
title: 快速入門：電腦視覺 2.0 和 2.1 - 擷取印刷和手寫文字 - REST、Java
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將搭配使用電腦視覺 API 與 Java 來擷取影像中的印刷和手寫文字。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 15c84b0c4cd4311300b951c3bf86b2bd62d48bfd
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "77566126"
---
# <a name="quickstart-extract-printed-and-handwritten-text-using-the-computer-vision-20-and-21-rest-api-and-java"></a>快速入門：使用電腦視覺 2.0 和 2.1 REST API 和 Java 擷取印刷和手寫文字

在本快速入門中，您將使用電腦視覺 REST API 來擷取影像中的印刷和/或手寫文字。 您可以使用[批次讀取](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)與[讀取作業結果](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d)方法偵測影像中的文字，然後將辨識出的字元擷取到電腦可讀取的字元資料流中。 API 將決定要針對每一行文字使用哪一種辨識模型，因此它支援同時含有印刷和手寫文字的影像。

相較於電腦視覺 2.0 和 2.1，電腦視覺 3.0 公開預覽版可提供：

* 更好的精確度
* 已變更的輸出格式
* 字組的信賴分數
* 支援具有其他語言參數的西班牙文和英文

#### <a name="version-2"></a>[第 2 版](#tab/version-2)

> [!IMPORTANT]
> [批次讀取](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)方法會以非同步方式執行。 這個方法不會在成功回應的主體中傳回任何資訊。 「批次讀取」方法會改為在 `Operation-Location` 回應標頭欄位的值中傳回 URI。 您接著可以呼叫這個 URI (它代表[讀取作業結果](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d) API)，檢查狀態並傳回「辨識文字」方法呼叫的結果。

#### <a name="version-3-public-preview"></a>[第 3 版 (公開預覽版)](#tab/version-3)

> [!IMPORTANT]
> [批次讀取](https://westus2.dev.cognitive.microsoft.com/docs/services/5d98695995feb7853f67d6a6/operations/5d986960601faab4bf452005)方法會以非同步方式執行。 這個方法不會在成功回應的主體中傳回任何資訊。 「批次讀取」方法會改為在 `Operation-Location` 回應標頭欄位的值中傳回 URI。 您接著可以呼叫這個 URI (它代表[讀取作業結果](https://westus2.dev.cognitive.microsoft.com/docs/services/5d98695995feb7853f67d6a6/operations/5d9869604be85dee480c8750) API)，檢查狀態並傳回「辨識文字」方法呼叫的結果。

---

## <a name="prerequisites"></a>Prerequisites

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services)。

- 您必須已安裝 [Java&trade; Platform, Standard Edition Development Kit 7 或 8](https://aka.ms/azure-jdks) (JDK 7 或 8)。
- 您必須有電腦視覺的訂用帳戶金鑰。 您可以從[試用認知服務](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)取得免費試用的金鑰。 或者，依照[建立認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的指示訂閱電腦視覺並取得金鑰。 然後，分別為名為 `COMPUTER_VISION_SUBSCRIPTION_KEY` 和 `COMPUTER_VISION_ENDPOINT` 的金鑰和服務端點字串[建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。

## <a name="create-and-run-the-sample-application"></a>建立並執行範例應用程式

#### <a name="version-2"></a>[第 2 版](#tab/version-2)

若要建立並執行範例，請執行下列步驟：

1. 在您最愛的 IDE 或編輯器中建立新的 Java 專案。 如果此選項可用，請從命令列應用程式範本建立 Java 專案。
1. 將下列程式庫匯入您的 Java 專案中。 如果您使用 Maven，則會提供每個程式庫的 Maven 座標。
   - [Apache HTTP 用戶端](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.5)
   - [Apache HTTP 核心](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.9)
   - [JSON 程式庫](https://github.com/stleary/JSON-java) (org.json:json:20180130)
1. 將下列 `import` 陳述式新增到包含適用於您應用程式之公用類別的 `Main` 檔案。  

   ```java
   import java.net.URI;
   import org.apache.http.HttpEntity;
   import org.apache.http.HttpResponse;
   import org.apache.http.client.methods.HttpGet;
   import org.apache.http.client.methods.HttpPost;
   import org.apache.http.client.utils.URIBuilder;
   import org.apache.http.entity.StringEntity;
   import org.apache.http.impl.client.CloseableHttpClient;
   import org.apache.http.impl.client.HttpClientBuilder;
   import org.apache.http.util.EntityUtils;
   import org.apache.http.Header;
   import org.json.JSONObject;
   ```

1. 將 `Main` 公用類別取代為下列程式碼。
1. (選擇性) 將 `imageToAnalyze` 的值取代為您要從中擷取文字之不同影像的 URL。
1. 儲存並建置 Java 專案。
1. 如果您使用 IDE，請執行 `Main`。 否則，請開啟命令提示字元視窗，然後使用 `java` 命令執行已編譯的類別。 例如： `java Main` 。

```java
public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Add your Computer Vision subscription key and endpoint to your environment variables.
    // After setting, close and then re-open your command shell or project for the changes to take effect.
    String subscriptionKey = System.getenv("COMPUTER_VISION_SUBSCRIPTION_KEY");
    String endpoint = ("COMPUTER_VISION_ENDPOINT");

    private static final String uriBase = endpoint + 
            "vision/v2.1/read/core/asyncBatchAnalyze";

    private static final String imageToAnalyze =
        "https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/" +
        "Cursive_Writing_on_Notebook_paper.jpg/800px-Cursive_Writing_on_Notebook_paper.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpTextClient = HttpClientBuilder.create().build();
        CloseableHttpClient httpResultClient = HttpClientBuilder.create().build();;

        try {
            // This operation requires two REST API calls. One to submit the image
            // for processing, the other to retrieve the text found in the image.

            URIBuilder builder = new URIBuilder(uriBase);

            // Prepare the URI for the REST API method.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity =
                    new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Two REST API methods are required to extract text.
            // One method to submit the image for processing, the other method
            // to retrieve the text found in the image.

            // Call the first REST API method to detect the text.
            HttpResponse response = httpTextClient.execute(request);

            // Check for success.
            if (response.getStatusLine().getStatusCode() != 202) {
                // Format and display the JSON error message.
                HttpEntity entity = response.getEntity();
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("Error:\n");
                System.out.println(json.toString(2));
                return;
            }

            // Store the URI of the second REST API method.
            // This URI is where you can get the results of the first REST API method.
            String operationLocation = null;

            // The 'Operation-Location' response header value contains the URI for
            // the second REST API method.
            Header[] responseHeaders = response.getAllHeaders();
            for (Header header : responseHeaders) {
                if (header.getName().equals("Operation-Location")) {
                    operationLocation = header.getValue();
                    break;
                }
            }

            if (operationLocation == null) {
                System.out.println("\nError retrieving Operation-Location.\nExiting.");
                System.exit(1);
            }

            // If the first REST API method completes successfully, the second
            // REST API method retrieves the text written in the image.
            //
            // Note: The response may not be immediately available. Text
            // recognition is an asynchronous operation that can take a variable
            // amount of time depending on the length of the text.
            // You may need to wait or retry this operation.

            System.out.println("\nText submitted.\n" +
                    "Waiting 10 seconds to retrieve the recognized text.\n");
            Thread.sleep(10000);

            // Call the second REST API method and get the response.
            HttpGet resultRequest = new HttpGet(operationLocation);
            resultRequest.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            HttpResponse resultResponse = httpResultClient.execute(resultRequest);
            HttpEntity responseEntity = resultResponse.getEntity();

            if (responseEntity != null) {
                // Format and display the JSON response.
                String jsonString = EntityUtils.toString(responseEntity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("Text recognition result response: \n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }
}
```

#### <a name="version-3-public-preview"></a>[第 3 版 (公開預覽版)](#tab/version-3)

若要建立並執行範例，請執行下列步驟：

1. 在您最愛的 IDE 或編輯器中建立新的 Java 專案。 如果此選項可用，請從命令列應用程式範本建立 Java 專案。
1. 將下列程式庫匯入您的 Java 專案中。 如果您使用 Maven，則會提供每個程式庫的 Maven 座標。
   - [Apache HTTP 用戶端](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.5)
   - [Apache HTTP 核心](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.9)
   - [JSON 程式庫](https://github.com/stleary/JSON-java) (org.json:json:20180130)
1. 將下列 `import` 陳述式新增到包含適用於您應用程式之公用類別的 `Main` 檔案。  

   ```java
    import java.net.URI;
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpGet;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.client.utils.URIBuilder;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClientBuilder;
    import org.apache.http.util.EntityUtils;
    import org.apache.http.Header;
    import org.json.JSONObject;
    ```

1. 將 `Main` 公用類別取代為下列程式碼。
1. (選擇性) 將 `language` 的值取代為您想要辨識的語言。 接受的值為 "en" (代表英文) 和 "es" (代表西班牙文)。
1. (選擇性) 將 `imageToAnalyze` 的值取代為您要從中擷取文字之不同影像的 URL。
1. 儲存並建置 Java 專案。
1. 如果您使用 IDE，請執行 `Main`。 否則，請開啟命令提示字元視窗，然後使用 `java` 命令執行已編譯的類別。 例如： `java Main` 。

```java

public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Add your Computer Vision subscription key and endpoint to your environment variables.
    // After setting, close and then re-open your command shell or project for the changes to take effect.
    private static String subscriptionKey = System.getenv("COMPUTER_VISION_SUBSCRIPTION_KEY");
    private static String endpoint = System.getenv("COMPUTER_VISION_ENDPOINT");

    // Set the language that you want to recognize
    private static String language = "en";  // Accepted values are "en" for English, or "es" for Spanish

    private static String uriBase = endpoint +
            "/vision/v3.0-preview/read/analyze";

    private static String imageToAnalyze =
            "https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/" +
                    "Cursive_Writing_on_Notebook_paper.jpg/800px-Cursive_Writing_on_Notebook_paper.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpTextClient = HttpClientBuilder.create().build();
        CloseableHttpClient httpResultClient = HttpClientBuilder.create().build();;

        System.out.println("Endpoint:         " + endpoint);
        System.out.println("Subscription key: " + subscriptionKey);
        System.out.println("Language:         " + language);

        try {
            // This operation requires two REST API calls. One to submit the image
            // for processing, the other to retrieve the text found in the image.

            URIBuilder builder = new URIBuilder(uriBase);
            builder.setParameter("language", language);

            // Prepare the URI for the REST API method.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity =
                    new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Two REST API methods are required to extract text.
            // One method to submit the image for processing, the other method
            // to retrieve the text found in the image.

            // Call the first REST API method to detect the text.
            HttpResponse response = httpTextClient.execute(request);

            // Check for success.
            if (response.getStatusLine().getStatusCode() != 202) {
                // Format and display the JSON error message.
                HttpEntity entity = response.getEntity();
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("Error:\n");
                System.out.println(json.toString(2));
                return;
            }

            // Store the URI of the second REST API method.
            // This URI is where you can get the results of the first REST API method.
            String operationLocation = null;

            // The 'Operation-Location' response header value contains the URI for
            // the second REST API method.
            Header[] responseHeaders = response.getAllHeaders();
            for (Header header : responseHeaders) {
                if (header.getName().equals("Operation-Location")) {
                    operationLocation = header.getValue();
                    break;
                }
            }

            if (operationLocation == null) {
                System.out.println("\nError retrieving Operation-Location.\nExiting.");
                System.exit(1);
            }

            // If the first REST API method completes successfully, the second
            // REST API method retrieves the text written in the image.
            //
            // Note: The response may not be immediately available. Text
            // recognition is an asynchronous operation that can take a variable
            // amount of time depending on the length of the text.
            // You may need to wait or retry this operation.

            System.out.println("\nText submitted.\n" +
                    "Waiting 10 seconds to retrieve the recognized text.\n");
            Thread.sleep(10000);

            // Call the second REST API method and get the response.
            HttpGet resultRequest = new HttpGet(operationLocation);
            resultRequest.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            HttpResponse resultResponse = httpResultClient.execute(resultRequest);
            HttpEntity responseEntity = resultResponse.getEntity();

            if (responseEntity != null) {
                // Format and display the JSON response.
                String jsonString = EntityUtils.toString(responseEntity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("Text recognition result response: \n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }
}
```

---

## <a name="examine-the-response"></a>檢查回應

成功的回應會以 JSON 的形式傳回。 範例應用程式會在主控台視窗中剖析並顯示成功的回應，如下列範例所示：

#### <a name="version-2"></a>[第 2 版](#tab/version-2)

```json
Text submitted. Waiting 10 seconds to retrieve the recognized text.

Text recognition result response:

{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 349.59,
      "width": 3200,
      "height": 3200,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [202,618,2047,643,2046,840,200,813],
          "text": "Our greatest glory is not",
          "words": [
            {
              "boundingBox": [204,627,481,628,481,830,204,829],
              "text": "Our"
            },
            {
              "boundingBox": [519,628,1057,630,1057,832,518,830],
              "text": "greatest"
            },
            {
              "boundingBox": [1114,630,1549,631,1548,833,1114,832],
              "text": "glory"
            },
            {
              "boundingBox": [1586,631,1785,632,1784,834,1586,833],
              "text": "is"
            },
            {
              "boundingBox": [1822,632,2115,633,2115,835,1822,834],
              "text": "not"
            }
          ]
        },
        {
          "boundingBox": [420,1273,2954,1250,2958,1488,422,1511],
          "text": "but in rising every time we fall",
          "words": [
            {
              "boundingBox": [423,1269,634,1268,635,1507,424,1508],
              "text": "but"
            },
            {
              "boundingBox": [667,1268,808,1268,809,1506,668,1507],
              "text": "in"
            },
            {
              "boundingBox": [874,1267,1289,1265,1290,1504,875,1506],
              "text": "rising"
            },
            {
              "boundingBox": [1331,1265,1771,1263,1772,1502,1332,1504],
              "text": "every"
            },
            {
              "boundingBox": [1812, 1263, 2178, 1261, 2179, 1500, 1813, 1502],
              "text": "time"
            },
            {
              "boundingBox": [2219, 1261, 2510, 1260, 2511, 1498, 2220, 1500],
              "text": "we"
            },
            {
              "boundingBox": [2551, 1260, 3016, 1258, 3017, 1496, 2552, 1498],
              "text": "fall"
            }
          ]
        },
        {
          "boundingBox": [1612, 903, 2744, 935, 2738, 1139, 1607, 1107],
          "text": "in never failing ,",
          "words": [
            {
              "boundingBox": [1611, 934, 1707, 933, 1708, 1147, 1613, 1147],
              "text": "in"
            },
            {
              "boundingBox": [1753, 933, 2132, 930, 2133, 1144, 1754, 1146],
              "text": "never"
            },
            {
              "boundingBox": [2162, 930, 2673, 927, 2674, 1140, 2164, 1144],
              "text": "failing"
            },
            {
              "boundingBox": [2703, 926, 2788, 926, 2790, 1139, 2705, 1140],
              "text": ",",
              "confidence": "Low"
            }
          ]
        }
      ]
    }
  ]
}
```

#### <a name="version-3-public-preview"></a>[第 3 版 (公開預覽版)](#tab/version-3)

```json
{
  "analyzeResult": {
    "readResults": [{
      "unit": "pixel",
      "width": 800,
      "angle": 0.8206,
      "language": "en",
      "page": 1,
      "lines": [
        {
          "boundingBox": [
            6,
            4,
            774,
            14,
            773,
            61,
            5,
            49
          ],
          "words": [
            {
              "boundingBox": [
                14,
                5,
                76,
                6,
                74,
                49,
                12,
                48
              ],
              "confidence": 0.83,
              "text": "The"
            },
            {
              "boundingBox": [
                84,
                6,
                182,
                7,
                180,
                51,
                82,
                49
              ],
              "confidence": 0.762,
              "text": "quick"
            },
            {
              "boundingBox": [
                191,
                7,
                312,
                9,
                309,
                54,
                189,
                51
              ],
              "confidence": 0.67,
              "text": "brown"
            },
            {
              "boundingBox": [
                320,
                9,
                382,
                10,
                379,
                55,
                317,
                54
              ],
              "confidence": 0.849,
              "text": "fox"
            },
            {
              "boundingBox": [
                390,
                10,
                497,
                11,
                493,
                57,
                387,
                55
              ],
              "confidence": 0.703,
              "text": "jumps"
            },
            {
              "boundingBox": [
                506,
                11,
                596,
                12,
                591,
                59,
                502,
                57
              ],
              "confidence": 0.799,
              "text": "over"
            },
            {
              "boundingBox": [
                604,
                12,
                666,
                13,
                661,
                60,
                600,
                59
              ],
              "confidence": 0.923,
              "text": "the"
            },
            {
              "boundingBox": [
                674,
                13,
                773,
                14,
                768,
                62,
                670,
                60
              ],
              "confidence": 0.863,
              "text": "lazy"
            }
          ],
          "language": "en",
          "text": "The quick brown fox jumps over the lazy"
        },
        {
          "boundingBox": [
            5,
            53,
            79,
            56,
            77,
            95,
            4,
            92
          ],
          "words": [{
            "boundingBox": [
              6,
              53,
              74,
              56,
              72,
              95,
              5,
              92
            ],
            "confidence": 0.418,
            "text": "dog"
          }],
          "language": "en",
          "text": "dog"
        },
        {
          "boundingBox": [
            0,
            90,
            787,
            95,
            787,
            145,
            0,
            136
          ],
          "words": [
            {
              "boundingBox": [
                1,
                96,
                79,
                93,
                79,
                135,
                0,
                136
              ],
              "confidence": 0.835,
              "text": "Pack"
            },
            {
              "boundingBox": [
                87,
                93,
                151,
                92,
                151,
                135,
                87,
                135
              ],
              "confidence": 0.88,
              "text": "my"
            },
            {
              "boundingBox": [
                162,
                92,
                226,
                91,
                225,
                135,
                161,
                135
              ],
              "confidence": 0.301,
              "text": "box"
            },
            {
              "boundingBox": [
                234,
                91,
                335,
                90,
                335,
                135,
                233,
                135
              ],
              "confidence": 0.959,
              "text": "with"
            },
            {
              "boundingBox": [
                346,
                91,
                418,
                91,
                417,
                136,
                345,
                135
              ],
              "confidence": 0.489,
              "text": "five"
            },
            {
              "boundingBox": [
                426,
                91,
                527,
                93,
                527,
                138,
                425,
                136
              ],
              "confidence": 0.727,
              "text": "dozen"
            },
            {
              "boundingBox": [
                554,
                94,
                687,
                98,
                687,
                143,
                553,
                139
              ],
              "confidence": 0.377,
              "text": "liquor"
            },
            {
              "boundingBox": [
                701,
                99,
                787,
                103,
                787,
                146,
                700,
                143
              ],
              "confidence": 0.693,
              "text": "jugs"
            }
          ],
          "language": "en",
          "text": "Pack my box with five dozen liquor jugs"
        }
      ],
      "height": 154
    }],
    "version": "3.0.0"
  },
  "createdDateTime": "2020-02-11T21:21:14Z",
  "lastUpdatedDateTime": "2020-02-11T21:21:19Z",
  "status": "succeeded"
}
```

---

## <a name="clean-up-resources"></a>清除資源

不再需要 Java 專案時請將它刪除，包括已編譯的類別與匯入的程式庫。

## <a name="next-steps"></a>後續步驟

探索使用「電腦視覺」在影像中執行光學字元辨識 (OCR)、建立智慧型裁剪縮圖以及偵測、分類、標記和描述視覺特徵 (包括臉部) 的 Java Swing 應用程式。 若要快速地試驗電腦視覺 API，請嘗試 [Open API 測試主控台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)。

> [!div class="nextstepaction"]
> [電腦視覺 API Java 教學課程](../Tutorials/java-tutorial.md)
