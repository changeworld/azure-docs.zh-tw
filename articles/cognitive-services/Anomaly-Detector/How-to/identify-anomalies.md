---
title: 如何在時間序列資料上使用異常檢測器 API
titleSuffix: Azure Cognitive Services
description: 瞭解如何檢測資料中的異常，無論是批次處理還是流資料。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: aahi
ms.openlocfilehash: ca93de71f64efaf21c78b37b9c9aee193d13b28d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "71840230"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>如何：在時間序列資料上使用異常檢測器 API  

[異常檢測器 API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)提供了兩種異常檢測方法。 您可以在整個時間序列中檢測異常作為批次處理，也可以通過檢測最新資料點的異常狀態生成資料。 檢測模型返回異常結果以及每個資料點的預期值以及上下異常檢測邊界。 您可以使用這些值來視覺化正常值的範圍和資料中的異常。

## <a name="anomaly-detection-modes"></a>異常檢測模式 

異常檢測器 API 提供檢測模式：批次處理和流式處理。

> [!NOTE]
> 以下請求 URL 必須與訂閱的相應終結點結合使用。 例如： `https://<your-custom-subdomain>.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>批次偵測

要檢測給定時間範圍內一批資料點的異常，請使用以下請求 URI 與時間序列資料： 

`/timeseries/entire/detect`. 

通過一次發送時間序列資料，API 將使用整個系列生成模型，並用它來分析每個資料點。  

### <a name="streaming-detection"></a>流式檢測

要持續檢測流資料上的異常，請使用以下請求 URI 與最新的資料點： 

`/timeseries/last/detect'`. 

通過在生成新資料點時發送資料點，可以即時監視資料。 將生成一個模型，並生成您發送的資料點，API 將確定時間序列中的最新點是否為異常。

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>調整下部和上部異常檢測邊界

預設情況下，使用`expectedValue`和`upperMargin`計算異常檢測的上邊界和下邊界。 `lowerMargin` 如果您需要不同的邊界，我們建議對`marginScale``upperMargin`或`lowerMargin`應用 。 邊界的計算方式如下：

|界限  |計算  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

以下示例顯示了異常檢測器 API 結果，其敏感性不同。

### <a name="example-with-sensitivity-at-99"></a>靈敏度為 99 的示例

![預設敏感度](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>靈敏度為 95 的示例

![99 靈敏度](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>靈敏度為 85 的示例

![85 靈敏度](../media/sensitivity_85.png)

## <a name="next-steps"></a>後續步驟

* [什麼是異常檢測器 API？](../overview.md)
* [快速入門：使用異常檢測器 REST API 檢測時間序列資料中的異常情況](../quickstarts/detect-data-anomalies-csharp.md)
