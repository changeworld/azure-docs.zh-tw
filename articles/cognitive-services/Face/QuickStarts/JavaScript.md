---
title: 快速入門：使用 REST API 和 JavaScript 偵測影像中的人臉
titleSuffix: Azure Cognitive Services
description: 在此快速入門中，您可以使用認知服務的臉部 API 搭配 JavaScript，偵測影像中的臉部。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.custom: devx-track-js
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 06aa840c3cf33c9d1b70b800d45b9b455c4d61ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858331"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-javascript"></a>快速入門：使用 REST API 和 JavaScript 偵測影像中的人臉

在本快速入門中，您將使用 Azure Face REST API 搭配 JavaScript 來偵測影像中的人臉。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="建立 Face 資源"  target="_blank">建立 Face 資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至 Face API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。
* 程式碼編輯器，例如 [Visual Studio Code](https://code.visualstudio.com/download)

## <a name="initialize-the-html-file"></a>初始化 HTML 檔案

建立新的 HTML 檔案 (detectFaces.html)**，並新增下列程式碼。

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Detect Faces Sample</title>
        <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
    </head>
    <body></body>
</html>
```

然後，在文件的 `body` 元素內新增下列程式碼。 此程式碼會設定基本的使用者介面，內有 URL 欄位、[分析臉部]**** 按鈕、[回應] 窗格和 [影像顯示] 窗格。

:::code language="html" source="~/cognitive-services-quickstart-code/javascript/web/face/rest/detect.html" id="html_include":::

## <a name="write-the-javascript-script"></a>撰寫 JavaScript 指令碼

在文件中的 `h1` 元素正上方新增下列程式碼。 此程式碼會設定 JavaScript 程式碼來呼叫臉部 API。

:::code language="html" source="~/cognitive-services-quickstart-code/javascript/web/face/rest/detect.html" id="script_include":::

您將需要以訂用帳戶金鑰值更新 `subscriptionKey` 欄位，且您必須變更 `uriBase` 字串，使它包含正確的端點字串。 `returnFaceAttributes` 欄位可指定所要擷取的臉部屬性；您可以根據您的用途來變更此字串。

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="run-the-script"></a>執行指令碼

在瀏覽器中開啟 detectFaces.html**。 當您按一下 [分析臉部]**** 按鈕時，應用程式應該會顯示來自指定 URL 的影像，並印出臉部資料的 JSON 字串。

![GettingStartCSharpScreenshot](../Images/face-detect-javascript.png)

以下文字是成功 JSON 回應的範例。

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

## <a name="extract-face-attributes"></a>擷取臉部屬性
 
若要擷取臉部屬性，請使用偵測模型 1 並新增 `returnFaceAttributes` 查詢參數。

```javascript
// Request parameters.
var params = {
    "detectionModel": "detection_01",
    "returnFaceAttributes": "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise",
    "returnFaceId": "true"
};
```

回應現在包含臉部屬性。 例如：

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

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已撰寫 JavaScript 指令碼來呼叫 Azure 臉部辨識服務，進而偵測影像中的臉部並傳回其屬性。 接下來，請瀏覽臉部 API 參考文件，以取得更多資訊。

> [!div class="nextstepaction"]
> [臉部 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
