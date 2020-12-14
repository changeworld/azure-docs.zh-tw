---
title: 臉部 REST API 快速入門
description: 使用臉部 REST API 搭配 cURL 來偵測臉部、尋找相似 (依影像進行臉部搜尋)、識別臉部 (臉部辨識搜尋) 及遷移臉部資料。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 12/06/2020
ms.author: pafarley
ms.openlocfilehash: be942f73ee0a3d5a8850141c937754bad330db90
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96912116"
---
開始使用臉部 REST API 進行臉部辨識。 臉部服務可讓您存取先進的演算法，以偵測和辨識影像中的人臉。

使用臉部 REST API 來：

* [偵測影像中的臉部](#detect-faces-in-an-image)
* [尋找類似臉部](#find-similar-faces)

> [!NOTE]
> 本快速入門會使用 cURL 命令來呼叫 REST API。 您也可以使用程式設計語言來呼叫 REST API。 如需 [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Face/rest)、[Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Face/rest)、[Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/Face/rest)、[JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Face/rest) 和 [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/Face/rest) 的範例，請參閱 GitHub 範例。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="建立 Face 資源"  target="_blank">建立 Face 資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至 Face API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。


## <a name="detect-faces-in-an-image"></a>偵測影像中的人臉

您將會使用類似下列的命令來呼叫臉部 API，並從影像中取得臉部屬性資料。 首先，將程式碼複製到文字編輯器 &mdash; 您將需要變更命令的特定部分，才能執行該程式碼。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_2":::

進行下列變更：
1. 將 `Ocp-Apim-Subscription-Key` 指派至您的有效臉部訂用帳戶金鑰。
1. 變更查詢 URL 的第一個部分，以符合與您訂用帳戶金鑰對應的端點。
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. 選擇性地將要求主體中的 URL 變更為指向不同的映像。

完成變更之後，請開啟命令提示字元並輸入新的命令。 

### <a name="examine-the-results"></a>檢查結果

您應該會在主控台視窗中看到顯示為 JSON 資料的臉部資訊。 例如：

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    }
  }
]  
```

### <a name="get-face-attributes"></a>取得臉部屬性
 
若要擷取臉部屬性，請再次呼叫偵測 API，但將 `detectionModel` 設定為 `detection_01`。 也請新增 `returnFaceAttributes` 查詢參數。 此命令現在看起來應該如下所示。 如同前面，插入您的臉部訂用帳戶金鑰和端點。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_1":::

### <a name="examine-the-results"></a>檢查結果

傳回的臉部資訊現在包含臉部屬性。 例如：

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="find-similar-faces"></a>尋找類似臉部

此作業會取得一個偵測到的臉部 (來源)，並搜尋一組其他臉部 (目標) 來尋找相符臉部 (依影像進行臉部搜尋)。 若找到相符的臉部，便會將相符臉部的識別碼列印到主控台。

### <a name="detect-faces-for-comparison"></a>偵測臉部以進行比較

首先，您必須先偵測影像中的臉部，才能進行比較。 執行此命令，就像您在[偵測臉部](#detect-faces-in-an-image)一節中所做的一樣。 此偵測方法已針對比較作業最佳化。 該方法不會擷取詳細的臉部特性 (如上節中所述)，而是會使用不同的偵測模型。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detect_for_similar":::

尋找 JSON 回應中的 `"faceId"` 值，並儲存到暫存位置。 然後，再次針對其他影像 URL 呼叫上述命令，並儲存其臉部識別碼。 您會使用這些識別碼作為要從中尋找相似臉部的目標臉部群組。

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_group":::

最後，偵測您將用來比對的單一來源臉部，並儲存其識別碼。 將此識別碼與其他識別碼分開。

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_matcher":::

### <a name="find-matches"></a>尋找相符臉部

將下列命令複製到文字編輯器。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar":::

接著進行下列變更：
1. 將 `Ocp-Apim-Subscription-Key` 指派至您的有效臉部訂用帳戶金鑰。
1. 變更查詢 URL 的第一個部分，以符合與您訂用帳戶金鑰對應的端點。

針對 `body` 值使用下列 JSON 內容：

:::code language="JSON" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_body":::

1. 使用 `"faceId"` 的來源臉部識別碼。
1. 將其他臉部識別碼貼入 `"faceIds"` 陣列中作為條件。

### <a name="examine-the-results"></a>檢查結果

您會收到 JSON 回應，其中列出符合查詢項目的臉部識別碼。

```json
[
    {
        "persistedFaceId" : "015839fb-fbd9-4f79-ace9-7675fc2f1dd9",
        "confidence" : 0.82
    },
    ...
] 
```

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用臉部 REST API 執行基本臉部辨識工作。 接下來，請瀏覽參考文件來深入了解此程式庫。

> [!div class="nextstepaction"]
> [臉部 API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

* [什麼是臉部辨識服務？](../../overview.md)