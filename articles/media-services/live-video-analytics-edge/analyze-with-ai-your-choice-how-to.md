---
title: 使用您選擇的 AI 來分析即時影片-Azure
description: 在本文中，您將瞭解如何建立 IoT Edge 模組，以與 IoT Edge 上的即時影片分析整合，以使用您選擇的電腦視覺模型來分析即時影片。
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 6a1ea3ebd8c7de4c691d7a982dbc08e9d08d9e38
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86182860"
---
# <a name="analyze-live-video-with-ai-of-your-choice"></a>使用您選擇的 AI 分析即時影片

在本文中，您將瞭解如何建立 IoT Edge 模組，以與 IoT Edge 上的即時影片分析整合，以使用您選擇的電腦視覺模型來分析即時影片。 

## <a name="pre-reading"></a>預先閱讀

[媒體圖表概念](media-graph-concept.md)

## <a name="media-graph-extension"></a>Media graph 擴充功能

IoT Edge 上的即時影片分析定義了擴充性模型，可讓您透過圖表延伸模組，將 media graph 處理功能延伸至您自己的媒體分析元件。 您的分析元件可以利用傳統的映射處理技術或電腦視覺 AI 模型。 藉由在 media graph 中包含[HTTP 擴充處理器](media-graph-concept.md#http-extension-processor)節點，即可啟用圖形延伸模組。 HTTP 延伸模組處理器可以將影片畫面轉送至您所指定的 HTTP 端點，並透過這項功能作為元件的介面。 可以連接到本機或遠端端點，並可在必要時由驗證和 TLS 加密來保護。 此外，處理器還可以在向前轉送之前，選擇性地調整和編碼影片畫面。

您可以選擇在任何可用的推斷執行時間（例如 ONNX、TensorFlow、PyTorch 或您自己的 docker 容器）上執行您選擇的推斷模型。 您也可以使用您選擇的程式設計語言和程式庫，透過 HTTP 伺服器在您自己的容器上公開影像推斷功能。 推斷容器應該與即時影片分析 edge 模組一起部署，以獲得最佳效能，然後透過您的圖形拓撲中包含的 HTTP 擴充處理器來叫用它。
此外，您可以選擇性地將動作偵測器[處理器](media-graph-concept.md#motion-detection-processor)和[畫面播放速率篩選處理器](media-graph-concept.md#frame-rate-filter-processor)（上游）新增至 HTTP 擴充處理器，藉此節流呼叫自訂模組的頻率。

下圖描述高階資料流程：

![邊緣裝置](./media/analyze-live-video-with-ai-your-choice-how-to/edge-device.png)

這可讓您使用您選擇的 AI 模型來分析影片，以符合您獨特的商務需求。 AI 模型可能來自開放原始碼的社區，或來自您自己的資料科學家或特製化的 AI 提供者。

## <a name="media-graph-http-extension-contract-definitions"></a>Media graph HTTP 延伸模組合約定義

此區段定義定義資料流程的 HTTP 合約。

### <a name="http-extensibility-protocol"></a>HTTP 擴充性通訊協定  

HTTP 合約定義如下：

* 您的模組會作為 HTTP 伺服器。
* IoT Edge 模組上的即時影片分析會作為 HTTP 用戶端。

### <a name="request"></a>要求

從即時影片分析模組到您模組的要求會如下所示：

| 機碼 | 值 |
|---|---|
|POST| `https://hostname/optional-path?optional-query`|
|接受|application/json、*/*|
|授權| 基本、摘要、持有者 (透過自訂標頭支援) |
|Content-Type|image/jpeg<br/>image/png<br/>image/bmp<br/>影像/x-原始|
|Content-Length|主體長度（以位元組為單位）|
|User-Agent|Azure 媒體服務|
|主體|影像位元組，以其中一種支援的內容類型編碼的二進位檔。|

#### <a name="example"></a>範例

```
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

### <a name="response"></a>回應

從您的模組到 Live Video Analytics 模組的回應應如下所示

| 機碼 | 值 |
|---|---|
|狀態碼|200確定-找到推斷結果<br/>204無內容-AI 找不到任何內容<br/>400不正確的要求-不應為<br/>500內部伺服器錯誤-不應為<br/>503伺服器忙碌-AMS 會根據「重試後」標頭，或根據預設的時間長度（如果未設定標頭，則為）。|
|Content-Type|application/json|
|Content-Length|    主體長度（以位元組為單位）|
|主體|具有單一「推斷」屬性的 JSON 物件。|

#### <a name="example"></a>範例

```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 468
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 17 Apr 2020 04:44:01 GMT

{
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.9048132 },
        "box": { "l": 0.42681578, "t": 0.47660735, "w": 0.019501392, "h": 0.020954132 }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.8953932 },
        "box": { "l": 0.55083525, "t": 0.4843858, "w": 0.046550274, "h": 0.046502113 }
      }
    }    
  ]
}
```

強烈建議您在下列定義的預先建立架構之後，使用有效的 JSON 檔來傳迴響應。 這可讓您更有效地確保與其他元件的互通性，以及新增至即時影片分析模組的未來功能。

如果您的模組傳回的回應中內容類型不是 "application/json"，即時影片分析會將訊息編碼為基底64內容，並將其序列化為不透明的 JSON 承載。

如果您的模組傳回內容類型為 "application/json" 的回應，但 JSON 架構並未遵循[下面所述的推斷中繼資料架構](#inference-metadata-schema)，則會透過管線轉送訊息內容，但會降低互通性。

> [!NOTE]
> 如果您的模組未產生任何結果，它應該會傳回 HTTP 204 狀態碼， (沒有內容) 空白的回應主體。 即時影片分析將瞭解這是空的結果，而且不會在整個管線中轉送事件。

### <a name="inference-metadata-schema"></a>推斷中繼資料架構

每個推斷物件會遵循此超集合架構：

```
{
  "type": "[classification|motion|entity|text|other]",
  "subtype": "",              // Free form subtype id

  // Object has *at most one of* the following
  "classification":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ]
  },

  "motion":
  {
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
    // "regionId": ""
  },

  "entity":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ],
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
  },

  "text":
  {
    "value": "",              // Text value
    "language": "",           // Optional BCP47 Language Code (https://tools.ietf.org/html/bcp47)
    "startTimestamp": 0,      // Optional start timestamp
    "endTimestamp": 0         // Optional end timestamp
  },

  // Plus every object can have zero or more extensions
  "extensions":
  {
    "name1": "value1",
    "name2": "value2"
    // ...
  }
}
```
 
### <a name="data-contracts---class-hierarchy"></a>資料合約-類別階層

![資料合約-類別階層](./media/analyze-live-video-with-ai-your-choice-how-to/data-contracts.png)

### <a name="examples"></a>範例  

下列範例包含具有所有支援之推斷類型的單一事件：

```
{
  "inferences": [
    // Light detection
    {
      "type": "classification",
      "subtype": "lightDetection",
      "classification": {
        "tag": { "value": "daylight", "confidence": 0.86 },
        "attributes": [
            { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 }
        ]
      }
    },

    // Motion detection
    {
      "type": "motion",
      "subtype": "motionDetection",
      "motion":
      {
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Object detection
    {
      "type": "entity",
      "subtype": "objectDetection",    
      "entity":
      {
        "tag": { "value": "dog", "confidence": 0.97 },
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Vehicle identification
    {
      "type": "entity",
      "subtype": "vehicleIdentification",    
      "entity":
      {
        "tag": { "value": "007-SPY", "confidence": 0.82 },
        "attributes":[  
          { "name": "color", "value": "black", "confidence": 0.90 },
          { "name": "body", "value": "coupe", "confidence": 0.87 },
          { "name": "make", "value": "Aston Martin", "confidence": 0.35 },
          { "name": "model", "value": "DBS V12", "confidence": 0.33 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // People identification
    {
      "type": "entity",
      "subtype": "peopleIdentification",    
      "entity":
      {
        "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 },
        "attributes":[  
          { "name": "age", "value": "73", "confidence": 0.87 },
          { "name": "glasses", "value": "yes", "confidence": 0.94 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      },
    }
  ]
}
```
 
## <a name="sample-http-extension-modules"></a>HTTP 延伸模組範例

您可以在[即時影片分析 GitHub](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis)存放庫中找到一些範例 HTTP 延伸模組。 其中一個[影片分析範例示範](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx)如何使用[YOLOv3](https://pjreddie.com/darknet/yolo/) [ONNX](http://onnx.ai/)模型來建立 IoT Edge 模組，以進行物件偵測。 另一個[影片分析範例示範](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx-tiny)如何使用小型 YOLOv3，這是輕量版的 YOLOv3 ONNX 模型。 您可以使用相同的方法，透過您選擇的 AI 模型來建立自己的模組。

## <a name="next-steps"></a>後續步驟

[疑難排解](troubleshoot-how-to.md)
