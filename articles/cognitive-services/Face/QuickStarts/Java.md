---
title: 快速入門：使用 Azure REST API 和 Java 偵測影像中的臉部
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將使用 Azure Face REST API 搭配 Java 來偵測影像中的臉部。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/23/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: b8ea2908711497db5e86a7dd665548e33b9d9f25
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "96020852"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-java"></a>快速入門：使用 REST API 和 Java 偵測影像中的人臉

在本快速入門中，您將使用 Azure Face REST API 搭配 Java 來偵測影像中的人臉。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。 

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="建立 Face 資源"  target="_blank">建立 Face 資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至 Face API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。
* 您選擇的任何 Java IDE。

## <a name="create-the-java-project"></a>建立 Java 專案

1. 在您的 IDE 中建立新的命令列 Java 應用程式，並以 **main** 方法新增 **Main** 類別。
1. 將下列程式庫匯入您的 Java 專案中。 如果您使用 Maven，則會提供每個程式庫的 Maven 座標。
   - [Apache HTTP 用戶端](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.6)
   - [Apache HTTP 核心](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.10)
   - [JSON 程式庫](https://github.com/stleary/JSON-java) (org.json:json:20180130)
   - [Apache Commons 記錄](https://commons.apache.org/proper/commons-logging/download_logging.cgi) (commons-logging:commons-logging:1.1.2)

## <a name="add-face-detection-code"></a>新增臉部偵測程式碼

開啟您專案的 Main 類別。 在這裡，您將新增載入影像及偵測臉部所需的程式碼。

### <a name="import-packages"></a>匯入套件

在檔案頂端新增下列 `import` 陳述式。

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="dependencies":::

### <a name="add-essential-fields"></a>新增必要欄位

將 **Main** 類別取代為下列程式碼。 這項資料會指定連線到 Face 服務的方式，以及接收輸入資料的位置。 您將需要以訂用帳戶金鑰值更新 `subscriptionKey` 欄位，並變更 `uriBase` 字串，使它包含正確的端點字串。 您可以將 `imageWithFaces` 值設定為指向不同影像檔的路徑。

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

`faceAttributes` 欄位只是特定屬性類型的清單。 其用於指定要對偵測到的臉部擷取哪些資訊。

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="environment":::

### <a name="call-the-face-detection-rest-api"></a>呼叫臉部偵測 REST API

使用下列程式碼新增 **main** 方法。 此方法會建構對臉部 API 發出的 REST 呼叫，以偵測遠端影像中的臉部資訊 (`faceAttributes` 字串會指定要擷取的臉部屬性)。 然後將輸出資料寫入 JSON 字串。

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="main":::

### <a name="parse-the-json-response"></a>剖析 JSON 回應

直接在先前的程式碼下方新增下列區塊，這會將傳回的 JSON 資料轉換成更容易閱讀的格式，然後再將資料列印至主控台。 最後，關閉 try-catch 區塊、**main** 方法和 **Main** 類別。

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="print":::

## <a name="run-the-app"></a>執行應用程式

編譯程式碼並加以執行。 在主控台視窗中，成功的回應會以可輕鬆閱讀的 JSON 格式顯示臉部資料。 例如：

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  }
}]
```

## <a name="extract-face-attributes"></a>擷取臉部屬性
 
若要擷取臉部屬性，請使用偵測模型 1 並新增 `returnFaceAttributes` 查詢參數。

```java
builder.setParameter("detectionModel", "detection_01");
builder.setParameter("returnFaceAttributes", "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise");
```

回應現在包含臉部屬性。 例如：

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  },
  "faceAttributes": {
    "makeup": {
      "eyeMakeup": true,
      "lipMakeup": true
    },
    "facialHair": {
      "sideburns": 0,
      "beard": 0,
      "moustache": 0
    },
    "gender": "female",
    "accessories": [],
    "blur": {
      "blurLevel": "low",
      "value": 0.06
    },
    "headPose": {
      "roll": 0.1,
      "pitch": 0,
      "yaw": -32.9
    },
    "smile": 0,
    "glasses": "NoGlasses",
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
    },
    "emotion": {
      "contempt": 0,
      "surprise": 0.005,
      "happiness": 0,
      "neutral": 0.986,
      "sadness": 0.009,
      "disgust": 0,
      "anger": 0,
      "fear": 0
    },
    "exposure": {
      "value": 0.67,
      "exposureLevel": "goodExposure"
    },
    "occlusion": {
      "eyeOccluded": false,
      "mouthOccluded": false,
      "foreheadOccluded": false
    },
    "noise": {
      "noiseLevel": "low",
      "value": 0
    },
    "age": 22.9
  },
  "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f"
}]
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立簡單的 Java 主控台應用程式以對 Azure 臉部 API 使用 REST 呼叫，進而偵測影像中的臉部並傳回其屬性。 接下來，請瀏覽臉部 API 參考文件，以深入了解支援的案例。

> [!div class="nextstepaction"]
> [臉部 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
