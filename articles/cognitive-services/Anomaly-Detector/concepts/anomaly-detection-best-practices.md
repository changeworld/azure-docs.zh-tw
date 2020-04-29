---
title: 使用異常偵測器 API 的最佳做法
titleSuffix: Azure Cognitive Services
description: 瞭解使用異常偵測器 API 偵測異常時的最佳作法。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 9407f2fc9375765efb6eb9688b3ebfeef24ba90a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "67721615"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>使用異常偵測器 API 的最佳做法

異常偵測器 API 是無狀態的異常偵測服務。 其結果的精確度和效能可能會受到下列影響：

* 如何準備您的時間序列資料。
* 使用的異常偵測器 API 參數。
* API 要求中的資料點數目。 

您可以使用本文來瞭解使用 API 的最佳作法，以取得資料的最佳結果。 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>使用 batch （整個）或最新（最後一個）點異常偵測的時機

異常偵測器 API 的批次偵測端點可讓您透過整個時間序列資料，偵測異常狀況。 在此偵測模式中，會建立單一統計模型，並將其套用至資料集內的每個點。 如果您的時間序列具有下列特性，我們建議使用批次偵測在一個 API 呼叫中預覽您的資料。

* 季節性時間序列，偶爾會發生異常狀況。
* 具有偶爾尖峰/下降的平面趨勢時間序列。 

我們不建議使用批次異常偵測來進行即時資料監視，或在沒有高於特性的時間序列資料上使用它。 

* 批次偵測只會建立並套用一個模型，每個點的偵測都會在整個數列的內容中完成。 如果時間序列資料在沒有季節性的情況下上下趨勢，則模型可能會遺漏某些變更點（資料中的下降和尖峰）。 同樣地，某些在資料集內較不重要的變更點，可能不會被視為足以併入模型中的明顯意義。

* 進行即時資料監視時，批次偵測的速度會比偵測最新點的異常狀態慢，因為正在分析的點數目。

若要進行即時資料監視，建議您只偵測最新資料點的異常狀態。 藉由持續套用最新的點偵測，可以更有效率且更精確地進行串流資料監視。

下列範例說明這些偵測模式對效能可能造成的影響。 第一張圖顯示持續偵測異常狀態最新點的結果，再加上28個先前觀察到的資料點。 紅色點是異常。

![顯示使用最新點之異常偵測的影像](../media/last.png)

以下是使用批次異常偵測的相同資料集。 針對作業所建立的模型已略過數個異常，並以矩形標記。

![顯示使用 batch 方法之異常偵測的影像](../media/entire.png)

## <a name="data-preparation"></a>資料準備

異常偵測器 API 會接受格式化為 JSON 要求物件的時間序列資料。 時間序列可以是依時間順序記錄的任何數值資料。 您可以將時間序列資料的 windows 傳送至異常偵測器 API 端點，以改善 API 的效能。 您可以傳送的資料點數目下限為12，而最大值為8640點。 資料[細微性](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview)會定義為取樣資料的速率。 

傳送至異常偵測器 API 的資料點必須具有有效的國際標準時間（UTC）時間戳記和數值。 

```json
{
    "granularity": "daily",
    "series": [
      {
        "timestamp": "2018-03-01T00:00:00Z",
        "value": 32858923
      },
      {
        "timestamp": "2018-03-02T00:00:00Z",
        "value": 29615278
      },
    ]
}
```

如果您的資料是在非標準時間間隔進行取樣，您可以在要求中加入`customInterval`屬性來指定它。 例如，如果您的數列每5分鐘取樣一次，您可以將下列內容新增至您的 JSON 要求：

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>遺漏資料點

遺漏的資料點在平均分佈的時間序列資料集中是常見的，特別是細微的取樣間隔。 例如，每隔幾分鐘取樣一次資料）。 在您的資料中遺漏小於10% 的預期點數，對您的偵測結果不會有負面影響。 請考慮根據其特性填入資料中的間距，例如替代較早期間的資料點、線性插補或移動平均。

### <a name="aggregate-distributed-data"></a>匯總分散式資料

異常偵測器 API 最適合用於平均分佈的時間序列。 如果您的資料是隨機散發的，您應該依時間單位（例如，每分鐘、每小時或每日）匯總它，例如。

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>具有季節性模式之資料的異常偵測

如果您知道您的時間序列資料有季節性模式（一個定期發生），您可以改善精確度和 API 回應時間。 

`period`當您建立 JSON 要求時，指定，可將異常偵測延遲降到50%。 `period`是一個整數，它會指定時間序列中重複模式所需的資料點數目。 例如，每日有一個資料點的時間序列會`period`有 as `7`，而每小時有一個點的時間序列（具有相同的每週模式）會有`period`的。 `7*24` 如果您不確定資料的模式，就不需要指定此參數。

為了獲得最佳結果，請`period`提供4個資料點，再加上一個額外的資料點。 例如，如上面所述每週模式的每小時資料，都應該在要求主體（`7 * 24 * 4 + 1`）中提供673資料點。

### <a name="sampling-data-for-real-time-monitoring"></a>即時監視的資料取樣

如果您的串流資料是以短時間進行取樣（例如，秒或分鐘），則傳送建議的資料點數目可能會超過異常偵測器 API 允許的最大數目（8640個資料點）。 如果您的資料顯示穩定的季節性模式，請考慮以較長的時間間隔（例如小時）傳送時間序列資料的範例。 以這種方式取樣您的資料，也會明顯改善 API 回應時間。 

## <a name="next-steps"></a>後續步驟

* [什麼是異常偵測器 API？](../overview.md)
* [快速入門：使用異常偵測器，偵測時間序列資料中的異常狀況 REST API](../quickstarts/detect-data-anomalies-csharp.md)
