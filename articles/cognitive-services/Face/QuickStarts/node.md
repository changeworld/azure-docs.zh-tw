---
title: 快速入門：使用 Azure REST API 和 Node.js 偵測影像中的臉部
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將使用 Azure Face REST API 搭配 Node.js 來偵測影像中的臉部。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: devx-track-javascript
ms.openlocfilehash: 44bd0a766574889334601a354b47946a80fc475a
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87833687"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-nodejs"></a>快速入門：使用 Face REST API 和 Node.js 偵測影像中的臉部

在本快速入門中，您將使用 Azure Face REST API 搭配 Node.js 來偵測影像中的人臉。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="建立 Face 資源"  target="_blank">建立 Face 資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至 Face API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。
- 程式碼編輯器，例如 [Visual Studio Code](https://code.visualstudio.com/download)

## <a name="set-up-the-node-environment"></a>設定 Node 環境

移至您想要建立專案的資料夾，並建立新的檔案 (facedetection.js)。 然後將 `axios` 模組安裝至這個專案。 這可讓您的指令碼發出 HTTP 要求。

```shell
npm install axios --save
```

## <a name="write-the-nodejs-script"></a>撰寫 Node.js 指令碼

將下列程式碼貼入 facedetection.js。 這些欄位會指定連線到 Face 服務的方式，以及接收輸入資料的位置。 [建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication)並將您的 Face 訂用帳戶金鑰和端點新增至環境變數。 您可以變更 `imageUrl` 欄位以指向您自己的輸入影像。

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

```javascript
'use strict';

const axios = require('axios').default;

// Add a valid subscription key and endpoint to your environment variables.
let subscriptionKey = process.env['FACE_SUBSCRIPTION_KEY']
let endpoint = process.env['FACE_ENDPOINT'] + '/face/v1.0/detect'

// Optionally, replace with your own image URL (for example a .jpg or .png URL).
let imageUrl = 'https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/faces.jpg'
```

然後，新增下列程式碼以呼叫人臉識別 API，並從輸入影像中取得臉部屬性資料。 `returnFaceAttributes` 欄位會指定要擷取的臉部屬性。 您可以根據您的用途來變更此字串。


```javascript
// Send a POST request
axios({
    method: 'post',
    url: endpoint,
    params : {
        returnFaceId: true,
        returnFaceLandmarks: false,
        returnFaceAttributes: 'age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise'
    },
    data: {
        url: imageUrl,
    },
    headers: { 'Ocp-Apim-Subscription-Key': subscriptionKey }
}).then(function (response) {
    console.log('Status text: ' + response.status)
    console.log('Status text: ' + response.statusText)
    console.log()
    //console.log(response.data)
    response.data.forEach((face) => {
      console.log('Face ID: ' + face.faceId)
      console.log('Face rectangle: ' + face.faceRectangle.top + ', ' + face.faceRectangle.left + ', ' + face.faceRectangle.width + ', ' + face.faceRectangle.height)
      console.log('Smile: ' + face.faceAttributes.smile)
      console.log('Head pose: ' + JSON.stringify(face.faceAttributes.headPose))
      console.log('Gender: ' + face.faceAttributes.gender)
      console.log('Age: ' + face.faceAttributes.age)
      console.log('Facial hair: ' + JSON.stringify(face.faceAttributes.facialHair))
      console.log('Glasses: ' + face.faceAttributes.glasses)
      console.log('Smile: ' + face.faceAttributes.smile)
      console.log('Emotion: ' + JSON.stringify(face.faceAttributes.emotion))
      console.log('Blur: ' + JSON.stringify(face.faceAttributes.blur))
      console.log('Exposure: ' + JSON.stringify(face.faceAttributes.exposure))
      console.log('Noise: ' + JSON.stringify(face.faceAttributes.noise))
      console.log('Makeup: ' + JSON.stringify(face.faceAttributes.makeup))
      console.log('Accessories: ' + JSON.stringify(face.faceAttributes.accessories))
      console.log('Hair: ' + JSON.stringify(face.faceAttributes.hair))
      console.log()
    });
}).catch(function (error) {
    console.log(error)
});
```

## <a name="save-and-run-the-script"></a>儲存並執行指令碼

在進行變更後，請開啟命令提示字元並使用 `node` 命令執行檔案。

```
node facedetection.js
```

以下是來自 `response.data` 的完整 JSON 資料。 例如：

```json
[
  {
    "faceId": "ae8952c1-7b5e-4a5a-a330-a6aa351262c9",
    "faceRectangle": {
      "top": 621,
      "left": 616,
      "width": 195,
      "height": 195
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 6.8,
        "yaw": 3.7
      },
      "gender": "male",
      "age": 37,
      "facialHair": {
        "moustache": 0.4,
        "beard": 0.4,
        "sideburns": 0.1
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.999,
        "sadness": 0.001,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.89
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.51
      },
      "noise": {
        "noiseLevel": "medium",
        "value": 0.59
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.04,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.98
          },
          {
            "color": "brown",
            "confidence": 0.87
          },
          {
            "color": "gray",
            "confidence": 0.85
          },
          {
            "color": "other",
            "confidence": 0.25
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.02
          }
        ]
      }
    }
  },
  {
    "faceId": "b1bb3cbe-5a73-4f8d-96c8-836a5aca9415",
    "faceRectangle": {
      "top": 693,
      "left": 1503,
      "width": 180,
      "height": 180
    },
    "faceAttributes": {
      "smile": 0.003,
      "headPose": {
        "pitch": 0,
        "roll": 2,
        "yaw": -2.2
      },
      "gender": "female",
      "age": 56,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0.001,
        "disgust": 0,
        "fear": 0,
        "happiness": 0.003,
        "neutral": 0.984,
        "sadness": 0.011,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.83
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.41
      },
      "noise": {
        "noiseLevel": "high",
        "value": 0.76
      },
      "makeup": {
        "eyeMakeup": false,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.06,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.99
          },
          {
            "color": "gray",
            "confidence": 0.89
          },
          {
            "color": "other",
            "confidence": 0.64
          },
          {
            "color": "brown",
            "confidence": 0.34
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.03
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已撰寫 Node.js 指令碼來呼叫 Azure 臉部辨識服務，進而偵測影像中的臉部並傳回其屬性。 接下來，請瀏覽臉部 API 參考文件，以取得更多資訊。

> [!div class="nextstepaction"]
> [臉部 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
