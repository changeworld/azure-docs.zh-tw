---
title: 快速入門：使用 Azure REST API 和 C# 偵測影像中的臉部
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將使用 Azure Face REST API 搭配 C# 來偵測影像中的臉部。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.openlocfilehash: f15fbc340aafa325f772a1fd50a2129fa5044c47
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "96020869"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-c"></a>快速入門：使用 Face REST API 和 C# 偵測影像中的臉部

在本快速入門中，您將使用 Azure Face REST API 搭配 C# 來偵測影像中的人臉。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="建立 Face 資源"  target="_blank">建立 Face 資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至 Face API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。
- 任何版本的 [Visual Studio](https://www.visualstudio.com/downloads/)。

## <a name="create-the-visual-studio-project"></a>建立 Visual Studio 專案

1. 在 Visual Studio 中建立新的 **主控台應用程式 (.NET Framework)** 專案，並將其命名為 **FaceDetection**。
1. 如果您的解決方案中有其他專案，請選取此專案作為單一啟始專案。

## <a name="add-face-detection-code"></a>新增臉部偵測程式碼

開啟新專案的 Program.cs 檔案。 在這裡，您將新增載入影像及偵測臉部所需的程式碼。

### <a name="include-namespaces"></a>包含命名空間

在 *Program.cs* 檔案的頂端新增下列 `using` 陳述式。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="dependencies":::

### <a name="add-essential-fields"></a>新增必要欄位

新增包含下列欄位的 **Program** 類別。 這項資料會指定連線到 Face 服務的方式，以及接收輸入資料的位置。 您將需要以訂用帳戶金鑰值更新 `subscriptionKey` 欄位，並變更 `uriBase` 字串，使它包含您的資源端點字串。

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="environment":::

### <a name="receive-image-input"></a>接收影像輸入

對 **Program** 類別的 **Main** 方法新增下列程式碼。 此程式碼會將提示寫入至主控台，要求使用者輸入其本機影像檔案路徑。 然後呼叫另一種方法：**MakeAnalysisRequest**，以處理該位置上的影像。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="main":::

### <a name="call-the-face-detection-rest-api"></a>呼叫臉部偵測 REST API

將下列方法新增至 **Program** 類別。 此方法會建構對臉部 API 發出的 REST 呼叫，以偵測遠端影像中的臉部資訊 (`requestParameters` 字串會指定要擷取的臉部屬性)。 然後將輸出資料寫入 JSON 字串。

您將在下列步驟中定義協助程式方法。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="request":::

### <a name="process-the-input-image-data"></a>處理輸入影像資料

將下列方法新增至 **Program** 類別。 此方法會將位於指定檔案路徑的影像轉換成位元組陣列。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="getimage":::

### <a name="parse-the-json-response"></a>剖析 JSON 回應

將下列方法新增至 **Program** 類別。 此方法會格式化 JSON 輸入，以便閱讀。 您的應用程式會將此字串資料寫入主控台。 然後您就可以關閉類別和命名空間。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="print":::

## <a name="run-the-app"></a>執行應用程式

成功的回應會以可輕鬆閱讀的 JSON 格式顯示臉部資料。 例如：

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
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

```csharp
string requestParameters = "detectionModel=detection_01&returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";
```

回應現在包含臉部屬性。 例如：

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
      "faceRectangle": {
         "top": 131,
         "left": 177,
         "width": 162,
         "height": 162
      },
      "faceAttributes": {
         "smile": 0.0,
         "headPose": {
            "pitch": 0.0,
            "roll": 0.1,
            "yaw": -32.9
         },
         "gender": "female",
         "age": 22.9,
         "facialHair": {
            "moustache": 0.0,
            "beard": 0.0,
            "sideburns": 0.0
         },
         "glasses": "NoGlasses",
         "emotion": {
            "anger": 0.0,
            "contempt": 0.0,
            "disgust": 0.0,
            "fear": 0.0,
            "happiness": 0.0,
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
            "value": 0.0
         },
         "makeup": {
            "eyeMakeup": true,
            "lipMakeup": true
         },
         "accessories": [

         ],
         "occlusion": {
            "foreheadOccluded": false,
            "eyeOccluded": false,
            "mouthOccluded": false
         },
         "hair": {
            "bald": 0.0,
            "invisible": false,
            "hairColor": [
               {
                  "color": "brown",
                  "confidence": 1.0
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

在本快速入門中，您已建立簡單的 .NET 主控台應用程式來搭配使用 REST 呼叫和 Azure 臉部辨識服務，進而偵測影像中的臉部並傳回其屬性。 接下來，請瀏覽臉部 API 參考文件，以深入了解支援的案例。

> [!div class="nextstepaction"]
> [臉部 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
