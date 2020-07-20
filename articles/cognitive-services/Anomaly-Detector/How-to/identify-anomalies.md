---
title: 如何在時間序列資料上使用異常偵測器 API
titleSuffix: Azure Cognitive Services
description: 瞭解如何以批次或串流資料的形式偵測資料中的異常狀況。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: aahi
ms.openlocfilehash: ca93de71f64efaf21c78b37b9c9aee193d13b28d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "71840230"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>如何：在時間序列資料上使用異常偵測器 API  

異常偵測器[API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)提供兩種異常偵測方法。 您可以在整個時間序列中以批次方式來偵測異常狀況，或藉由偵測最新資料點的異常狀態來產生資料。 偵測模型會傳回異常結果以及每個資料點的預期值，以及最高和較低的異常偵測界限。 您可以使用這些值來視覺化標準值的範圍，以及資料中的異常。

## <a name="anomaly-detection-modes"></a>異常偵測模式 

異常偵測器 API 提供偵測模式：批次和串流。

> [!NOTE]
> 下列要求 Url 必須與您的訂用帳戶的適當端點結合。 例如：`https://<your-custom-subdomain>.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>批次偵測

若要在指定的時間範圍內，偵測整個資料點批次中的異常狀況，請使用下列要求 URI 搭配您的時間序列資料： 

`/timeseries/entire/detect`. 

藉由一次傳送您的時間序列資料，API 將會使用整個數列來產生模型，並使用它來分析每個資料點。  

### <a name="streaming-detection"></a>串流偵測

若要持續偵測串流資料的異常狀況，請使用下列要求 URI 搭配您的最新資料點： 

`/timeseries/last/detect'`. 

藉由在產生新資料點時傳送它們，您可以即時監視資料。 系統會使用您傳送的資料點來產生模型，而 API 會判斷時間序列中的最新點是否為異常。

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>調整較低和較高的異常偵測界限

根據預設，系統會使用`expectedValue`、 `upperMargin`和`lowerMargin`來計算異常偵測的上限和下限。 如果您需要不同的界限，建議您`marginScale`將套用`upperMargin`至`lowerMargin`或。 界限的計算方式如下：

|界限  |計算  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

下列範例會以不同的敏感性顯示異常偵測器 API 結果。

### <a name="example-with-sensitivity-at-99"></a>敏感度為99的範例

![預設敏感度](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>敏感度為95的範例

![99敏感度](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>敏感度為85的範例

![85敏感度](../media/sensitivity_85.png)

## <a name="next-steps"></a>後續步驟

* [什麼是異常偵測器 API？](../overview.md)
* [快速入門：使用異常偵測器，偵測時間序列資料中的異常狀況 REST API](../quickstarts/detect-data-anomalies-csharp.md)
