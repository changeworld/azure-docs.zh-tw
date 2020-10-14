---
title: 推斷中繼資料結構描述 - Azure
description: 在本文中，您將了解推斷中繼資料結構描述。
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 2de437577dc00692fb98c46fec32bfaa6612dc99
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019489"
---
# <a name="inference-metadata-schema"></a>推斷中繼資料結構描述 

不論使用 HTTP 型合約還是 gRPC 型合約的推斷物件都會遵循本主題所述的物件模型。

## <a name="object-model"></a>物件模型

![物件模型](./media/inference-metadata-schema/object-model.png)
 
|類型定義|描述|
|---|---|
|Tag|與結果相關聯的標記或標籤。和標記一起使用時，您甚至會取得與標記相關聯的信賴度值。|
|屬性|與結果相關聯的其他屬性。 您可以新增您從推斷引擎收到的新屬性以及信賴度值。|
|屬性清單|選擇性屬性的清單。|
|矩形|相對於影像左上角的矩形區域。 必要的屬性會是「長度」、「寬度」、「高度」和「距原點的上邊緣距離」。|
|分類|分類器的標籤通常適用於整個範例。 有了「標記」的協助，您就可以將結果分類。|
|單位|在範例中偵測到或識別出的實體。 當推斷引擎偵測到實體時，其會取得「標記」、已推斷的其他屬性，並傳回所找到實體周圍矩形方塊的座標。|
|事件|在範例上偵測到的事件。 在範例中偵測到事件時，會傳回事件的名稱和事件的特定屬性。|
|動作|在範例上偵測到的動作。 在範例中偵測到動作時，會傳回偵測到動作的矩形周框方塊座標。|
|文字|會傳回與範例相關聯的文字，以及文字的開始和結束時間戳記。|
|其他|傳回其他一般承載資訊。|

下列範例包含具有一些受支援推斷類型的單一事件：

```
[ 
  // Light Detection 
  { 
    "type": "classification", 
    "subtype": "lightDetection", 
    "classification": { 
      "tag": { "value": "daylight", "confidence": 0.86 }, 
      "attributes": [ 
          { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 } 
      ] 
    } 
  }, 
 
  // Motion Detection 
  { 
    "type": "motion", 
    "subtype": "motionDetection", 
    "motion": 
    { 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Yolo V3 
  { 
    "type": "entity", 
    "subtype": "objectDetection",     
    "entity": 
    { 
      "tag": { "value": "dog", "confidence": 0.97 }, 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Vehicle Identification 
  { 
    "type": "entity", 
    "subtype": "vehicleIdentification",     
    "entity": 
    { 
      "tag": { "value": "007-SPY", "confidence": 0.82 }, 
      "attributes":[   
        { "name": "color", "value": "black", "confidence": 0.90 }, 
        { "name": "body", "value": "coupe", "confidence": 0.87 }, 
        { "name": "make", "value": "Aston Martin", "confidence": 0.35 }, 
        { "name": "model", "value": "DBS V12", "confidence": 0.33 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // People Identification 
  { 
    "type": "entity", 
    "subtype": "peopleIdentification",     
    "entity": 
    { 
      "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 }, 
      "attributes":[   
        { "name": "age", "value": "73", "confidence": 0.87 }, 
        { "name": "glasses", "value": "yes", "confidence": 0.94 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    }, 
 
    // Open type coming from the gRPC Map 
    "extensions":  
    { 
      "vector": "e1xkaXNwbGF5c3R5bGUgaVxoYmFyIHtcZnJhYyB7ZH17ZHR9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSA9e1xoYXQge0h9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSB9KQ==", 
      "skeleton": "p1,p2,p3,p4" 
    } 
  }, 
 
  // Captions 
  {     
    "type": "text", 
    "subtype": "speechToText",   
    "text": 
    { 
      "value": "Humor 75%. Confirmed. Self-destruct sequence in T minus 10… 9…", 
      "language": "en-US", 
      "startTimestamp": 12000, 
      "endTimestamp": 13000 
    } 
]
```

## <a name="next-steps"></a>後續步驟

- [gRPC 資料合約](./grpc-extension-protocol.md)
- [HTTP 資料合約](./http-extension-protocol.md)