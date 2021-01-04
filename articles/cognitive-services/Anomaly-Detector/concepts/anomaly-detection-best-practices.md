---
title: 使用異常偵測器 API 的最佳做法
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用異常偵測器 API 偵測異常狀況時的最佳做法。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: mbullwin
ms.openlocfilehash: 9457c610b256dd4602ef0dc51a47eeffb3c63b49
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705144"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>使用異常偵測器 API 的最佳作法

異常偵測器 API 是無狀態的異常偵測服務。 其結果的精確度和效能可能會受到下列影響：

* 如何準備您的時間序列資料。
* 使用的異常偵測器 API 參數。
* API 要求中的資料點數目。 

您可以使用本文來瞭解使用 API 取得最佳資料結果的最佳作法。 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>使用 batch (整個) 或最新 (最後) 點異常偵測的時機

異常偵測器 API 的批次偵測端點可讓您透過整個時間序列資料來偵測異常狀況。 在此偵測模式中，會建立單一統計模型並套用至資料集內的每個點。 如果您的時間序列有下列特性，建議您在一個 API 呼叫中使用批次偵測來預覽您的資料。

* 季節性時間序列，偶爾發生異常狀況。
* 具有偶發尖峰/下降的平面趨勢時間序列。 

我們不建議使用批次異常偵測來進行即時資料監視，或在沒有上述特性的時間序列資料上使用它。 

* 批次偵測只會建立並套用一個模型，每個點的偵測都是在整個數列的內容中完成。 如果時間序列資料在沒有季節性的情況下向上和向下趨勢，則模型可能會遺漏資料) 中的某些變更 (dip 和尖峰。 同樣地，某些較不重要的變更點可能不會被視為足以併入模型的重要部分。

* 進行即時資料監視時，批次偵測的速度會比偵測最新點的異常狀態慢，因為正在分析的點數目。

若要進行即時資料監視，建議您只偵測最新資料點的異常狀態。 藉由持續套用最新的點偵測，可以更有效率且更精確地進行串流資料監視。

下列範例說明這些偵測模式對效能所造成的影響。 第一張圖片顯示持續偵測異常狀態最近點的結果，以及28個先前看到的資料點。 紅點是異常。

![使用最新點顯示異常偵測的影像](../media/last.png)

以下是使用批次異常偵測的相同資料集。 針對作業所建立的模型已略過數個異常，並以矩形標記。

![使用 batch 方法顯示異常偵測的影像](../media/entire.png)

## <a name="data-preparation"></a>資料準備

異常偵測器 API 接受格式化為 JSON 要求物件的時間序列資料。 時間序列可以是一段時間內依順序記錄的任何數值資料。 您可以將時間序列資料的 windows 傳送至異常偵測器 API 端點，以改善 API 的效能。 您可以傳送的資料點數目下限為12，最大值為8640點。 資料[細微性](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview)會定義為取樣資料的速率。 

傳送至異常偵測器 API 的資料點必須具有有效的國際標準時間 (UTC) 時間戳記和數值。 

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

如果您的資料是在非標準時間間隔進行取樣，您可以 `customInterval` 在要求中加入屬性來指定它。 例如，如果您的數列每隔5分鐘取樣一次，您可以將下列內容新增至您的 JSON 要求：

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>遺漏資料點

遺漏的資料點常見於平均分佈的時間序列資料集，特別是在小型取樣間隔 (細微的資料點。 例如，每隔幾分鐘就會) 取樣資料。 在您的資料中缺少小於10% 的預期點數，不應該對偵測結果產生負面影響。 請考慮根據其特性（例如，將資料點替換成較早的期間、線性插補或移動平均）來填滿資料的間距。

### <a name="aggregate-distributed-data"></a>匯總分散式資料

異常偵測器 API 最適合用於平均分佈的時間序列。 如果您的資料是隨機散發的，您應該依時間單位（例如，每分鐘、每小時或每日）進行匯總。

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>使用季節性模式對資料進行異常偵測

如果您知道您的時間序列資料有季節性模式 (定期出現) 的間隔，您可以改善精確度和 API 回應時間。 

`period`當您在建立 JSON 要求時指定，可以將異常偵測延遲降至50%。 `period`是一個整數，可指定時間序列重複模式所需的資料點數目。 例如，每一天有一個資料點的時間序列會有 `period` as `7` ，且每小時有一個時間序列 (具有相同的每週模式，) 會有 `period` 的  `7*24` 。 如果您不確定資料的模式，則不需要指定此參數。

為了獲得最佳結果，請提供4個 `period` 資料點的數量，再加上一個額外的資料點。 例如，如上面所述，每週的資料模式都應該在要求主體中提供673資料點 (`7 * 24 * 4 + 1`) 。

### <a name="sampling-data-for-real-time-monitoring"></a>即時監視的取樣資料

如果您的串流資料是以短時間間隔取樣 (例如秒或分鐘) ，則傳送建議的資料點數目可能會超過異常偵測器 API 的最大允許 (8640 資料點) 的數目。 如果您的資料顯示穩定的季節性模式，請考慮以較大的時間間隔（例如時數）傳送時間序列資料的範例。 以這種方式取樣您的資料，也會明顯地改善 API 回應時間。 

## <a name="next-steps"></a>後續步驟

* [什麼是異常偵測器 API？](../overview.md)
* [快速入門：使用異常偵測器偵測時間序列資料中的異常狀況](../quickstarts/client-libraries.md)