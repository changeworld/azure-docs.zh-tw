---
title: 如何在時間序列資料上使用異常偵測器 API
titleSuffix: Azure Cognitive Services
description: 瞭解如何以批次或串流資料來偵測資料中的異常情況。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: mbullwin
ms.openlocfilehash: 74f891ba7f5b400b5782565e670539167f4e2464
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2020
ms.locfileid: "97703427"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>如何：在時間序列資料上使用異常偵測器 API  

異常偵測器 [API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) 提供兩種異常偵測方法。 您可以在整個時間序列中偵測異常狀況，或偵測最新資料點的異常狀態以產生您的資料。 偵測模型會傳回異常結果以及每個資料點的預期值，以及最高和較低的異常偵測界限。 您可以使用這些值來視覺化一般值的範圍，以及資料中的異常。

## <a name="anomaly-detection-modes"></a>異常偵測模式 

異常偵測器 API 會提供偵測模式：批次和串流處理。

> [!NOTE]
> 下列要求 Url 必須與您的訂用帳戶的適當端點結合。 例如：`https://<your-custom-subdomain>.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>批次偵測

若要在指定的時間範圍內偵測整個資料點批次的異常狀況，請使用下列要求 URI 搭配您的時間序列資料： 

`/timeseries/entire/detect`. 

藉由一次傳送您的時間序列資料，API 將會使用整個數列來產生模型，並使用它分析每個資料點。  

### <a name="streaming-detection"></a>串流偵測

若要持續偵測串流資料的異常狀況，請使用下列要求 URI 與您最新的資料點： 

`/timeseries/last/detect'`. 

當您產生新的資料點時，您可以即時監視資料。 將會使用您傳送的資料點來產生模型，且 API 會判斷時間序列中的最新時間點是否為異常。

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>調整低和高異常偵測界限

根據預設，系統會使用、和來計算異常偵測的上限和下限 `expectedValue` `upperMargin` `lowerMargin` 。 如果您需要不同的界限，建議您 `marginScale` 將套用至 `upperMargin` 或 `lowerMargin` 。 界限的計算方式如下：

|界限  |計算  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

下列範例顯示不同敏感性的異常偵測器 API 結果。

### <a name="example-with-sensitivity-at-99"></a>敏感度為99的範例

![預設敏感度](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>敏感度為95的範例

![99敏感度](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>敏感度為85的範例

![85敏感度](../media/sensitivity_85.png)

## <a name="next-steps"></a>後續步驟

* [什麼是異常偵測器 API？](../overview.md)
* [快速入門：使用異常偵測器偵測時間序列資料中的異常狀況](../quickstarts/client-libraries.md)
