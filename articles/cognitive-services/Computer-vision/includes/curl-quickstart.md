---
title: 快速入門：電腦視覺 REST API
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將開始使用電腦視覺 REST API。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: bfd062da44e32061e191361011c0001373488cde
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2020
ms.locfileid: "96739214"
---
使用電腦視覺 REST API 來執行下列動作：

* 分析影像中的標籤、文字描述、臉部、成人內容等等。
* 透過讀取 API 讀取印刷和手寫文字。
* 使用智慧裁剪來產生縮圖

> [!NOTE]
> 本快速入門會使用 cURL 命令來呼叫 REST API。 您也可以使用程式設計語言來呼叫 REST API。 如需 [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/REST)、[Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/ComputerVision/REST)、[Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/ComputerVision/REST)、[JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/ComputerVision/REST) 和 [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/ComputerVision/REST) 的範例，請參閱 GitHub 範例。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/) 
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="建立電腦視覺資源"  target="_blank">建立電腦視覺資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]。
  * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至 電腦視覺服務。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
  * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。
* 已安裝 [cURL](https://curl.haxx.se/)

## <a name="analyze-an-image"></a>分析影像

若要分析影像中的各種視覺特徵，請執行下列步驟：

1. 將下列命令複製到文字編輯器。
1. 視需要在命令中進行下列變更：
    1. 將 `<subscriptionKey>` 的值取代為您的訂用帳戶金鑰。
    1. 將要求 URL 的第一個部分 (`westcentralus`) 取代為您端點 URL 中的文字。
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. (選擇性) 將要求本文 (`http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\`) 中的影像 URL 變更為要分析之不同影像的 URL。
1. 開啟 [命令提示字元] 視窗。
1. 將文字編輯器中的命令貼到命令提示字元視窗中，然後執行該命令。

```bash
curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v3.1/analyze?visualFeatures=Categories,Description&details=Landmarks" -d "{\"url\":\"http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\"}"
```

### <a name="examine-the-response"></a>檢查回應

成功的回應會以 JSON 的形式傳回。 範例應用程式會在命令提示字元視窗中剖析並顯示成功的回應，如下列範例所示：

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "b6e33879-abb2-43a0-a96e-02cb5ae0b795",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="read-printed-and-handwritten-text"></a>讀取印刷和手寫文字

電腦視覺可以讀取影像中的可見文字，並將它轉換成字元資料流。 如需文字辨識的詳細資訊，請參閱[光學字元辨識 (OCR)](../concept-recognizing-text.md#read-api) 概念文件。

若要建立並執行範例，請執行下列步驟：

1. 將下列命令複製到文字編輯器。
1. 視需要在命令中進行下列變更：
    1. 將 `<subscriptionKey>` 的值取代為您的訂用帳戶金鑰。
    1. 將要求 URL 的第一個部分 (`westcentralus`) 取代為您端點 URL 中的文字。
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. (選擇性) 將要求本文 (`https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\`) 中的影像 URL 變更為要分析之不同影像的 URL。
1. 開啟 [命令提示字元] 視窗。
1. 將文字編輯器中的命令貼到命令提示字元視窗中，然後執行該命令。

```bash
curl -v -X POST "https://westcentralus.api.cognitive.microsoft.com/vision/v3.1/read/analyze?language={string}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\"}"
```

回應會包含 `Operation-Location` 標頭，其值為唯一的 URL。 您可以使用此 URL 來查詢讀取作業的結果。 URL 會在 48 小時後過期。

1. 將下列命令複製到文字編輯器。
1. 將 URL 取代為您在上一個步驟中複製的 `Operation-Location` 值。
1. 視需要在命令中進行下列變更：
    1. 將 `<subscriptionKey>` 的值取代為您的訂用帳戶金鑰。
1. 開啟 [命令提示字元] 視窗。
1. 將文字編輯器中的命令貼到命令提示字元視窗中，然後執行該命令。

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/vision/v3.1/read/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{body}" 
```

### <a name="examine-the-response"></a>檢查回應

成功的回應會以 JSON 的形式傳回。 範例應用程式會在命令提示字元視窗中剖析並顯示成功的回應，如下列範例所示：

```json
{
  "status": "succeeded",
  "createdDateTime": "2019-10-03T14:32:04.236Z",
  "lastUpdatedDateTime": "2019-10-03T14:38:14.852Z",
  "analyzeResult": {
    "version": "v3.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 49.59,
        "width": 600,
        "height": 400,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              202,
              618,
              2047,
              643,
              2046,
              840,
              200,
              813
            ],
            "text": "Our greatest glory is not",
            "words": [
              {
                "boundingBox": [
                  204,
                  627,
                  481,
                  628,
                  481,
                  830,
                  204,
                  829
                ],
                "text": "Our",
                "confidence": 0.164
              },
              {
                "boundingBox": [
                  519,
                  628,
                  1057,
                  630,
                  1057,
                  832,
                  518,
                  830
                ],
                "text": "greatest",
                "confidence": 0.164
              },
              {
                "boundingBox": [
                  1114,
                  630,
                  1549,
                  631,
                  1548,
                  833,
                  1114,
                  832
                ],
                "text": "glory",
                "confidence": 0.164
              },
              {
                "boundingBox": [
                  1586,
                  631,
                  1785,
                  632,
                  1784,
                  834,
                  1586,
                  833
                ],
                "text": "is",
                "confidence": 0.164
              },
              {
                "boundingBox": [
                  1822,
                  632,
                  2115,
                  633,
                  2115,
                  835,
                  1822,
                  834
                ],
                "text": "not",
                "confidence": 0.164
              }
            ]
          },
...
        ]
      },
      {
        "page": 2,
        "language": "en",
        "angle": 1.32,
        "width": 600,
        "height": 400,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              1612,
              903,
              2744,
              935,
              2738,
              1139,
              1607,
              1107
            ],
            "text": "in never failing ,",
            "words": [
              {
                "boundingBox": [
                  1611,
                  934,
                  1707,
                  933,
                  1708,
                  1147,
                  1613,
                  1147
                ],
                "text": "in",
                "confidence": 0.164
              },
              {
                "boundingBox": [
                  1753,
                  933,
                  2132,
                  930,
                  2133,
                  1144,
                  1754,
                  1146
                ],
                "text": "never",
                "confidence": 0.999
              },
              {
                "boundingBox": [
                  2162,
                  930,
                  2673,
                  927,
                  2674,
                  1140,
                  2164,
                  1144
                ],
                "text": "failing",
                "confidence": 0.164
              },
              {
                "boundingBox": [
                  2703,
                  926,
                  2788,
                  926,
                  2790,
                  1139,
                  2705,
                  1140
                ],
                "text": ",",
                "confidence": 0.164
              }
            ]
          }
        ]
      }
    ]
  }
}
```

## <a name="generate-a-thumbnail"></a>產生縮圖

您可以使用電腦視覺搭配智慧裁剪來產生縮圖。 您可指定所需高度和寬度，其可與輸入影像的外觀比例不同。 「電腦視覺」會使用智慧型裁剪，以智慧方式識別關注區域，並產生該區域周圍的裁剪座標。
 
若要建立並執行範例，請執行下列步驟：

1. 將下列命令複製到文字編輯器。
1. 視需要在命令中進行下列變更：
    1. 將 `<subscriptionKey>` 的值取代為您的訂用帳戶金鑰。
    1. 將 `<thumbnailFile>` 的值取代為儲存所傳回縮圖影像的檔案路徑和名稱。
    1. 將要求 URL 的第一個部分 (`westcentralus`) 取代為您端點 URL 中的文字。
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. (選擇性) 將要求本文 (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) 中的影像 URL 變更為要產生縮圖之不同影像的 URL。
1. 開啟 [命令提示字元] 視窗。
1. 將文字編輯器中的命令貼到命令提示字元視窗中。
1. 按下 Enter 執行程式。

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westus.api.cognitive.microsoft.com/vision/v3.1/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

### <a name="examine-the-response"></a>檢查回應

成功的回應會將縮圖影像寫入至 `<thumbnailFile>` 中指定的檔案。 如果要求失敗，回應會包含錯誤碼和訊息以協助判斷問題所在。 如果要求看似成功，但所建立的縮圖卻不是有效的影像檔案，則可能表示您的訂用帳戶金鑰無效。


## <a name="next-steps"></a>後續步驟

深入探索電腦視覺 API。 若要快速地試驗電腦視覺 API，請嘗試 [Open API 測試主控台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b/console)。

> [!div class="nextstepaction"]
> [探索電腦視覺 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b)
