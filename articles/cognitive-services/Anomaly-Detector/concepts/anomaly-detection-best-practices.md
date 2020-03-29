---
title: 使用異常偵測器 API 的最佳做法
titleSuffix: Azure Cognitive Services
description: 瞭解使用異常檢測器 API 檢測異常時的最佳做法。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 9407f2fc9375765efb6eb9688b3ebfeef24ba90a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67721615"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>使用異常檢測器 API 的最佳做法

異常檢測器 API 是一種無狀態異常檢測服務。 其結果的準確性和性能可能受以下影響：

* 如何準備時間序列資料。
* 使用的異常檢測器 API 參數。
* API 請求中的資料點數。 

使用本文瞭解使用 API 獲取資料最佳結果的最佳做法。 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>何時使用批次（完整）或最新（最後）點異常檢測

異常檢測器 API 的批次處理檢測終結點允許您通過整個時間系列資料檢測異常。 在此檢測模式下，創建單個統計模型並將其應用於資料集中的每個點。 如果時間序列具有以下特徵，我們建議您在一次 API 呼叫中使用批次處理檢測來預覽資料。

* 季節性時間序列，偶爾出現異常。
* 平面趨勢時間序列，偶爾有峰值/下降。 

我們不建議使用批次處理異常檢測進行即時資料監控，或者對沒有上述特徵的時間序列資料使用它。 

* 批次處理檢測只創建並應用一個模型，每個點的檢測在整個系列的上下文中完成。 如果時間序列資料在沒有季節性的情況下上下移動，則模型可能會忽略某些變化點（資料中的下降和峰值）。 同樣，某些更改點比資料集後期的更改點不重要，可能未計為足夠重要，無法合併到模型中。

* 批次處理檢測比執行即時資料監視時檢測最新點的異常狀態要慢，因為要分析的點數。

對於即時資料監控，我們建議僅檢測最新資料點的異常狀態。 通過持續應用最新的點檢測，可以更高效、更準確地進行流資料監控。

下面的示例描述了這些檢測模式對性能的影響。 第一張圖片顯示了沿 28 個以前看到的資料點連續檢測異常狀態最新點的結果。 紅色點是異常點。

![使用最新點顯示異常檢測的圖像](../media/last.png)

下面是使用批次處理異常檢測的相同資料集。 為操作生成的模型忽略了幾個異常，這些異常以矩形為標記。

![使用批次處理方法顯示異常檢測的圖像](../media/entire.png)

## <a name="data-preparation"></a>資料準備

異常檢測器 API 接受格式化為 JSON 請求物件的時間序列資料。 時間序列可以是隨時間順序記錄的任何數值資料。 您可以將時間序列資料的視窗發送到異常檢測器 API 終結點，以提高 API 的性能。 可以發送的資料點最小數為 12，最大值為 8640 點。 [細微性](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview)定義為對資料進行採樣的速率。 

發送到異常檢測器 API 的資料點必須具有有效的協調通用時間 （UTC） 時間戳記和數值。 

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

如果資料是在非標準時間間隔內採樣的，則可以通過在請求中添加`customInterval`屬性來指定資料。 例如，如果每 5 分鐘對系列進行採樣，則可以將以下內容添加到 JSON 請求中：

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>缺少資料點

缺少資料點在均勻分佈的時間序列資料集中很常見，尤其是細微性細（取樣間隔小）的資料集。 例如，資料每隔幾分鐘採樣一次。 缺少資料中預期點數的 10% 不應對檢測結果產生負面影響。 請考慮根據資料的特徵來填補資料的空白，例如替換早期的資料點、線性插值或移動平均線。

### <a name="aggregate-distributed-data"></a>聚合分散式資料

異常檢測器 API 在均勻分佈的時間序列中效果最佳。 如果資料是隨機分佈的，則應按時間單位（例如每分鐘、每小時或每天）聚合資料。

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>季節性模式資料的異常檢測

如果您知道時間序列資料具有季節性模式（定期發生），則可以提高準確性和 API 回應時間。 

在構造`period`JSON 請求時指定 A 可將異常檢測延遲降低多達 50%。 是`period`一個整數，它大致指定時間序列重複模式所需的資料點數。 例如，每天具有一個資料點的時間序列將`period`具有 as `7`，每小時一個點的時間序列（具有相同每週模式）將具有 。 `period` `7*24` 如果您不確定資料的模式，則不必指定此參數。

為獲得最佳結果，請提供`period`4 個值的資料點，外加一個資料點。 例如，具有上述每週模式的小時資料應在請求正文 （）`7 * 24 * 4 + 1`中提供 673 個資料點。

### <a name="sampling-data-for-real-time-monitoring"></a>採樣資料，用於即時監控

如果以短時間間隔（例如秒或分鐘）對流資料進行採樣，則發送建議的資料點數可能會超過異常檢測器 API 允許的最大數量（8640 個資料點）。 如果資料顯示了穩定的季節性模式，請考慮以較大的時間間隔（如小時）發送時間序列資料樣本。 以這種方式對資料進行採樣也可以顯著提高 API 回應時間。 

## <a name="next-steps"></a>後續步驟

* [什麼是異常檢測器 API？](../overview.md)
* [快速入門：使用異常檢測器 REST API 檢測時間序列資料中的異常情況](../quickstarts/detect-data-anomalies-csharp.md)
